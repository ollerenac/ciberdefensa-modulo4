---
# Horas asignadas: 3 hrs
# Tipo: Teoría + Laboratorio (versión alumno)
---

# IDS con Snort en Windows: Configuración y Práctica (Parte 2)

> **Duración:** 3 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red | **Continuación de Parte 1 — incluye laboratorio integrado**

## Recap de la Parte 1

En la Parte 1 aprendimos la diferencia fundamental entre IDS (detecta y alerta) e IPS (detecta y bloquea), distinguimos NIDS de HIDS, y explicamos los dos métodos de detección: por firma (preciso pero ciego ante zero-days) y por anomalía (detecta lo desconocido pero con más falsos positivos). También introdujimos los conceptos de true positive, false positive, true negative y false negative. En esta sesión instalamos Snort 2.9.x en Windows 11, escribimos reglas propias y ejecutamos el IDS en modo activo para generar y leer alertas reales.

---

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Instalar Snort 2.9.x y Npcap en Windows 11 y verificar la configuración
- Leer y escribir reglas Snort básicas explicando cada campo de la anatomía de la regla
- Ejecutar Snort en modo IDS con alertas en consola
- Generar tráfico de prueba que dispare las reglas escritas y leer las alertas resultantes
- Aplicar la opción `threshold` para controlar el volumen de alertas en eventos frecuentes

---

## Anatomía de una Regla Snort

Antes de instalar nada, es imprescindible entender la estructura de una regla Snort. Esta es la regla más importante del módulo:

```snort
alert tcp any any -> $HOME_NET 22 (msg:"Intento de conexion SSH"; sid:1000001; rev:1;)
```

| Campo | Valor en el ejemplo | Significado |
|-------|--------------------|----|
| Acción | `alert` | Qué hace Snort cuando hay coincidencia: `alert` genera alerta, `drop` descarta (modo IPS) |
| Protocolo | `tcp` | Protocolo a inspeccionar: `tcp`, `udp`, `icmp`, `ip` |
| IP origen | `any` | IP o rango de origen de los paquetes (`any` = cualquier IP) |
| Puerto origen | `any` | Puerto de origen (`any` = cualquier puerto) |
| Dirección | `->` | Dirección del flujo: `->` unidireccional, `<>` bidireccional |
| IP destino | `$HOME_NET` | IP o rango de destino (variable definida en `snort.conf`) |
| Puerto destino | `22` | Puerto destino a vigilar (22 = SSH) |
| `msg:` | `"Intento de conexion SSH"` | Texto descriptivo que aparece en la alerta |
| `sid:` | `1000001` | ID único de la regla (>1000000 para reglas propias) |
| `rev:` | `1` | Versión de la regla (incrementar al modificarla) |

!!! note "¿Qué es $HOME_NET?"
    `$HOME_NET` es una variable definida en `snort.conf` que representa la red que queremos proteger. Por ejemplo, si nuestra red interna es `192.168.1.0/24`, definimos `var HOME_NET 192.168.1.0/24`. Usar la variable en las reglas hace que sean portables: si cambia la red, solo hay que cambiar la definición en `snort.conf`, no todas las reglas.

---

## Instalación: Snort + Npcap en Windows 11

Snort necesita Npcap para capturar paquetes de la interfaz de red en Windows. **Npcap debe instalarse antes que Snort.**

### Paso 1 — Instalar Npcap

Descargar Npcap desde `https://npcap.com/#download`. Ejecutar el instalador como Administrador.

!!! warning "Opción crítica durante la instalación de Npcap"
    Durante la instalación, marcar la casilla **"Install Npcap in WinPcap API-compatible Mode"**. Sin esta opción marcada, Snort no puede usar Npcap para capturar tráfico. Es el error más frecuente en la instalación.

### Paso 2 — Instalar Snort 2.9.x

Descargar el instalador de Snort 2.9.x para Windows desde `https://www.snort.org/downloads`. Instalar en la ruta predeterminada: `C:\Snort\`.

### Paso 3 — Descargar las reglas de la comunidad

Desde `https://www.snort.org/downloads`, descargar el paquete "Snort Community Rules" (no requiere cuenta). Extraer el contenido en `C:\Snort\rules\`.

### Paso 4 — Configurar snort.conf

El archivo de configuración principal está en `C:\Snort\etc\snort.conf`. Editar con Notepad como Administrador y establecer:

