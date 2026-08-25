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

El procedimiento canónico despliega un honeypot didáctico de baja interacción mediante `honeypot-simple.py`. El script usa la biblioteca estándar de Python 3, registra conexiones TCP y permite analizar correctamente el origen, el destino y la marca de tiempo de cada evento. La guía incluye además `honeypot-multiservicio.py` como ampliación opcional fuera de la ruta evaluada de tres horas.

---

## Preparación del instructor

Antes de la clase:

1. Verificar Python 3 en la VM o equipo Windows 11:

   ```powershell
   py -3 --version
   ```

   Si aparece `Python 3.x.x`, Python está listo. Si el comando falla, instalar el **Python Install Manager** directamente desde el servidor oficial:

   ```powershell
   Add-AppxPackage -AppInstallerFile https://www.python.org/ftp/python/pymanager/pymanager.appinstaller
   ```

   El comando puede terminar sin mostrar salida. Si no aparece texto rojo ni una excepción, instalar explícitamente el runtime estable predeterminado:

   ```powershell
   py install default
   ```

   Durante la UAT, este paso actualizó primero Python Install Manager, verificó la firma de `index-windows.json` y descargó el runtime. Las versiones concretas cambiarán con el tiempo; no exigir un número fijo.

   Comprobar el resultado:

   ```powershell
   py -3 --version
   ```

   El criterio de éxito es `Python 3.x.x`. Si `py` no se reconoce después de instalar el administrador, cerrar PowerShell, abrir una ventana nueva y repetir `py install default` y la comprobación.

   !!! note "Alternativa mediante WinGet"
       Si Microsoft Store funciona en la sesión interactiva, se puede usar `winget install --id 9NQ7512CXL7T --exact --source msstore --accept-source-agreements --accept-package-agreements --authentication-mode interactive`. Ante `0x8a15000f` o `0x80070520`, volver a la ruta `Add-AppxPackage`; no invertir tiempo de clase en reparar WinGet.

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

## UAT de la ampliación multservicio

Esta ampliación requiere 45–60 minutos adicionales y no modifica la rúbrica de diez puntos. Debe ejecutarse únicamente si el grupo terminó el laboratorio principal y el instructor ya verificó que los puertos `8022`, `8080` y `2121` están libres.

### Script multservicio canónico

El bloque siguiente debe ser idéntico al publicado en la guía del alumno. Guardarlo como `C:\HoneypotLab\honeypot-multiservicio.py`:

