---
# Horas asignadas: 3 hrs
# Tipo: Laboratorio (versión alumno)
---

# Lab: Filtrado Avanzado con iptables en Ubuntu (Parte 2)

> **Duración:** 3 horas | **Unidad:** En el Ordenador

!!! warning "Instrucciones generales"
    - Trabajar de forma individual salvo indicación del instructor.
    - Documentar cada paso con capturas de pantalla en su cuaderno.
    - Si un paso falla, **no avanzar** — notificar al instructor.

En la Parte 1 aprendimos las cadenas INPUT/OUTPUT/FORWARD, el reset seguro, la política
por defecto DROP, las reglas de loopback y conntrack, y el ciclo capturar→trabajar→restaurar.
Esta parte construye un ruleset completo siguiendo un escenario real de servidor de unidad,
agrega logging limitado de paquetes bloqueados y configura **persistencia real**: reglas
que sobreviven a un reinicio de la VM.

## Objetivos

Al finalizar este laboratorio, el alumno habrá:

- Derivado la subred del laboratorio desde la tabla de rutas (no un valor inventado)
- Construido un ruleset completo de iptables siguiendo un escenario de servidor
- Usado logging con límite de tasa para registrar paquetes bloqueados sin inundar el kernel
- Generado tráfico bloqueado real desde Windows y verificado el log y los contadores
- Distinguido el backup manual (`iptables-save`/`restore`) de la persistencia real
- Configurado `netfilter-persistent` y verificado que el ruleset sobrevive a un reinicio
- (Opcional) Entendido qué hace falta — completo — para que un equipo Linux actúe como router NAT

## Prerrequisitos

| Requisito | Verificación |
|-----------|-------------|
| Parte 1 completada | Existe `~/ruleset-inicial.txt`; snapshot `base-limpia` tomado |
| VM Ubuntu 24.04 con NAT + Host-only | `ip -4 addr` muestra ambas interfaces |
| Paquetes de la preparación previa | `systemctl is-enabled netfilter-persistent` responde `enabled` (instalado en P1) |
| Conectividad con el host Windows | `ping <IP-de-la-VM>` desde PowerShell responde |

## Escenario de la Parte 2

```
Escenario: Tu VM Ubuntu actúa como un pequeño servidor de unidad.

Requisitos del oficial de seguridad:
  - Solo permitir SSH (puerto 22) desde la subred del laboratorio
  - Permitir HTTP (puerto 80) desde cualquier IP
  - Permitir HTTPS (puerto 443) desde cualquier IP
  - Registrar (log) los paquetes bloqueados, con límite de tasa
  - Las reglas deben sobrevivir a un reinicio de la VM
  - Bloquear explícitamente todo lo demás
```

## Pasos del Laboratorio

### Parte 1: Derivar la subred y preparar el entorno

**Paso 1 — Derivar la subred del laboratorio (no inventarla)**

La restricción de SSH necesita la subred real de la red Host-only. Mirar la IP de la
interfaz no basta: hace falta la **red y su prefijo**, y eso lo dice la tabla de rutas:

**[VM Ubuntu — Bash]**

```bash
# Variables de la Parte 1 (sustituir enp0s8 por la interfaz Host-only real)
LAB_IF=enp0s8
LAB_IP=$(ip -4 addr show "$LAB_IF" | awk '/inet /{print $2}' | cut -d/ -f1)

# La ruta conectada de esa interfaz entrega la subred en formato CIDR
LAB_NET=$(ip -4 route show dev "$LAB_IF" proto kernel | awk '{print $1; exit}')
echo "Interfaz: $LAB_IF — IP: $LAB_IP — Subred del lab: $LAB_NET"
```

!!! note "Por qué derivar y no asumir"
    Un `/24` escrito de memoria puede no corresponder a la red real (VirtualBox
    permite cambiar el rango Host-only, y otros entornos usan otros prefijos). La
    regla de firewall correcta se construye desde la ruta conectada del sistema —
    el mismo hábito que se usa en un servidor de producción.

!!! question "Verificación"
    `LAB_NET` debe tener formato CIDR (por ejemplo `192.168.56.0/24`) y contener
    tanto la IP de la VM como la IP del host Windows anotada en la Parte 1.

    **Importante:** las variables de shell viven solo en esta terminal. Ejecutar
    los Pasos 1 a 7 en esta misma terminal — si se cierra o se abre otra,
    re-derivar las variables antes de continuar (`echo $LAB_NET` para comprobar).

**Paso 2 — Capturar estado y reset seguro (patrón de la Parte 1)**

**[VM Ubuntu — Bash]**

