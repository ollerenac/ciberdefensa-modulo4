---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab Despliegue y Análisis de Honeypot (LAB-05)

!!! danger "Solo para instructores"
    Este documento contiene el procedimiento esperado, las respuestas modelo y la rúbrica. Durante el ejercicio, proyectar únicamente la guía del alumno y el extracto de log sin respuestas.

!!! warning "Alcance obligatorio"
    La práctica principal escucha exclusivamente en `127.0.0.1`. No cambiar a `0.0.0.0`, no crear reglas de entrada y no conectar equipos entre sí. Una práctica en red necesitaría autorización, segmentación y un plan de contención distintos.

---

## Decisión técnica del laboratorio

El procedimiento canónico despliega un honeypot didáctico de baja interacción mediante `honeypot-simple.py`. El script usa la biblioteca estándar de Python 3, registra conexiones TCP y permite analizar correctamente el origen, el destino y la marca de tiempo de cada evento.

---

## Preparación del instructor

Antes de la clase:

1. Verificar Python 3 en la VM o equipo Windows 11:

   ```powershell
   py -3 --version
   ```

   Si aparece `Python 3.x.x`, Python está listo. Si el comando falla, comprobar que WinGet está disponible:

   ```powershell
   winget --version
   ```

   Instalar el **Python Install Manager** oficial:

   ```powershell
   winget install 9NQ7512CXL7T -e --accept-package-agreements --disable-interactivity
   ```

   Este comando instala el administrador, no el runtime de Python. El identificador está publicado en la [documentación oficial de Python para Windows](https://docs.python.org/3/using/windows.html#advanced-installation).

   Si el siguiente comando aún no se reconoce, cerrar PowerShell y abrir una ventana nueva. Después instalar explícitamente el runtime estable predeterminado:

   ```powershell
   pymanager install default
   ```

   Repetir la comprobación inicial. El criterio de éxito es `Python 3.x.x`.

   !!! note "Contingencia si WinGet no está disponible"
       Si `winget --version` falla o una política institucional bloquea WinGet, descargar el **Python Install Manager** únicamente desde [python.org](https://www.python.org/downloads/windows/). Instalarlo, abrir una PowerShell nueva si fuera necesario y continuar con la instalación del runtime y la comprobación indicadas arriba. Esta es una contingencia; la ruta principal es WinGet.

2. Crear el directorio de práctica:

   ```powershell
   New-Item -ItemType Directory -Force C:\HoneypotLab
   ```

3. Confirmar que el puerto está libre:

   ```powershell
   Get-NetTCPConnection -LocalPort 9999 -State Listen -ErrorAction SilentlyContinue
   ```

4. Guardar una copia de `honeypot-simple.py` en USB o en el medio institucional autorizado. No depender de internet durante la sesión.

5. Ejecutar una UAT completa: inicio, `Test-NetConnection`, aparición del evento, escritura de `honeypot.log` y cierre con `Ctrl+C`.

---

## Script canónico

El bloque siguiente debe ser idéntico al publicado en la guía del alumno. Guardarlo como `C:\HoneypotLab\honeypot-simple.py`:

```python
# Honeypot minimo de demostracion: detecta y registra conexiones TCP.
import datetime
from pathlib import Path
import socket
import sys

HOST = "127.0.0.1"
PORT = int(sys.argv[1]) if len(sys.argv) > 1 else 9999
LOG_PATH = Path(__file__).with_name("honeypot.log")

print(f"Honeypot iniciado en {HOST}:{PORT}")
print(f"Log: {LOG_PATH}")
print("Esperando conexiones... (Ctrl+C para detener)")

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server:
    server.bind((HOST, PORT))
    server.listen(5)

    try:
        while True:
            connection, source = server.accept()
            with connection:
                destination = connection.getsockname()
                timestamp = datetime.datetime.now().astimezone().isoformat(timespec="seconds")
                event = (
                    f"[{timestamp}] EVENTO_TCP "
                    f"origen={source[0]}:{source[1]} "
                    f"destino={destination[0]}:{destination[1]}"
                )
                print(event)
                with LOG_PATH.open("a", encoding="utf-8") as log_file:
                    log_file.write(event + "\n")
    except KeyboardInterrupt:
        print("\nHoneypot detenido.")
```

### Propiedades que deben preservarse

- `127.0.0.1` limita la escucha al mismo equipo.
- El puerto por defecto es `9999`, pero puede pasarse otro puerto alto como argumento.
- `Path(__file__)` coloca el log junto al script, independientemente del directorio desde el que se invoque.
- `getsockname()` registra el destino real y evita confundirlo con el puerto efímero del cliente.
- `socket`, `datetime`, `pathlib` y `sys` pertenecen a la biblioteca estándar.

---

## UAT esperada en Windows 11

### 1. Iniciar el sensor

```powershell
py -3 C:\HoneypotLab\honeypot-simple.py 9999
```

Salida inicial:

```text
Honeypot iniciado en 127.0.0.1:9999
Log: C:\HoneypotLab\honeypot.log
Esperando conexiones... (Ctrl+C para detener)
```

### 2. Generar tráfico desde otra consola

```powershell
Test-NetConnection 127.0.0.1 -Port 9999
```

Criterio de éxito:

```text
TcpTestSucceeded : True
```

### 3. Verificar consola y archivo

El puerto de origen variará en cada equipo:

```text
[2026-08-24T10:15:01-05:00] EVENTO_TCP origen=127.0.0.1:54321 destino=127.0.0.1:9999
```

```powershell
Get-Content C:\HoneypotLab\honeypot.log
```

Repetir la conexión tres veces. Deben aparecer tres eventos adicionales en consola y archivo. Después detener con `Ctrl+C`.

!!! note "Interpretación correcta"
    `54321` representa el puerto de origen elegido para el cliente; `9999` es el puerto destino del sensor. Una sucesión de puertos de origen no demuestra que se hayan probado varios destinos.

---

## Extracto de log para el ejercicio

Este bloque debe coincidir exactamente con el de la guía del alumno:

```text
[2026-08-24T10:15:01-05:00] EVENTO_TCP origen=192.168.1.100:51024 destino=192.168.1.10:9999
[2026-08-24T10:15:45-05:00] EVENTO_TCP origen=192.168.1.100:51025 destino=192.168.1.10:9999
[2026-08-24T10:16:02-05:00] EVENTO_TCP origen=192.168.1.100:51026 destino=192.168.1.10:9999
[2026-08-24T10:16:03-05:00] EVENTO_TCP origen=192.168.1.100:51027 destino=192.168.1.10:9999
[2026-08-24T10:16:04-05:00] EVENTO_TCP origen=192.168.1.100:51028 destino=192.168.1.10:9999
[2026-08-24T10:45:22-05:00] EVENTO_TCP origen=10.0.0.5:52048 destino=192.168.1.10:9999
[2026-08-24T10:45:23-05:00] EVENTO_TCP origen=10.0.0.5:52049 destino=192.168.1.10:9999
[2026-08-24T11:02:15-05:00] EVENTO_TCP origen=192.168.1.77:53000 destino=192.168.1.10:9999
[2026-08-24T11:02:18-05:00] EVENTO_TCP origen=192.168.1.77:53001 destino=192.168.1.10:9999
[2026-08-24T11:02:20-05:00] EVENTO_TCP origen=192.168.1.77:53002 destino=192.168.1.10:9999
```

### Respuestas modelo

**1. ¿Cuántos eventos y cuántas IP de origen distintas aparecen?**

Diez eventos y tres IP de origen: `192.168.1.100`, `10.0.0.5` y `192.168.1.77`.

**2. ¿Cuál es la IP más activa? ¿Qué puede observarse sin atribuir una causa?**

`192.168.1.100`, con cinco eventos entre las 10:15:01 y las 10:16:04. Es una concentración breve que podría corresponder a automatización, reintentos, monitoreo o reconocimiento. El log aislado no permite escoger una de esas causas.

**3. ¿En qué ventanas se concentra la actividad?**

Hay tres ventanas:

- 10:15:01–10:16:04: cinco eventos de `192.168.1.100`;
- 10:45:22–10:45:23: dos eventos de `10.0.0.5`;
- 11:02:15–11:02:20: tres eventos de `192.168.1.77`.

Los intervalos permiten buscar eventos relacionados en otros sistemas, pero no prueban que las tres fuentes pertenezcan al mismo actor.

**4. ¿Qué debe comprobarse para `192.168.1.77`?**

Confirmar primero el activo y su responsable en el inventario, el calendario de escaneos autorizados, tareas de monitoreo y cambios recientes. Después correlacionar con Snort, firewall, proxy y autenticación. Si el conjunto de evidencia indica compromiso, aplicar el procedimiento institucional de aislamiento y preservación de evidencia; la IP interna por sí sola no basta.

---

## Errores comunes y diagnóstico

| Síntoma | Causa probable | Acción |
|---|---|---|
| Ni `py` ni `pymanager` se reconocen | El Python Install Manager está ausente o la terminal aún no reconoce sus comandos | Cerrar y abrir PowerShell; si persiste, seguir primero la ruta WinGet de Preparación y usar la descarga manual solo como contingencia |
| `pymanager` funciona, pero la comprobación de Python 3 falla | El administrador está instalado, pero falta el runtime | Ejecutar la instalación explícita del runtime indicada en Preparación y repetir la comprobación inicial hasta obtener `Python 3.x.x` |
| `WinError 10048` o `Address already in use` | Otro proceso ya escucha en ese puerto | Consultar `Get-NetTCPConnection -LocalPort 9999 -State Listen`; usar otro puerto alto en ambos comandos |
| `WinError 10013` | Puerto excluido o reservado, política de seguridad o protección del endpoint | No asumir que se debe al número del puerto; probar otro puerto alto y escalar al administrador si persiste |
| `TcpTestSucceeded : False` | El sensor no está ejecutándose, se usó otro puerto o terminó con error | Volver a la primera consola, confirmar el puerto mostrado y repetir con el mismo valor |
| El evento aparece en consola pero no se encuentra el archivo | Se buscó el log en el directorio actual | Consultar `C:\HoneypotLab\honeypot.log`; el script siempre escribe junto a su propio archivo |
| Otra computadora no puede conectarse | Comportamiento esperado: el sensor escucha en loopback | Mantener localhost; no abrir el sensor a la red como solución improvisada |
| `localhost` resuelve a `::1` y la prueba falla | El script usa IPv4 (`AF_INET`) | Usar explícitamente `127.0.0.1` como indica la guía |

---

## Cowrie en WSL2 — ampliación opcional

Cowrie no forma parte de la ruta crítica. Su modo shell simula un sistema y se clasifica como **interacción media**; el modo proxy hacia un backend separado puede ofrecer alta interacción.

Solo realizar esta ampliación si WSL2 con Ubuntu ya está instalado y el grupo terminó la práctica principal. El flujo vigente para operadores es:

```bash
sudo apt-get update
sudo apt-get install -y python3-pip python3-venv libssl-dev libffi-dev \
  build-essential libpython3-dev python3-minimal authbind

mkdir -p ~/my-honeypot
cd ~/my-honeypot
python3 -m venv cowrie-env
source cowrie-env/bin/activate
python -m pip install --upgrade pip
python -m pip install cowrie
cowrie init
cowrie start
```

Probar en otra terminal WSL2:

```bash
cd ~/my-honeypot
source cowrie-env/bin/activate
ssh -p 2222 root@localhost
```

Detener desde el directorio inicializado:

```bash
cd ~/my-honeypot
source cowrie-env/bin/activate
cowrie stop
```

Consultar siempre la [guía oficial de instalación de Cowrie](https://github.com/cowrie/cowrie/blob/main/INSTALL.rst) antes de usar esta ampliación, porque sus requisitos pueden cambiar.

---

## Secuencia sugerida para las 3 horas

| Minutos | Actividad |
|---:|---|
| 0–20 | Recap, objetivo y diferencia entre alerta y atribución |
| 20–45 | Arquitectura del sensor, alcance localhost y campos TCP |
| 45–70 | Preparación, creación del script e inicio |
| 70–95 | Conexiones controladas y lectura de `honeypot.log` |
| 95–130 | Ejercicio individual y discusión de respuestas |
| 130–160 | Correlación con inventario, Snort, proxy y firewall |
| 160–175 | Validación individual y rúbrica |
| 175–180 | Cierre: el honeypot inicia la investigación, no la concluye |

Cowrie es una ampliación fuera de esta distribución o un reemplazo para un bloque avanzado previamente planificado; no añadirlo improvisadamente a los 180 minutos.

---

## Rúbrica de calificación

| Criterio | Puntos | Evidencia |
|---|---:|---|
| El sensor inicia en `127.0.0.1` y un puerto alto | 2 | Consola con dirección, puerto y ruta del log |
| `Test-NetConnection` produce un evento | 2 | `TcpTestSucceeded : True` y evento correspondiente |
| El archivo conserva origen y destino | 1 | `Get-Content C:\HoneypotLab\honeypot.log` |
| Q1: conteo de eventos y fuentes | 1 | 10 eventos, 3 IP de origen |
| Q2: fuente más activa e inferencia prudente | 1 | `192.168.1.100`, cinco eventos, sin atribución definitiva |
| Q3: ventanas temporales | 1 | Identifica correctamente las tres ventanas |
| Q4: hipótesis, correlación y decisión | 2 | Verifica autorización e inventario antes de contener |

**Total: 10 puntos**

---

## Mensaje de cierre

Un honeypot genera una señal deliberadamente fácil de investigar. Su utilidad desaparece si el analista confunde una conexión con una atribución, o un puerto de origen con el servicio atacado. El aprendizaje central es conservar el evento, interpretar sus campos y decidir mediante correlación.

<!-- Solución instructor para: docs/seguridad-criptografia/en-la-red/honeypots-p2.md -->
