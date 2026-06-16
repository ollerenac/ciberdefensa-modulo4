---
# Horas asignadas: 3 hrs
# Tipo: Teoría + Laboratorio (versión alumno)
---

# Honeypots: Despliegue y Análisis en Windows 11 (Parte 2)

> **Duración:** 3 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red | **Continuación de Parte 1**

## Recap de la Parte 1

En la Parte 1 definimos el honeypot como un recurso sin uso legítimo, donde cualquier conexión es sospechosa por definición y no existen falsos positivos. Distinguimos los honeypots de baja interacción (emulan servicios, más seguros) de los de alta interacción (ejecutan servicios reales, más datos pero mayor riesgo). Vimos también su valor en la detección de insider threats y los honeytokens como variante simple. En esta sesión desplegamos un honeypot de baja interacción en Windows 11, generamos tráfico de prueba y analizamos los logs para extraer inteligencia.

---

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Instalar HoneyPy en Windows 11 o usar el script Python de fallback como honeypot básico
- Configurar la emulación de servicios SSH y HTTP en puertos locales
- Generar conexiones de prueba al honeypot y observar el registro en tiempo real
- Leer y analizar las entradas del log del honeypot identificando todos los campos relevantes

---

## Herramienta: HoneyPy

HoneyPy es un honeypot de baja interacción escrito en Python. Emula servicios en puertos específicos y registra toda conexión entrante sin ejecutar un sistema real. Al ser Python, funciona en Windows de forma nativa sin necesidad de virtualización.

**Características relevantes para el lab:**
- Emula servicios TCP en puertos configurables (SSH en 22, HTTP en 80, etc.)
- Registra: timestamp, IP de origen, puerto de origen, servicio emulado
- Configuración mediante archivo `.cfg` simple
- Alternativa: si HoneyPy no instala correctamente, el lab continúa con un script Python básico incluido en esta página

---

## Instalación de HoneyPy en Windows 11

### Prerrequisito: Python 3

Verificar que Python 3 está instalado:

```
python --version
```

Si no está instalado, descargar desde `https://python.org/downloads/`. Durante la instalación, marcar "Add Python to PATH".

### Instalar HoneyPy

```
pip install honeypy
```

Verificar la instalación:

```
honeypy --version
```

!!! tip "Si pip install honeypy falla"
    En redes con restricciones (proxies corporativos, redes sin acceso PyPI), intentar la instalación directa desde el repositorio de código fuente:
    ```
    pip install git+https://github.com/foospidy/HoneyPy.git
    ```
    Si tampoco funciona, usar el **Script Python Básico** de la sección siguiente — proporciona las mismas capacidades de detección para este laboratorio sin dependencias externas.

---

## Configuración del Honeypot

HoneyPy usa un archivo de configuración `honeypy.cfg`. Crear el archivo en `C:\HoneyPy\honeypy.cfg` con el siguiente contenido:

```ini
[honeypy]
# Directorio donde se guardan los logs del honeypot
logdir = C:\HoneyPy\logs

[services]
# Emular servicio SSH en puerto 22
ssh = true
ssh_port = 22

# Emular servicio HTTP en puerto 80
http = true
http_port = 80
```

Crear la carpeta de logs:

```
mkdir C:\HoneyPy\logs
```

Iniciar el honeypot:

```
honeypy -c C:\HoneyPy\honeypy.cfg
```

!!! warning "Conflictos de puertos antes de iniciar"
    El honeypot intentará escuchar en los puertos 22 y 80. Si algún servicio legítimo ya usa esos puertos, el honeypot no podrá iniciarse. Verificar antes de iniciar:
    ```
    netstat -an | findstr ":22 "
    netstat -an | findstr ":80 "
    ```
    Si algún puerto está en uso, cambiar el puerto del honeypot en `honeypy.cfg` a uno no utilizado (por ejemplo, `ssh_port = 2222` o `http_port = 8080`).

---

## Alternativa con Socket Python Básico