```bash
# Capturar el estado actual antes de trabajar
sudo iptables-save > ~/ruleset-inicial-p2.txt
cat ~/ruleset-inicial-p2.txt    # vacío = la VM no tenía reglas cargadas (válido; ver nota en Parte 1)

# Reset seguro: políticas ACCEPT PRIMERO, flush después
sudo iptables -P INPUT ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -F
sudo iptables -X
sudo iptables -Z
# Confirmar estado limpio
sudo iptables -L -v -n
```

### Parte 2: Construir el ruleset del escenario

Las reglas llevan comentarios con `-m comment` — así el propósito de cada regla queda
grabado **dentro del ruleset** y sobrevive a `iptables-save`/`restore`.

**Paso 3 — Reglas base obligatorias**

**[VM Ubuntu — Bash]**

```bash
# Regla 1: Permitir loopback (siempre primero)
sudo iptables -A INPUT -i lo -m comment --comment "loopback siempre permitido" -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
# Regla 2: Permitir conexiones establecidas (stateful)
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED \
  -m comment --comment "conexiones ya aprobadas (stateful)" -j ACCEPT
```

**Paso 4 — SSH restringido a la subred derivada**

**[VM Ubuntu — Bash]**

```bash
# Regla 3: SSH solo desde la subred del laboratorio (usa la variable derivada)
sudo iptables -A INPUT -p tcp --dport 22 -s "$LAB_NET" \
  -m comment --comment "SSH solo desde subred del lab" -j ACCEPT
```

!!! question "Reflexión"
    ¿Qué ocurre si alguien desde una IP fuera de `$LAB_NET` intenta conectar por SSH?
    La regla no coincide y el paquete cae a la política DROP.
    Eso es exactamente el requisito del escenario.

**Paso 5 — HTTP y HTTPS sin restricción de origen**

**[VM Ubuntu — Bash]**

```bash
# Regla 4: HTTP desde cualquier IP
sudo iptables -A INPUT -p tcp --dport 80 \
  -m comment --comment "HTTP abierto" -j ACCEPT
# Regla 5: HTTPS desde cualquier IP
sudo iptables -A INPUT -p tcp --dport 443 \
  -m comment --comment "HTTPS abierto" -j ACCEPT
```

**Paso 6 — Logging con límite de tasa, antes del DROP**

**[VM Ubuntu — Bash]**

```bash
# Regla 6: Registrar paquetes que serán bloqueados — CON límite de tasa
sudo iptables -A INPUT -m limit --limit 5/min --limit-burst 10 \
  -m comment --comment "log de bloqueados (limitado)" \
  -j LOG --log-prefix "IPTABLES-DROP: " --log-level 4
```

!!! warning "Nunca LOG sin límite"
    Una regla LOG sin `-m limit` registra **cada** paquete no permitido. Un escaneo
    de puertos o una ráfaga de tráfico puede inundar el log del kernel en segundos.
    `--limit 5/min` con `--limit-burst 10` deja evidencia suficiente para el análisis
    sin degradar el sistema.

!!! note "Cómo funciona el LOG"
    La regla de LOG **no bloquea** el tráfico — registra y deja pasar el paquete a lo
    que sigue (aquí, la política DROP). LOG y DROP son dos eventos separados en la
    vida del paquete. Para ver los registros:

    ```bash
    sudo dmesg | grep "IPTABLES-DROP"
    # Alternativa:
    sudo journalctl -k | grep "IPTABLES-DROP"
    ```

**Paso 7 — Aplicar política DROP y verificar el ruleset**

**[VM Ubuntu — Bash]**

```bash
# Política final: bloquear todo lo que no coincidió con ninguna regla anterior
sudo iptables -P INPUT DROP
# Ver todas las reglas con números de línea
sudo iptables -L -v -n --line-numbers
```

El orden correcto debe ser:

1. loopback → ACCEPT
2. conntrack ESTABLISHED,RELATED → ACCEPT
3. tcp:22 desde `$LAB_NET` → ACCEPT
4. tcp:80 → ACCEPT
5. tcp:443 → ACCEPT
6. LOG (con limit)
7. Política INPUT: DROP

### Parte 3: Probar el ruleset con tráfico real

**Paso 8 — Probar los servicios permitidos**

**[VM Ubuntu — Bash]**

```bash
# Levantar el servidor HTTP de prueba (dejar corriendo en esta terminal)
sudo python3 -m http.server 80
```

En una **segunda terminal** de la VM (la regla 443 también merece su prueba real):