```snort
# Definir la red a proteger (ajustar a la red del laboratorio)
var HOME_NET 192.168.1.0/24

# Ruta a la carpeta de reglas
var RULE_PATH C:\Snort\rules

# Carpeta para logs de alertas
var LOG_DIR C:\Snort\log

# Incluir reglas de la comunidad
include $RULE_PATH\community.rules
```

### Paso 5 — Verificar la configuración

```
C:\Snort\bin\snort.exe -T -c C:\Snort\etc\snort.conf
```

La opción `-T` ejecuta Snort en modo test: verifica la configuración sin capturar tráfico ni generar alertas. Al final debe aparecer: `Snort successfully validated the configuration!`. Si aparece un error, indica la línea problemática en `snort.conf`.

---

## Escribir Reglas Propias

Las reglas propias deben guardarse en un archivo separado: `C:\Snort\rules\mis-reglas.rules`. Agregar al final de `snort.conf` la línea: `include $RULE_PATH\mis-reglas.rules`.

### Regla 1 — Detectar ping ICMP

```snort
alert icmp any any -> $HOME_NET any (msg:"Ping ICMP detectado"; itype:8; sid:1000010; rev:1;)
```

- `icmp`: protocolo ICMP
- `itype:8`: tipo ICMP 8 = Echo Request (el paquete que envía el comando `ping`)
- Esta regla genera una alerta por cada echo request que llegue a nuestra red

### Regla 2 — Detectar intento de conexión Telnet

```snort
alert tcp any any -> $HOME_NET 23 (msg:"Intento de conexion Telnet inseguro"; sid:1000011; rev:1;)
```

- Puerto 23 es el puerto estándar de Telnet
- La regla alerta ante cualquier intento de establecer conexión TCP al puerto 23 de la red protegida

### Regla 3 — Detectar cadena sospechosa en tráfico HTTP

```snort
alert tcp any any -> $HOME_NET 80 (msg:"HTTP con cadena sospechosa cmd.exe"; content:"cmd.exe"; nocase; sid:1000012; rev:1;)
```

- `content:"cmd.exe"`: busca la cadena literal `cmd.exe` en el payload del paquete
- `nocase`: sin distinción de mayúsculas (detecta `CMD.EXE`, `cmd.Exe`, etc.)
- Útil para detectar intentos de ejecución remota de comandos vía web (webshells, exploits HTTP)

!!! tip "Dónde guardar las reglas y cómo incluirlas"
    Guardar las tres reglas en `C:\Snort\rules\mis-reglas.rules`, una por línea.
    En `snort.conf`, agregar al final:
    ```
    include $RULE_PATH\mis-reglas.rules
    ```
    Después verificar de nuevo con `snort -T -c snort.conf` antes de ejecutar en modo IDS.

---

## Ejecutar Snort en Modo IDS

### Listar las interfaces de red disponibles

```
C:\Snort\bin\snort.exe -W
```

Este comando muestra todas las interfaces de red que Npcap puede usar, con su número de índice. Ejemplo de salida:

```
1. \Device\NPF_{GUID1} (Adaptador WiFi Intel)
2. \Device\NPF_{GUID2} (Adaptador Ethernet Realtek)
```

### Ejecutar en modo IDS con alertas en consola

```
C:\Snort\bin\snort.exe -i <numero_interfaz> -A console -c C:\Snort\etc\snort.conf
```

- `-i 1`: usar la interfaz número 1 (ajustar al número de la interfaz activa)
- `-A console`: mostrar las alertas directamente en la consola (útil para el lab; en producción se usaría `-A fast` o `-A full` para escribir a archivo)
- `-c`: ruta al archivo de configuración

!!! tip "¿Cuál es la interfaz activa?"
    En un portátil con WiFi y Ethernet, normalmente solo una está activa. Probar primero con la interfaz WiFi (generalmente la 1). Si Snort no genera alertas al hacer ping, cambiar al otro número de interfaz.

---

## Generando Tráfico de Prueba y Leyendo Alertas

Con Snort corriendo en un CMD, abrir otro CMD para generar el tráfico que dispare las reglas:

**Para la Regla 1 (ping ICMP):**
```
ping 127.0.0.1
```

**Para la Regla 2 (Telnet):**
```
telnet localhost 23
```
El intento de conexión puede no establecerse (si no hay servicio Telnet), pero el paquete SYN enviado hacia el puerto 23 es suficiente para que Snort lo detecte.

