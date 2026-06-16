---
# Horas asignadas: 3 hrs
# Tipo: Laboratorio (versión alumno)
---

# Lab: Filtrado Avanzado con iptables en WSL2 (Parte 2)

> **Duración:** 3 horas | **Unidad:** En el Ordenador

!!! warning "Instrucciones generales"
    - Trabajar de forma individual salvo indicación del instructor.
    - Documentar cada paso con capturas de pantalla en su cuaderno.
    - Si un paso falla, **no avanzar** — notificar al instructor.

En la Parte 1 aprendimos las cadenas INPUT/OUTPUT/FORWARD, la política por defecto DROP,
y las reglas de loopback y ESTABLISHED. Esta parte construye un ruleset completo siguiendo
un escenario real de servidor de unidad, agrega logging de paquetes bloqueados y practica
la persistencia de reglas con `iptables-save` y `iptables-restore`.

## Objetivos

Al finalizar este laboratorio, el alumno habrá:

- Construido un ruleset completo de iptables siguiendo un escenario de servidor
- Usado logging de iptables para registrar paquetes bloqueados
- Aplicado `iptables-save` para exportar las reglas a un archivo persistente
- Restaurado las reglas desde archivo con `iptables-restore` y verificado su supervivencia
- (Opcional) Explorado el concepto de NAT con MASQUERADE

## Prerrequisitos

| Requisito | Verificación |
|-----------|-------------|
| Parte 1 completada | `sudo iptables -L -n` muestra reglas de la Parte 1 |
| WSL2 Ubuntu activo | `wsl --list --verbose` muestra Ubuntu Running |

## Escenario de la Parte 2

```
Escenario: Tu máquina WSL2 actúa como un pequeño servidor de unidad.

Requisitos del oficial de seguridad:
  - Solo permitir SSH (puerto 22) desde la subred local (172.16.0.0/24)
  - Permitir HTTP (puerto 80) desde cualquier IP
  - Permitir HTTPS (puerto 443) desde cualquier IP
  - Registrar (log) todos los paquetes que sean bloqueados por la política DROP
  - Las reglas deben sobrevivir a un reinicio del servicio
  - Bloquear explícitamente todo lo demás
```

!!! note "Sobre la subred del escenario"
    En WSL2, la subred interna varía entre equipos y reinicios. Usaremos `172.16.0.0/24`
    como ejemplo del escenario — en tu entorno real, verificar la IP de tu interfaz WSL2
    con `ip addr show eth0`. La lógica del lab aplica independientemente del rango exacto.

## Pasos del Laboratorio

### Parte 1: Limpiar y preparar el entorno

**Paso 1 — Resetear a estado limpio**

Antes de construir el nuevo ruleset, eliminar todas las reglas anteriores y restablecer
las políticas a ACCEPT:

```bash
# Eliminar todas las reglas y restablecer políticas a ACCEPT
sudo iptables -F
sudo iptables -X
sudo iptables -Z
sudo iptables -P INPUT ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
# Confirmar estado limpio
sudo iptables -L -v -n
```

!!! question "Verificación"
    La salida debe mostrar las tres cadenas con política ACCEPT y sin reglas.
    Este es el punto de partida del escenario.

### Parte 2: Construir el ruleset del escenario

**Paso 2 — Reglas base obligatorias**

Como en la Parte 1, las primeras reglas son siempre loopback y ESTABLISHED:

