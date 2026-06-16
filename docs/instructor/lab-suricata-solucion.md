---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab IDS con Snort en Windows (LAB-04)

!!! info "Nota histórica sobre el nombre del archivo"
    El nombre de este archivo es `lab-suricata-solucion.md` por razones históricas del esquema de nombres del programa original. El contenido corresponde al lab de **Snort 2.9.x en Windows 11**, que es la herramienta elegida en FEATURES.md por disponibilidad de instalador documentado para Windows.

!!! danger "Solo para instructores"
    Este documento contiene las configuraciones esperadas, reglas completas, alertas de referencia y criterios de calificación. No mostrarlo en el proyector durante el lab.

---

## Prerrequisito de Instalación — Verificar ANTES del lab

**Orden de instalación obligatorio:** Npcap PRIMERO, Snort DESPUÉS.

1. **Instalar Npcap** desde [npcap.com](https://npcap.com). La opción "WinPcap API-compatible Mode" **DEBE** estar marcada durante la instalación. Sin esta casilla marcada, Snort no detecta las interfaces de red y falla con error fatal al iniciar.

2. **Instalar Snort 2.9.x** desde [snort.org](https://www.snort.org). Verificar que la versión sea 2.9.x (no 3.x — la sintaxis de `snort.conf` cambió completamente entre versiones y los materiales del lab son incompatibles con Snort 3).

3. **Descargar Community Rules** desde snort.org/downloads (no requiere registro). Descomprimir el contenido en `C:\Snort\rules\`. Verificar que existe el archivo `C:\Snort\rules\community.rules`.

4. **Tener los archivos MSI en USB** para distribución offline — la descarga puede tardar en aulas con red lenta.

5. **Verificar identificadores de interfaz antes del lab:**
   ```cmd
   C:\Snort\bin\snort.exe -W
   ```
   Este comando lista las interfaces disponibles con sus números. Anotar el número de la interfaz de loopback (generalmente se llama "Adapter for loopback traffic capture" o similar). Se usa con `-i <número>` al ejecutar Snort.

---

## snort.conf Mínimo Funcional

El `snort.conf` oficial incluido en el instalador tiene aproximadamente 500 líneas. Para el lab, usar esta versión mínima: copiar en un nuevo archivo `C:\Snort\etc\snort-lab.conf`.

```text
# Ruta del directorio de reglas
var RULE_PATH C:\Snort\rules

# Red a proteger (ajustar a la IP del aula)
ipvar HOME_NET 192.168.1.0/24

# Variables de puertos estándar (mínimo requerido por el parser de Snort)
portvar HTTP_PORTS 80
portvar SHELLCODE_PORTS !80

# Incluir las reglas de la comunidad (opcional en el lab — puede comentarse para simplificar)
include $RULE_PATH\community.rules

# Incluir las reglas personalizadas del alumno
include $RULE_PATH\mis-reglas.rules
```

!!! note "Nota para el instructor"
    Ejecutar siempre con el archivo minimizado: `snort.exe -c C:\Snort\etc\snort-lab.conf`. Si el alumno usa el `snort.conf` completo incluido en el instalador, puede tardar varios minutos en compilar las reglas y generar mensajes de advertencia confusos. El archivo mínimo es más rápido y produce salida más clara para aprender.

**Crear el archivo de reglas del alumno** (vacío al inicio del lab):
```cmd
echo. > C:\Snort\rules\mis-reglas.rules
```

**Probar la configuración en modo test (sin capturar tráfico):**
```cmd
C:\Snort\bin\snort.exe -T -c C:\Snort\etc\snort-lab.conf
```
Salida esperada de éxito: `Snort successfully validated the configuration!`

---

## Reglas Snort Esperadas

Las 3 reglas que el alumno debe escribir en `C:\Snort\rules\mis-reglas.rules`. Se incluyen notas sobre opciones que los alumnos suelen olvidar.

**Regla 1 — ICMP ping (versión con threshold para evitar alert storm):**
```text
alert icmp any any -> $HOME_NET any (msg:"Ping ICMP detectado"; itype:8; threshold: type limit, track by_src, count 1, seconds 60; sid:1000010; rev:1;)
```

Versión sin threshold (el alumno puede empezar así y agregar el threshold como mejora):
```text
alert icmp any any -> $HOME_NET any (msg:"Ping ICMP detectado"; itype:8; sid:1000010; rev:1;)
```

!!! note "Sobre la opción itype:8"
    `itype:8` limita la alerta a paquetes ICMP de tipo 8 (Echo Request, es decir el "ping" propiamente). Sin esta opción, la regla genera alertas también para las respuestas ICMP tipo 0 (Echo Reply) — duplicando las alertas.

**Regla 2 — Telnet:**
```text
alert tcp any any -> $HOME_NET 23 (msg:"Intento de conexion Telnet inseguro"; sid:1000011; rev:1;)
```

**Regla 3 — HTTP con cadena sospechosa:**
```text
alert tcp any any -> $HOME_NET 80 (msg:"HTTP con cadena sospechosa - cmd.exe"; content:"cmd.exe"; nocase; http_uri; sid:1000012; rev:1;)
```

!!! note "Sobre la opción http_uri"
    La opción `http_uri` mejora la detección al limitar la búsqueda de la cadena al URI de la petición HTTP. Sin ella, Snort busca "cmd.exe" en todo el payload del paquete TCP — puede funcionar pero genera más falsos positivos (por ejemplo, en respuestas de páginas que mencionan "cmd.exe" en su contenido HTML).

---

## Alertas Esperadas en la Consola

Formato exacto de cada alerta cuando Snort corre en modo consola (`-A console`). El instructor puede mostrar esto como referencia antes de que el alumno genere el tráfico.

**Ejecutar Snort en modo IDS sobre la interfaz loopback (usar el número correcto de la interfaz):**
```cmd
C:\Snort\bin\snort.exe -i 1 -A console -c C:\Snort\etc\snort-lab.conf
```

**Alerta de ping (Regla 1):**
```text
05/15-14:30:00.123456  [**] [1:1000010:1] "Ping ICMP detectado" [**] [Classification: (null)] [Priority: 0] {ICMP} 127.0.0.1 -> 127.0.0.1
```

**Alerta de Telnet (Regla 2):**
```text
05/15-14:31:00.456789  [**] [1:1000011:1] "Intento de conexion Telnet inseguro" [**] [Classification: (null)] [Priority: 0] {TCP} 127.0.0.1:54321 -> 127.0.0.1:23
```

**Alerta de HTTP con cmd.exe (Regla 3):**
```text
05/15-14:32:00.789012  [**] [1:1000012:1] "HTTP con cadena sospechosa - cmd.exe" [**] [Classification: (null)] [Priority: 0] {TCP} 127.0.0.1:54322 -> 93.184.216.34:80
```

**Explicación de cada campo (mostrar en proyector al revisar la primera alerta):**

| Campo | Ejemplo | Significado |
|-------|---------|-------------|
| Timestamp | `05/15-14:30:00.123456` | Fecha/hora del paquete (mes/día-hora:min:seg.microseg) |
| Separador | `[**]` | Marca de inicio/fin de alerta en Snort |
| ID de regla | `[1:1000010:1]` | `[generator_id:sig_id:revision]` |
| Mensaje | `"Ping ICMP detectado"` | Campo `msg` de la regla |
| Clasificación | `[Classification: (null)]` | Clasificación de la regla (null si no se especificó) |
| Prioridad | `[Priority: 0]` | Prioridad de la regla (0 por defecto si no se especificó) |
| Protocolo | `{ICMP}` | Protocolo del paquete que disparó la alerta |
| IP origen | `127.0.0.1` | IP de quien envió el paquete |
| IP destino | `127.0.0.1:23` | IP y puerto del destino |

---

## Comandos de Prueba para Generar Tráfico

Los comandos exactos para que el alumno genere las alertas de cada regla.

```cmd
:: Para Regla 1 — desde CMD en Windows (loopback)
ping 127.0.0.1

:: Para Regla 2 — intento de Telnet
:: El servicio Telnet no está corriendo, pero el paquete SYN genera la alerta en Snort
telnet localhost 23
:: Si Telnet Client no está instalado en Windows 11:
:: Panel de control -> Programas -> Activar características de Windows -> Telnet Client -> OK
```

```powershell
# Para Regla 3 — curl con cmd.exe en la URL
curl http://example.com/cmd.exe
# Alternativa sin curl (abrir en el navegador con proxy desactivado):
# http://example.com/cmd.exe
```

!!! tip "Organización pedagógica sugerida"
    Organizar en parejas: un alumno ejecuta Snort en modo consola, el otro genera el tráfico desde otra ventana CMD. Cuando la alerta aparece, rotar roles. La visualización en tiempo real es el momento de mayor impacto del lab.

---

## Errores Comunes y Cómo Manejarlos

| Error | Causa probable | Solución |
|-------|---------------|----------|
| `ERROR: Cannot open the network interface. Please select an interface number.` | No se especificó la interfaz con `-i` | Ejecutar primero `snort.exe -W` para listar interfaces y usar el número correcto en `-i <número>` |
| `FATAL ERROR: [Snort] => Can't find a usable network interface` | Npcap no instalado o instalado sin "WinPcap API-compatible Mode" | Desinstalar Npcap, reinstalar marcando la casilla "WinPcap API-compatible Mode" |
| `snort -T` pasa pero en modo IDS no aparecen alertas | El archivo `mis-reglas.rules` no se está incluyendo en `snort-lab.conf` | Verificar que la línea `include $RULE_PATH\mis-reglas.rules` está en el archivo y que la ruta `RULE_PATH` es correcta |
| Error de sintaxis en `snort -T` con mensaje sobre `sid` | SID duplicado — dos reglas con el mismo `sid:1000010` | Verificar que cada regla tiene un `sid` único: 1000010, 1000011, 1000012 respectivamente |
| Alerta de ping aparece cientos o miles de veces | La regla ICMP no tiene la opción `threshold` | Agregar `threshold: type limit, track by_src, count 1, seconds 60;` a la regla ICMP antes del `sid` |
| No aparece alerta de Telnet aunque el comando se ejecutó | Snort está capturando en la interfaz de red principal, pero `telnet localhost 23` va por la interfaz loopback | Verificar con `snort.exe -W` el número de la interfaz loopback y reiniciar Snort con `-i <número_loopback>` |

---

## Notas de Dictado

### Timing sugerido (total: ~90 minutos de lab)

**Instalación Npcap + Snort (20 min):** Hacer que todos instalen en paralelo. Verificar con `snort --version` antes de avanzar. La señal de éxito: el comando muestra `Version 2.9.x.x (Build XXX)` sin errores.

**`snort-lab.conf` mínimo y `snort -T` (15 min):** El modo test es importante — confirma que la sintaxis de la configuración es válida antes de capturar tráfico real. "Si `snort -T` no pasa, no avanzar — debuggear con el alumno en ese momento." Los errores de `snort -T` suelen ser: ruta de reglas incorrecta, directiva mal escrita, o variable `HOME_NET` con formato incorrecto.

**Escribir las 3 reglas en `mis-reglas.rules` (20 min):** Los alumnos escriben manualmente (no copiar-pegar a ciegas) para entender la sintaxis. El instructor circula verificando: que los SIDs son únicos, que cada regla termina con punto y coma, que las opciones están dentro de los paréntesis.

**Ejecutar en modo IDS y generar alertas (30 min):** El momento de mayor impacto — ver la alerta aparecer en tiempo real mientras otro alumno genera el tráfico. Organizar en parejas: uno en Snort, otro generando tráfico con `ping` y `telnet`.

**Threshold (15 min):** Demostrar primero el alert storm: `ping -n 100 127.0.0.1` sin threshold genera 100 alertas. Luego agregar el threshold y repetir — ahora solo 1 alerta por minuto. Este contraste es pedagógicamente poderoso para explicar por qué el tuning de reglas es esencial en producción.

---

## Rúbrica de Calificación

| Criterio | Puntos | Cómo verificar |
|----------|--------|----------------|
| `snort -T -c snort-lab.conf` pasa sin errores | 2 | El alumno muestra la salida `Snort successfully validated the configuration!` |
| Regla 1 (ICMP) correctamente escrita y genera alerta | 2 | Código de la regla en el archivo + captura de pantalla de la alerta en consola |
| Regla 2 (Telnet) correctamente escrita y genera alerta | 2 | Código de la regla en el archivo + captura de pantalla de la alerta en consola |
| Regla 3 (HTTP content) correctamente escrita y genera alerta | 2 | Código de la regla en el archivo + captura de pantalla de la alerta en consola |
| Regla 1 con threshold agregado (sin alert storm) | 1 | Versión final del archivo `mis-reglas.rules` entregado con el `threshold` visible |
| Análisis escrito de una alerta (todos los campos explicados) | 1 | Párrafo de análisis en la entrega identificando timestamp, ID de regla, protocolo, IPs |

**Total: 10 puntos**

---

<!-- Solución instructor para: docs/seguridad-criptografia/en-la-red/ids-ips-p2.md -->