**Para la Regla 3 (cadena HTTP):**
```
curl http://example.com/cmd.exe
```

**Formato de la alerta en consola:**

```
[**] [1:1000010:1] Ping ICMP detectado [**]
[Priority: 0]
06/16-14:30:00.123456 127.0.0.1 -> 127.0.0.1
ICMP TTL:128 TOS:0x0 ID:1234 IpLen:20 DgmLen:60
Type:8  Code:0  ID:256   Seq:1  ECHO
```

| Campo en la alerta | Significado |
|--------------------|------------|
| `[1:1000010:1]` | GID:SID:REV — identificador único de la regla |
| `Ping ICMP detectado` | Texto del campo `msg:` de la regla |
| `06/16-14:30:00.123456` | Fecha y hora de detección |
| `127.0.0.1 -> 127.0.0.1` | IP origen → IP destino del paquete detectado |
| `ECHO` | Tipo de paquete ICMP (Echo Request) |

---

## Control de Volumen de Alertas (threshold)

**El problema del alert storm:** La Regla 1 genera una alerta por cada echo request. Con `ping -n 100 127.0.0.1`, eso son 100 alertas en segundos para un solo evento. Un analista no puede revisar 100 alertas idénticas — el ruido enmascara los eventos realmente importantes.

**Solución: opción `threshold`**

```snort
alert icmp any any -> $HOME_NET any (
  msg:"Ping ICMP detectado";
  itype:8;
  threshold: type limit, track by_src, count 1, seconds 60;
  sid:1000010; rev:2;
)
```

Explicación de los parámetros de threshold:

| Parámetro | Valor | Significado |
|-----------|-------|------------|
| `type limit` | `limit` | Limitar el número de alertas generadas |
| `track by_src` | `by_src` | Contar por IP de origen (cada IP tiene su propio contador) |
| `count 1` | `1` | Máximo de alertas a generar dentro del período |
| `seconds 60` | `60` | Ventana de tiempo en segundos |

Con esta configuración, sin importar cuántos pings envíe una IP específica, Snort generará como máximo 1 alerta por minuto para esa IP. Esto reduce el ruido manteniendo la detección.

---

## Validación del laboratorio

- [ ] `snort -T -c C:\Snort\etc\snort.conf` muestra "Snort successfully validated the configuration!"
- [ ] Snort en modo IDS (`-A console`) muestra al menos 1 alerta al ejecutar `ping 127.0.0.1`
- [ ] La alerta de Telnet (Regla 2) aparece en la consola al ejecutar `telnet localhost 23`
- [ ] La regla con `threshold` no genera más de 1 alerta por minuto para el mismo IP de origen, aunque se envíen múltiples pings

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    El analista de seguridad de la unidad recibe una alerta de Snort a las 02:17: `[1:1000012:1] HTTP con cadena sospechosa cmd.exe` originado desde la IP interna 192.168.1.88 hacia un servidor web externo desconocido. Una cadena `cmd.exe` en una petición HTTP saliente es un indicador de que el equipo puede estar comunicándose con un servidor de comando y control (C2) de malware — enviando resultados de comandos ejecutados en el equipo comprometido. El analista aísla inmediatamente 192.168.1.88 de la red y escala el incidente. La regla de Snort detectó en tiempo real algo que ningún firewall de capa 3 habría visto: el contenido del payload del paquete.

---

## Resumen

- Snort 2.9.x requiere Npcap con "WinPcap API-compatible mode" instalado previamente; el comando `snort -T -c snort.conf` valida la configuración antes de ejecutar
- Cada regla Snort tiene: acción, protocolo, IP origen, puerto origen, dirección, IP destino, puerto destino y opciones entre paréntesis (msg, sid, rev, content, threshold...)
- Ejecutar Snort en modo IDS: `snort -i <interfaz> -A console -c snort.conf`; la opción `-W` lista las interfaces disponibles
- El `threshold` es esencial en producción para evitar el alert storm — sin él, un único evento puede generar miles de alertas que enmascaran incidentes reales
- Snort inspecciona el contenido del payload (capa 7), lo que lo hace más poderoso que un firewall de capa 3/4 para detectar ataques que abusan de protocolos permitidos

<!-- Solución disponible para el instructor en: docs/instructor/lab-suricata-solucion.md -->

---

*Siguiente: [Honey Pots (Parte 1)](honeypots-p1.md)*