```bash
# Regla 1: Permitir loopback (siempre primero)
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
# Regla 2: Permitir conexiones establecidas (stateful)
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

**Paso 3 — SSH restringido por subred**

A diferencia de la Parte 1, el SSH aquí tiene una restricción de origen:

```bash
# Regla 3: Permitir SSH solo desde la subred local del escenario
# Adaptar 172.16.0.0/24 a la subred real de tu WSL2 si es diferente
sudo iptables -A INPUT -p tcp --dport 22 -s 172.16.0.0/24 -j ACCEPT
```

!!! question "Reflexión"
    ¿Qué ocurre si alguien desde `10.0.0.1` intenta conectar por SSH?
    La regla de subred no coincide, el paquete cae a la política DROP.
    Eso es exactamente el requisito del escenario.

**Paso 4 — HTTP y HTTPS sin restricción de origen**

```bash
# Regla 4: HTTP desde cualquier IP
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
# Regla 5: HTTPS desde cualquier IP
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```

**Paso 5 — Logging antes de DROP**

Esta regla registra en el sistema todos los paquetes que serán bloqueados:

```bash
# Regla 6: Registrar paquetes que serán bloqueados
# Esta regla DEBE ir antes de la política DROP
sudo iptables -A INPUT -j LOG --log-prefix "IPTABLES-DROP: " --log-level 4
```

!!! note "Cómo funciona el LOG"
    La regla de LOG **no bloquea** el tráfico — solo registra y deja pasar el paquete
    a la siguiente regla (que será la política DROP). Para ver los logs registrados:

    ```bash
    sudo dmesg | grep "IPTABLES-DROP"
    # Alternativa:
    sudo journalctl -k | grep "IPTABLES-DROP"
    ```

**Paso 6 — Aplicar política DROP**

```bash
# Política final: bloquear todo lo que no coincidió con ninguna regla anterior
sudo iptables -P INPUT DROP
```

**Paso 7 — Ver el ruleset completo**

```bash
# Ver todas las reglas con números de línea (útil para editar o verificar orden)
sudo iptables -L -v -n --line-numbers
```

Anotar el ruleset completo. El orden correcto debe ser:

1. loopback → ACCEPT
2. ESTABLISHED → ACCEPT
3. tcp:22 desde subred → ACCEPT
4. tcp:80 → ACCEPT
5. tcp:443 → ACCEPT
6. LOG (todos los demás)
7. Política INPUT: DROP

### Parte 3: Persistencia con iptables-save

**Paso 8 — Guardar las reglas**

```bash
# Exportar el ruleset actual a un archivo de texto
sudo iptables-save > ~/mis-reglas-iptables.txt
# Verificar que el archivo se creó correctamente
cat ~/mis-reglas-iptables.txt
```

!!! warning "iptables no guarda automáticamente"
    iptables NO guarda las reglas al reiniciar WSL2 o Ubuntu. Si cierras la sesión,
    las reglas desaparecen. `iptables-save` exporta el ruleset actual a un archivo
    de texto que puedes restaurar más tarde con `iptables-restore`.

**Paso 9 — Simular pérdida de reglas y restaurar**

```bash
# Simular reinicio: eliminar todas las reglas
sudo iptables -F
sudo iptables -P INPUT ACCEPT
# Verificar que las reglas desaparecieron
sudo iptables -L -n
# Restaurar desde el archivo guardado
sudo iptables-restore < ~/mis-reglas-iptables.txt
# Verificar que las reglas volvieron correctamente
sudo iptables -L -v -n --line-numbers
```

!!! question "Verificación"
    ¿El ruleset después de `iptables-restore` es idéntico al del Paso 7?
    Comparar el número de reglas y el orden — deben ser exactamente iguales.

### Parte 4: Verificación del logging

**Paso 10 — Generar tráfico bloqueado y ver logs**

```bash
# Desde WSL2, ver si hay entradas de log de paquetes bloqueados:
sudo dmesg | tail -20 | grep "IPTABLES-DROP"
# Alternativa si dmesg no muestra nada:
sudo journalctl -k --since "1 minute ago" | grep "IPTABLES-DROP"
```

!!! note "Logging en WSL2"
    En WSL2, el logging de iptables hacia dmesg puede variar según la versión del kernel
    WSL2. Si no aparece nada en dmesg, es un comportamiento normal — el LOG sí está
    configurado, pero WSL2 puede no routear las entradas del kernel a dmesg en todas
    las configuraciones. El instructor puede demostrar el logging desde una VM Linux
    estándar si WSL2 no muestra los mensajes.

### Parte 5 (Opcional): Concepto de NAT

**Paso 11 — NAT conceptual**

!!! info "Extensión opcional"
    Solo para alumnos que terminaron antes del tiempo. El instructor puede mostrar
    esto en el proyector si el grupo va adelantado.

```bash
# Ver la tabla NAT (vacía por defecto en este lab)
sudo iptables -t nat -L -n

# Concepto: MASQUERADE permite que un equipo actúe como router NAT
# En una configuración real de router, agregaríamos:
#   sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
# Esto haría que el tráfico saliente parezca originarse desde eth0
# NO ejecutar en el lab — solo para comprensión conceptual
```

El NAT (Network Address Translation) es lo que hace tu router doméstico: múltiples
dispositivos con IPs privadas comparten una IP pública. En iptables, la regla
`MASQUERADE` en la tabla `nat` realiza exactamente esa traducción de direcciones.

## Validación final

Al terminar todas las partes, verificar:

- [ ] El ruleset tiene exactamente 6 reglas de INPUT más la política DROP
- [ ] `iptables-restore` restauró el ruleset correctamente tras el flush simulado
- [ ] El archivo `~/mis-reglas-iptables.txt` existe en el home directory de Ubuntu
- [ ] Cada regla en el archivo entregado tiene un comentario explicativo escrito a mano

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

1. El contenido del archivo `~/mis-reglas-iptables.txt` (copiar en documento de texto)
2. Captura de pantalla de `sudo iptables -L -v -n --line-numbers` mostrando el ruleset
   completo del escenario
3. Al menos 1 línea del log de iptables mostrando un DROP, o nota explicando que WSL2
   no mostró el log en su configuración
4. El archivo entregado con cada regla anotada con un comentario de una línea explicando
   qué hace

---

<!-- Solución disponible para el instructor en: docs/instructor/lab-iptables-p2-solucion.md -->