Si HoneyPy no instala correctamente, este script Python implementa un honeypot mínimo funcional. No requiere dependencias externas — solo Python 3 estándar:

```python
# honeypot-simple.py
# Honeypot minimo: escucha en un puerto TCP y registra cada conexion
import socket
import datetime

HOST = '127.0.0.1'   # Solo escuchar en localhost (no exponer a la red)
PORT = 9999           # Puerto del honeypot (elegir uno no usado)

print(f"[*] Honeypot iniciando en {HOST}:{PORT}")
print("[*] Presionar Ctrl+C para detener")
print("[*] Esperando conexiones...")

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    s.bind((HOST, PORT))
    s.listen()
    while True:
        conn, addr = s.accept()
        timestamp = datetime.datetime.now().isoformat()
        print(f"[{timestamp}] CONEXION desde {addr[0]}:{addr[1]}")
        conn.close()
```

Guardar como `C:\HoneyPy\honeypot-simple.py` e iniciar:

```
python C:\HoneyPy\honeypot-simple.py
```

El script registra cada conexión en la consola con timestamp e IP de origen. Para este laboratorio, la funcionalidad es equivalente a HoneyPy.

---

## Generando Tráfico de Prueba

Con el honeypot corriendo, abrir una segunda ventana de CMD para generar conexiones de prueba:

**Si usando HoneyPy con puertos 22 y 80:**
```
# Conectar al puerto SSH emulado
telnet localhost 22

# Conectar al puerto HTTP emulado
curl http://localhost:80
```

**Si usando el script básico en puerto 9999:**
```
# Conectar al honeypot básico
telnet localhost 9999
```

**Opcional avanzado — si se tiene WSL2 con Cowrie instalado:**
```
# Cowrie emula un servidor SSH interactivo completo
ssh root@localhost -p 2222
```
Con Cowrie, se puede escribir comandos como si fuera un servidor Linux real: Cowrie los registra todos aunque no ejecute nada. Esto permite experimentar con técnicas de post-explotación de forma segura.

---

## Análisis de Logs del Honeypot

Esta es la sección pedagógica central del laboratorio. El análisis de logs es la habilidad que convierte el honeypot de una "trampa" en una fuente de inteligencia accionable.