```bash
# Listener para la regla HTTPS — sirve HTTP plano, pero la prueba es de conexión TCP
sudo python3 -m http.server 443
```

En la terminal de trabajo, confirmar ambos listeners (patrón de la Parte 1):

```bash
sudo ss -lntp | grep -E ':80 |:443 '
```

**[Windows — PowerShell]**

```powershell
# SSH permitido: el host Windows está dentro de la subred del lab
Test-NetConnection <IP-de-la-VM> -Port 22
# HTTP permitido desde cualquier origen
curl.exe -4 -I http://<IP-de-la-VM>/
# HTTPS permitido desde cualquier origen (prueba de conexión TCP a la regla 5)
Test-NetConnection <IP-de-la-VM> -Port 443
```

!!! question "Verificación"
    Las tres pruebas exitosas. En la VM, `sudo iptables -L INPUT -v -n` muestra
    contadores `pkts > 0` en las reglas tcp:22, tcp:80 y tcp:443. Recordar la
    lección de la Parte 1: una regla ACCEPT sin servicio detrás daría fallo
    instantáneo (refused) aunque el firewall la permita — por eso cada prueba
    tiene su listener.

**Paso 9 — Generar tráfico bloqueado y verificarlo dos veces**

Probar un puerto **sin** regla ACCEPT y confirmar el bloqueo con dos evidencias
independientes: el log del kernel y el contador de la regla LOG.

**[Windows — PowerShell]**

```powershell
# Puerto 8080: no hay regla que lo permita → debe fallar
Test-NetConnection <IP-de-la-VM> -Port 8080
```

**[VM Ubuntu — Bash]**

```bash
# Evidencia 1: la entrada en el log del kernel
sudo dmesg | grep "IPTABLES-DROP" | tail -5
# Evidencia 2: el contador de la regla LOG incrementó
sudo iptables -L INPUT -v -n --line-numbers
```

!!! question "Verificación"
    - `TcpTestSucceeded : False` en PowerShell, y **lento** (~20 s): la firma del
      DROP silencioso vista en la Parte 1 — el emisor agota su timeout sin recibir
      respuesta alguna.
    - El log muestra una línea `IPTABLES-DROP:` con `SRC=<IP-Windows>`,
      `DST=<IP-VM>`, `PROTO=TCP` y `DPT=8080`.
    - La regla LOG muestra `pkts > 0`.
    Anotar en el cuaderno qué significa cada campo del log (IN=, SRC=, DST=, DPT=).

### Parte 4: Backup manual vs. persistencia real

**Paso 10 — Backup y restauración manual (iptables-save / iptables-restore)**

Este mecanismo **no** es persistencia: es una copia bajo demanda que alguien tiene
que restaurar a mano.

**[VM Ubuntu — Bash]**

```bash
# Exportar el ruleset del escenario a un archivo
sudo iptables-save > ~/mis-reglas-iptables.txt
# Ver el archivo: las reglas incluyen los comentarios -m comment
cat ~/mis-reglas-iptables.txt

# Simular pérdida de reglas (reset seguro: políticas primero)
sudo iptables -P INPUT ACCEPT
sudo iptables -F
sudo iptables -L -n          # sin reglas

# Restaurar desde el archivo
sudo iptables-restore < ~/mis-reglas-iptables.txt
sudo iptables -L -v -n --line-numbers   # el ruleset volvió, con política DROP
```

!!! question "Verificación"
    ¿El ruleset después de `iptables-restore` es idéntico al del Paso 7?
    Comparar número de reglas, orden y comentarios — deben ser exactamente iguales.

**Paso 11 — Persistencia real: sobrevivir a un reinicio**

**[VM Ubuntu — Bash]**

```bash
# Guardar el ruleset actual como el ruleset de arranque
sudo netfilter-persistent save
# Ver dónde quedó guardado (el archivo es de root y solo root puede leerlo)
sudo cat /etc/iptables/rules.v4

# Reiniciar la VM de verdad
sudo systemctl reboot
```

Tras el reinicio, abrir una terminal y verificar:

```bash
# ¿Sobrevivieron las reglas al reinicio?
sudo iptables -L -v -n --line-numbers
```

!!! question "Verificación"
    El ruleset completo del escenario (política DROP incluida) está activo **sin
    haber ejecutado ningún comando de restauración**. Eso es persistencia: el
    servicio `netfilter-persistent` restauró `/etc/iptables/rules.v4` durante el
    arranque. Comparar con el Paso 10: allí la restauración fue manual.

