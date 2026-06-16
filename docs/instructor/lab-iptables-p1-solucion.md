---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab Filtrado con Iptables (Parte 1)

!!! danger "Solo para instructores"
    Este documento contiene la solución completa con salidas esperadas, errores comunes
    y notas de timing para dictar la clase. No mostrar a los alumnos.
    Está excluido del nav público de MkDocs pero es visible en el repositorio GitHub.

## Solución Completa Paso a Paso

### Paso 1 — Verificar versión de iptables

Salida esperada en la terminal del alumno:

```
$ sudo iptables --version
iptables v1.8.7 (nf_tables)
```

**Por qué funciona:** Ubuntu 22.04 incluye iptables por defecto. La variante `nf_tables`
es el backend moderno (iptables-nft). Si el alumno ve `(legacy)` en lugar de `(nf_tables)`,
el lab funciona igual — es solo una diferencia de backend.

**Qué revisar si falla:** Si el comando no existe, ejecutar `sudo apt-get install iptables -y`.
Si aparece "sudo: command not found", el alumno no está dentro de WSL2 Ubuntu sino en
PowerShell — indicarle que debe abrir la sesión Ubuntu.

### Paso 2 — Estado inicial del firewall

Salida esperada (estado limpio, sin reglas):

```
$ sudo iptables -L -v -n
Chain INPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination

Chain FORWARD (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination
```

**Por qué funciona:** WSL2 arranca con iptables en estado limpio (política ACCEPT, sin reglas).
Esto es el punto de partida conocido que necesitamos.

**Qué revisar si la salida incluye reglas:** El alumno tiene reglas de una sesión anterior.
Continuar al Paso 3 (flush) — eso limpia todo.

### Paso 3 — Flush (limpieza)

Salida esperada después del flush:

```
$ sudo iptables -F && sudo iptables -X && sudo iptables -Z
$ sudo iptables -L -v -n
Chain INPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination

Chain FORWARD (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination
```

**Nota:** Los contadores de pkts/bytes se resetean a 0 con `-Z`. La política sigue siendo
ACCEPT porque `-F` solo elimina reglas, no cambia políticas.

### Paso 4 — Regla de loopback

Después de ejecutar los dos comandos, verificar con `iptables -L -v -n`:

```
Chain INPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0

Chain OUTPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  *      lo      0.0.0.0/0            0.0.0.0/0
```

**Por qué es obligatorio primero:** La interfaz loopback (`lo`) es usada internamente por
el sistema para comunicación entre procesos. Si se aplica DROP sin esta regla, servicios
como DNS local, bases de datos locales y comunicación entre procesos del sistema fallan.

### Paso 5 — Regla ESTABLISHED

Después de agregar la regla de conexiones establecidas:

```
Chain INPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0    state RELATED,ESTABLISHED
```

**Concepto a reforzar en voz alta (pausa pedagógica):** "El kernel de Linux mantiene
una tabla de conexiones activas (conntrack). Cuando tú iniciás una conexión hacia fuera
(por ejemplo, `curl http://example.com`), el kernel registra esa conexión. Cuando llega
la respuesta HTTP del servidor, el kernel la reconoce como parte de esa conexión
ESTABLISHED y la deja pasar — aunque la política general sea DROP. Sin esta regla,
el curl saldría (OUTPUT es ACCEPT) pero la respuesta nunca llegaría."

### Paso 6 — Permitir puertos SSH y HTTP

```
Chain INPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0    state RELATED,ESTABLISHED
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0    tcp dpt:22
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0    tcp dpt:80
```

### Paso 7 — Aplicar política DROP

Después de cambiar la política:

```
Chain INPUT (policy DROP)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0    state RELATED,ESTABLISHED
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0    tcp dpt:22
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0    tcp dpt:80

Chain FORWARD (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  *      lo      0.0.0.0/0            0.0.0.0/0
```

**El cambio clave:** La cadena INPUT ahora muestra `policy DROP` en lugar de `policy ACCEPT`.

### Paso 8 — Leer el ruleset completo

El alumno debe poder identificar en la salida del Paso 7:

- La política DROP de INPUT en el encabezado de la cadena
- 4 reglas en INPUT: loopback, ESTABLISHED, tcp:22, tcp:80
- La columna `in` muestra `lo` para la regla de loopback y `*` (cualquier interfaz) para las demás
- Los contadores `pkts` y `bytes` en 0 (ningún paquete ha pasado por las reglas todavía)

### Paso 9 — Pruebas de conectividad

**Prueba 1: Ping a loopback**

```
$ ping -c 3 127.0.0.1
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.048 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.052 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.044 ms

--- 127.0.0.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss
```

**Prueba 2: HTTP a internet**