```python
# Honeypot multservicio de baja interaccion para uso exclusivo en localhost.
import datetime
from pathlib import Path
import socket
import threading

HOST = "127.0.0.1"
SERVICES = (("SSH", 8022), ("HTTP", 8080), ("FTP", 2121))
LOG_PATH = Path(__file__).with_name("honeypot-multiservicio.log")
MAX_INPUT = 1024
LOG_LOCK = threading.Lock()
STOP_EVENT = threading.Event()


def register_event(service, source, destination, event_type, detail=None):
    timestamp = datetime.datetime.now().astimezone().isoformat(timespec="seconds")
    event = (
        f"[{timestamp}] servicio={service} "
        f"origen={source[0]}:{source[1]} "
        f"destino={destination[0]}:{destination[1]} "
        f"evento={event_type}"
    )
    if detail:
        event += f' detalle="{detail}"'

    with LOG_LOCK:
        print(event)
        with LOG_PATH.open("a", encoding="utf-8") as log_file:
            log_file.write(event + "\n")


def receive_line(connection):
    data = bytearray()
    while len(data) < MAX_INPUT:
        chunk = connection.recv(min(256, MAX_INPUT - len(data)))
        if not chunk:
            break
        data.extend(chunk)
        if b"\n" in data:
            break

    text = data.decode("utf-8", errors="replace").splitlines()
    first_line = text[0] if text else ""
    safe_line = "".join(char if char.isprintable() else "?" for char in first_line)
    return safe_line.replace('"', "'")[:120]


def handle_client(connection, source, service):
    with connection:
        destination = connection.getsockname()
        connection.settimeout(2)
        register_event(service, source, destination, "CONEXION")

        try:
            if service == "SSH":
                connection.sendall(b"SSH-2.0-OpenSSH_8.9p1 LabHoneypot\r\n")

            elif service == "HTTP":
                request_line = receive_line(connection)
                parts = request_line.split()
                method = parts[0] if parts else "INVALIDA"
                path = parts[1].split("?", 1)[0] if len(parts) > 1 else "/"
                register_event(service, source, destination, "PETICION", f"{method} {path}")

                body = b"<h1>Portal de administracion</h1><p>Servicio de laboratorio.</p>"
                response = (
                    b"HTTP/1.1 200 OK\r\n"
                    b"Content-Type: text/html; charset=utf-8\r\n"
                    + f"Content-Length: {len(body)}\r\n".encode("ascii")
                    + b"Connection: close\r\n\r\n"
                    + body
                )
                connection.sendall(response)

            elif service == "FTP":
                connection.sendall(b"220 FTP de laboratorio listo\r\n")
                command_line = receive_line(connection)
                command = command_line.split(maxsplit=1)[0].upper() if command_line else "VACIO"
                register_event(service, source, destination, "COMANDO", command)
                connection.sendall(b"530 Autenticacion no disponible\r\n")

        except (ConnectionError, socket.timeout):
            return


def serve(service, listener):
    while not STOP_EVENT.is_set():
        try:
            connection, source = listener.accept()
        except OSError:
            break
        threading.Thread(
            target=handle_client,
            args=(connection, source, service),
            daemon=True,
        ).start()


def create_listeners():
    listeners = []
    try:
        for service, port in SERVICES:
            listener = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            try:
                listener.bind((HOST, port))
                listener.listen(5)
            except OSError:
                listener.close()
                raise
            listeners.append((service, port, listener))
    except OSError:
        for _, _, listener in listeners:
            listener.close()
        raise
    return listeners


def main():
    try:
        listeners = create_listeners()
    except OSError as error:
        raise SystemExit(f"No se pudieron abrir todos los puertos: {error}") from error

    print("Honeypot multservicio iniciado:")
    for service, port, _ in listeners:
        print(f"- {service} simulado en {HOST}:{port}")
    print(f"Log: {LOG_PATH}")
    print("Esperando conexiones... (Ctrl+C para detener)")

    threads = []
    for service, _, listener in listeners:
        thread = threading.Thread(target=serve, args=(service, listener), daemon=True)
        thread.start()
        threads.append(thread)

    try:
        while all(thread.is_alive() for thread in threads):
            STOP_EVENT.wait(0.5)
    except KeyboardInterrupt:
        print("\nHoneypot multservicio detenido.")
    finally:
        STOP_EVENT.set()
        for _, _, listener in listeners:
            listener.close()


if __name__ == "__main__":
    main()
```

### Secuencia UAT y criterios de éxito

1. Comprobar los puertos antes de iniciar:

   ```powershell
   8022, 8080, 2121 | ForEach-Object {
       Get-NetTCPConnection -LocalPort $_ -State Listen -ErrorAction SilentlyContinue
   }
   ```

   No debe aparecer ninguna fila.

2. Iniciar el emulador:

   ```powershell
   py -3 C:\HoneypotLab\honeypot-multiservicio.py
   ```

3. En otra consola, verificar las tres escuchas:

   ```powershell
   Get-NetTCPConnection -LocalPort 8022,8080,2121 -State Listen |
       Select-Object LocalAddress, LocalPort, State
   ```

   Deben aparecer `8022`, `8080` y `2121` asociados a `127.0.0.1`.