!!! note "Backup ≠ persistencia"
    `iptables-save > archivo` produce un **backup manual**. La persistencia real
    requiere un servicio que restaure las reglas en cada arranque
    (`netfilter-persistent` en Ubuntu). Un servidor de producción sin esto pierde
    su firewall en el primer reinicio.

### Parte 5 (Opcional): Qué hace falta para un router NAT

!!! info "Extensión opcional"
    Solo para alumnos que terminaron antes del tiempo. Conceptual — no ejecutar.

Una regla MASQUERADE **por sí sola no convierte un equipo en router**. Hacen falta
tres piezas, y la VM de este lab —con sus dos interfaces— es exactamente la topología
donde aplicarían:

```bash
# Pieza 1: habilitar el reenvío de paquetes en el kernel (apagado por defecto)
#   sudo sysctl -w net.ipv4.ip_forward=1

# Pieza 2: permitir el tránsito en la cadena FORWARD
#   sudo iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT
#   sudo iptables -A FORWARD -i enp0s3 -o enp0s8 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Pieza 3: traducir las direcciones de salida (NAT) en la interfaz externa
#   sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
```

El NAT (Network Address Translation) es lo que hace un router doméstico: múltiples
dispositivos con IPs privadas comparten una IP pública. Pero el router lo logra con
las tres piezas juntas: reenvío del kernel, reglas FORWARD entre dos interfaces, y
MASQUERADE en la tabla `nat`. Faltando cualquiera, no hay router.

### Parte 6: Limpieza final

**Paso 12 — Restaurar el estado original (OBLIGATORIO)**

**[VM Ubuntu — Bash]**

```bash
# Cerrar los servidores de Python (80 y 443) si siguen corriendo (Ctrl+C en sus terminales)

# Restaurar el ruleset capturado al inicio de este lab
# (si ~/ruleset-inicial-p2.txt quedó vacío, usar en su lugar el reset seguro
#  del Paso 2: políticas ACCEPT primero, después -F -X -Z)
sudo iptables-restore < ~/ruleset-inicial-p2.txt
# Volver a persistir el estado LIMPIO, para que el lab no quede activo en el arranque
sudo netfilter-persistent save
# Verificar
sudo iptables -L -v -n
```

!!! warning "No omitir el segundo save"
    En el Paso 11 persistimos el ruleset del escenario. Si se restaura el estado
    limpio pero no se vuelve a ejecutar `netfilter-persistent save`, el próximo
    reinicio reactivará las reglas del laboratorio. Opción nuclear si algo quedó
    inconsistente: restaurar el snapshot `base-limpia` de VirtualBox.

## Validación final

Al terminar todas las partes, verificar:

- [ ] `LAB_NET` se derivó de `ip -4 route` y aparece en la regla SSH del ruleset
- [ ] El ruleset del escenario tiene 6 reglas de INPUT + política DROP, en el orden correcto
- [ ] Las tres reglas de servicio (22, 80, 443) probadas con un servicio real escuchando
- [ ] La regla LOG incluye `limit: avg 5/min burst 10` en la salida de `iptables -L -v -n`
- [ ] La sonda al puerto 8080 desde Windows quedó registrada en el log **y** en el contador
- [ ] `iptables-restore` reprodujo el ruleset exacto tras el flush simulado (Paso 10)
- [ ] Tras `systemctl reboot`, el ruleset del escenario estaba activo sin intervención (Paso 11)
- [ ] El estado final quedó limpio y persistido limpio (Paso 12)

## Complemento OFFen

!!! info "Ejercicio complementario (opcional)"
    Este laboratorio se complementa con el escenario de bypass de firewall disponible
    en la plataforma OFFen. El escenario simula un atacante que intenta encontrar puertos
    abiertos en un servidor con un ruleset similar al construido en este lab. Observar
    desde la perspectiva ofensiva por qué las restricciones de subred para SSH y el
    logging son efectivas como controles defensivos.

    Acceder desde: `http://[ip-offen-server]` con las credenciales del curso.

## Entrega

El alumno debe entregar:

1. El contenido de `~/mis-reglas-iptables.txt` (con los comentarios `-m comment`
   visibles en las reglas)
2. Captura de `sudo iptables -L -v -n --line-numbers` mostrando el ruleset completo
   del escenario, incluida la subred derivada en la regla SSH
3. Captura del Paso 9: la línea del log `IPTABLES-DROP:` correspondiente a la sonda
   al puerto 8080 y el contador de la regla LOG con `pkts > 0`
4. Captura del Paso 11: el ruleset activo inmediatamente después del reinicio
   (evidencia de persistencia real)

---

<!-- Solución disponible para el instructor en: docs/instructor/lab-iptables-p2-solucion.md -->