**Formato de log de HoneyPy** (archivo en `C:\HoneyPy\logs\`):

```
2026-06-16T14:30:00.123 | 127.0.0.1 | 54321 | ssh | conexion entrante
2026-06-16T14:30:01.456 | 127.0.0.1 | 54321 | ssh | desconexion
2026-06-16T14:31:05.789 | 127.0.0.1 | 54899 | http | conexion entrante
```

**Formato de log del script básico** (en consola):

```
[2026-06-16T14:30:00.123456] CONEXION desde 127.0.0.1:54321
[2026-06-16T14:31:05.789012] CONEXION desde 127.0.0.1:54899
```

**Ejercicio de análisis:** El instructor proyecta el siguiente extracto de log de ejemplo con 10 entradas. El alumno debe responder las 4 preguntas:

```
[2026-06-16T09:14:00] CONEXION desde 192.168.1.22:1024
[2026-06-16T09:14:01] CONEXION desde 192.168.1.22:1025
[2026-06-16T09:14:01] CONEXION desde 192.168.1.22:1026
[2026-06-16T09:14:02] CONEXION desde 192.168.1.55:4455
[2026-06-16T09:14:03] CONEXION desde 192.168.1.22:1027
[2026-06-16T09:14:03] CONEXION desde 192.168.1.22:1028
[2026-06-16T09:22:17] CONEXION desde 192.168.1.55:6699
[2026-06-16T09:22:19] CONEXION desde 192.168.1.55:6700
[2026-06-16T09:22:20] CONEXION desde 192.168.1.55:6701
[2026-06-16T09:22:21] CONEXION desde 192.168.1.55:6702
```

**Preguntas:**

1. ¿Cuántas conexiones únicas se registraron en total?
2. ¿Cuál es la IP más activa y qué patrón muestra su comportamiento?
3. ¿A qué horas se concentraron las conexiones? ¿Hay diferencia de contexto entre los dos grupos?
4. Una conexión viene desde la IP 192.168.1.55, que pertenece a la red interna de la unidad. ¿Qué sugiere esto sobre el posible origen del ataque?

---

## Valor Operacional del Análisis

El honeypot es un detector, no un protector. Registra eventos — el valor está en la respuesta que los datos permiten. Cuando el log registra una conexión, el analista debe ejecutar este proceso:

1. **Identificar la fuente:** ¿La IP es interna o externa? Si es interna, ¿a qué equipo corresponde según el DHCP o el inventario de red?

2. **Evaluar el patrón:** ¿Es una conexión aislada o un patrón repetido? ¿Las conexiones múltiples son a puertos secuenciales (port scan)?

3. **Clasificar el origen probable:**
   - IP interna con patrón de escaneo → equipo comprometido realizando reconocimiento lateral, o insider threat
   - IP externa (si el honeypot tiene IP pública) → reconocimiento automatizado de internet o ataque dirigido

4. **Correlacionar con otros logs:** Contrastar con el `access.log` de Squid (¿ese equipo hizo descargas inusuales?), con las alertas de Snort (¿hay tráfico sospechoso adicional desde esa IP?), con los logs del firewall.

5. **Escalar o contener:** Si la evidencia apunta a compromiso, aislar el equipo de la red antes de investigar para detener la propagación.

---

## Validación del laboratorio

- [ ] El honeypot (HoneyPy o script básico) inicia sin errores y muestra mensaje de escucha en el puerto configurado
- [ ] Al conectar desde `telnet localhost <puerto>` al honeypot, aparece una entrada en el log (consola o archivo)
- [ ] El log muestra: timestamp, IP de origen, puerto de origen
- [ ] El alumno puede responder correctamente las 4 preguntas del ejercicio de análisis del log de ejemplo

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    El oficial de seguridad de una base despliega un honeypot en la red interna con el nombre de recurso "FS-RESERVADO-ALPHA". A las 02:34, el honeypot registra 6 conexiones en 2 segundos desde la IP interna 192.168.10.55, correspondiente al equipo de un cabo de guardia. El patrón (múltiples puertos en segundos) indica una herramienta automatizada de escaneo, no acceso manual. El oficial activa el protocolo de respuesta a incidentes: aísla el equipo 192.168.10.55, analiza su memoria y disco, y descubre un troyano de reconocimiento instalado 3 horas antes mediante un correo de phishing. Sin el honeypot, el ataque hubiera continuado en silencio — el IDS perimetral no habría detectado el movimiento lateral en la red interna.

---

## Resumen

- HoneyPy es un honeypot de baja interacción en Python que emula servicios y registra conexiones sin ejecutar un sistema real; si falla la instalación, el script Python básico provee la misma funcionalidad de detección
- El honeypot solo detecta — no bloquea ni protege. Su valor está en la calidad del análisis posterior al log
- Todo acceso al honeypot debe tratarse como un incidente hasta demostrar lo contrario: incluso conexiones desde IPs internas conocidas son sospechosas
- La correlación del log del honeypot con logs de Squid, Snort y firewall proporciona contexto completo para una respuesta a incidentes efectiva
- En entornos militares, el honeypot es especialmente valioso para detectar movimiento lateral y reconocimiento interno, que los sistemas perimetrales no pueden ver

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- Repositorio HoneyPy en GitHub: `https://github.com/foospidy/HoneyPy` — documentación completa y plugins adicionales
- Cowrie SSH Honeypot: `https://github.com/cowrie/cowrie` — honeypot SSH interactivo con registro de sesión completa (requiere Linux o WSL2)
- T-Pot: plataforma de múltiples honeypots con dashboards Kibana (para referencia de arquitecturas avanzadas, no para este lab)

<!-- Solución disponible para el instructor en: docs/instructor/lab-honeypot-solucion.md -->

---

*Siguiente: [Examen Final de la Asignatura](examen-final.md)*