4. Ejecutar sin modificaciones los clientes SSH, HTTP y FTP publicados en los pasos D, E y F de la guía del alumno. Los resultados esperados son:

   | Prueba | Respuesta | Eventos mínimos |
   |---|---|---|
   | SSH simulado | `SSH-2.0-OpenSSH_8.9p1 LabHoneypot` | `servicio=SSH`, destino `8022`, `evento=CONEXION` |
   | HTTP simulado | Estado `200` y texto `Portal de administracion` | `servicio=HTTP`, destino `8080`, `evento=CONEXION` y `detalle="GET /admin"` |
   | FTP simulado | Saludo `220` y rechazo `530` | `servicio=FTP`, destino `2121`, `evento=CONEXION` y `detalle="USER"` |

5. Confirmar que `honeypot-multiservicio.log` no contiene `alumno`, encabezados HTTP, parámetros de consulta ni contraseñas. Después detener con `Ctrl+C` y comprobar que desaparecen las tres escuchas.

### Respuestas modelo de la ampliación

1. SSH corresponde al puerto destino `8022`, HTTP a `8080` y FTP a `2121`.
2. Un banner es texto controlado por quien opera el servicio. Puede imitar otro producto o versión; es una pista para la identificación, no una prueba concluyente del software real.
3. HTTP conserva solo método y ruta sin la consulta; FTP conserva únicamente el verbo. Se omiten argumentos, encabezados, cuerpos y contraseñas para minimizar la recopilación de datos y evitar guardar secretos.
4. Tres puertos **destino** distintos muestran que el cliente contactó varias superficies simuladas del mismo host. Esto es compatible con reconocimiento multservicio en un escenario real, pero en el laboratorio sigue siendo tráfico autorizado y no demuestra intención maliciosa. Los puertos de origen cambiantes, por sí solos, solo reflejan asignaciones efímeras del cliente.

!!! warning "Controles que no deben relajarse"
    El script debe conservar `HOST = "127.0.0.1"`, las lecturas acotadas, el tiempo de espera y la exclusión de credenciales. No crear reglas de firewall ni reemplazar los puertos altos por `22`, `80` o `21` durante la clase.

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
| Ni `py` ni `pymanager` se reconocen | El Python Install Manager está ausente o la terminal aún no reconoce sus comandos | Ejecutar la ruta `Add-AppxPackage`; cerrar y abrir PowerShell antes de volver a comprobar |
| `py` se reconoce, pero la comprobación de Python 3 falla | El administrador está instalado, pero falta el runtime | Ejecutar `py install default` y repetir la comprobación hasta obtener `Python 3.x.x` |
| WinGet devuelve `0x8a15000f` | La fuente comunitaria `winget` carece de datos aunque el paquete pertenezca a `msstore` | No reparar fuentes durante la clase; instalar desde Python.org con `Add-AppxPackage` |
| WinGet devuelve `0x80070520` | Microsoft Store no dispone de una sesión de inicio válida para adquirir el paquete | Abandonar la ruta Store y usar `Add-AppxPackage` en la sesión interactiva del alumno |
| `WinError 10048` o `Address already in use` | Otro proceso ya escucha en ese puerto | Consultar `Get-NetTCPConnection -LocalPort 9999 -State Listen`; usar otro puerto alto en ambos comandos |
| `WinError 10013` | Puerto excluido o reservado, política de seguridad o protección del endpoint | No asumir que se debe al número del puerto; probar otro puerto alto y escalar al administrador si persiste |
| `TcpTestSucceeded : False` | El sensor no está ejecutándose, se usó otro puerto o terminó con error | Volver a la primera consola, confirmar el puerto mostrado y repetir con el mismo valor |
| El multservicio muestra que no pudo abrir todos los puertos | Al menos uno de `8022`, `8080` o `2121` ya está ocupado | Consultar cada puerto antes de iniciar; detener el proceso de práctica anterior o editar un puerto alto en el script y en todas sus pruebas |
| Una prueba de banner queda esperando | El emulador no inició, se consultó otro puerto o la respuesta no llegó antes del límite | Confirmar las tres escuchas y conservar `ReceiveTimeout = 3000` en los clientes PowerShell |
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

El ejercicio multservicio y Cowrie son ampliaciones fuera de esta distribución o reemplazos para un bloque avanzado previamente planificado; no añadirlos improvisadamente a los 180 minutos.

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