```
$ curl -I http://example.com
HTTP/1.1 200 OK
Content-Encoding: gzip
Accept-Ranges: bytes
Age: 559461
Cache-Control: max-age=604800
Content-Type: text/html; charset=UTF-8
Date: Mon, 16 Jun 2026 00:00:00 GMT
Etag: "3147526947"
Expires: Mon, 23 Jun 2026 00:00:00 GMT
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
Server: ECS (nyb/1D04)
X-Cache: HIT
Content-Length: 648
```

La primera línea `HTTP/1.1 200 OK` confirma que el tráfico saliente (curl) funciona
y que la respuesta entrante (ESTABLISHED) llega correctamente.

**Prueba 3: Ping a IP bloqueada**

```
$ sudo iptables -A INPUT -s 8.8.8.8 -j DROP
$ ping -c 3 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2003ms
```

El 100% de pérdida de paquetes confirma que la regla DROP para 8.8.8.8 funciona.
**Nota:** En WSL2, el ping puede mostrar "Request timeout" o simplemente no mostrar
respuestas — ambos comportamientos son correctos. Lo importante es 0 paquetes recibidos.

## Errores Comunes y Cómo Manejarlos

| Error | Causa probable | Qué decirle al alumno |
|-------|---------------|----------------------|
| `sudo: iptables: command not found` | iptables no instalado en Ubuntu | Ejecutar `sudo apt-get install iptables -y` antes de continuar |
| Después del Paso 7, el curl deja de funcionar | Aplicaron DROP antes de agregar la regla ESTABLISHED (Paso 5) | Limpiar con `iptables -F`, resetear política con `iptables -P INPUT ACCEPT`, comenzar desde Paso 3 |
| ping a 8.8.8.8 sigue funcionando tras agregar la regla DROP | La regla DROP se agregó después de una regla ESTABLISHED que captura el tráfico existente | Verificar con `iptables -L -v -n --line-numbers`. Si ESTABLISHED está antes que DROP:8.8.8.8, el ping activo sigue por conntrack. Esperar a que la conexión expire y reintentar. |
| WSL2 no tiene acceso a internet (curl falla) | Problema de resolución DNS en WSL2 | Verificar `/etc/resolv.conf`. Si está vacío, ejecutar: `echo "nameserver 8.8.8.8" \| sudo tee /etc/resolv.conf` |
| `iptables: Table does not exist` | WSL2 kernel no soporta iptables (kernels muy antiguos) | Verificar `uname -r`. Si es anterior a 5.10, actualizar WSL2 con `wsl --update` desde PowerShell de Windows |

## Notas de Dictado

**Timing sugerido (1 hora total):**

- Prerrequisitos y apertura WSL2 (Paso 1-2): ~10 min
- Flush y estado inicial (Paso 3): ~5 min
- Pausa conceptual: dibujar diagrama de cadenas en la pizarra: ~10 min
- Reglas de loopback y ESTABLISHED (Pasos 4-5): ~15 min — PAUSA OBLIGATORIA aquí (ver abajo)
- Puertos específicos y política DROP (Pasos 6-7): ~10 min
- Lectura del ruleset y pruebas (Pasos 8-9): ~10 min

**Pausa conceptual obligatoria antes del Paso 7:**

Antes de que los alumnos ejecuten `-P INPUT DROP`, el instructor debe dibujar en la pizarra:

```
Internet → [INPUT policy=DROP] → Ubuntu
                ↑
         4 reglas ACCEPT:
         1. lo → ACCEPT
         2. ESTABLISHED → ACCEPT
         3. tcp:22 → ACCEPT
         4. tcp:80 → ACCEPT
         (todo lo demás: DROP por política)
```

Si los alumnos cambian la política sin entender el modelo, generarán errores (curl deja
de funcionar) y perderán el hilo del lab. Esta pausa de 5 minutos evita 15 minutos de
recuperación de errores.

**Concepto a reforzar en voz alta después del Paso 5 (conntrack):**

"El kernel recuerda que tú iniciaste la conexión. Cuando llega la respuesta, la reconoce
como parte de esa conexión ESTABLISHED y la deja pasar aunque la política sea DROP.
Esto es el filtrado stateful — el firewall es inteligente sobre el estado de las conexiones."

**Si el grupo va lento:**

Omitir la Prueba 3 del Paso 9 (bloqueo de 8.8.8.8) y mostrar el resultado desde el
proyector del instructor. Las Pruebas 1 y 2 son las esenciales para la validación.

## Material del Instructor

Comandos de demostración para el proyector:

```bash
# Mostrar estado antes y después del cambio de política
sudo iptables -L -n | grep "policy"

# Ver qué reglas están capturando tráfico (después de las pruebas, pkts > 0)
sudo iptables -L -v -n

# Mostrar el ruleset en formato numérico con líneas (útil para explicar orden)
sudo iptables -L -v -n --line-numbers

# Verificar que la regla de loopback tiene tráfico (pkts debe ser > 0 tras las pruebas)
sudo iptables -L INPUT -v -n | grep "lo"

# Restaurar estado limpio para demo en vivo
sudo iptables -F
sudo iptables -P INPUT ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -L -v -n
```
