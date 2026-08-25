---
# Horas asignadas: 3 hrs
# Tipo: Teoría + Laboratorio (versión alumno)
---

# Honeypots: Despliegue y Análisis en Windows 11 (Parte 2)

> **Duración:** 3 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red | **Continuación de Parte 1**

## Recap de la Parte 1

En la Parte 1 definimos el honeypot como un recurso sin uso operacional. Una conexión produce una alerta de alta confianza, pero el analista todavía debe descartar actividades autorizadas y errores de configuración. También distinguimos los honeypots de baja interacción de los de alta interacción, revisamos los honeytokens y estudiamos su utilidad para detectar reconocimiento interno.

En esta sesión desplegaremos un **honeypot didáctico de baja interacción** en Windows 11, generaremos tráfico controlado y analizaremos los eventos registrados.

---

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Desplegar un sensor TCP local usando Python 3 y únicamente su biblioteca estándar
- Generar una conexión de prueba sin instalar Telnet ni herramientas externas
- Diferenciar el puerto de origen del cliente del puerto destino del sensor
- Analizar un log sin convertir una observación aislada en una atribución definitiva
- Proponer acciones de verificación, correlación y contención basadas en evidencia
- Reconocer, en la ampliación opcional, cómo distintos banners y puertos representan servicios simulados

---

## Herramienta del laboratorio

Usaremos un honeypot didáctico construido con el módulo `socket` de Python. El script:

- escucha conexiones TCP en `127.0.0.1:9999`;
- registra fecha y hora, IP y puerto de origen, e IP y puerto destino;
- guarda los eventos en `C:\HoneypotLab\honeypot.log`;
- acepta y cierra cada conexión después de registrar sus extremos;
- utiliza la biblioteca estándar de Python 3.

!!! info "Alcance del sensor"
    La práctica principal se concentra en **detección y registro de conexiones TCP**. Al terminarla se propone una ampliación opcional que emula de forma limitada SSH, HTTP y FTP, siempre en localhost.

---

## Preparación

### Paso 1 — Verificar o instalar Python 3

Abrir PowerShell y ejecutar:

```powershell
py -3 --version
```

Si aparece `Python 3.x.x`, Python está listo y se puede continuar con el Paso 2. Si el comando falla, usar esta ruta principal en PowerShell:

1. Instalar el **Python Install Manager** desde el servidor oficial de Python:

   ```powershell
   Add-AppxPackage -AppInstallerFile https://www.python.org/ftp/python/pymanager/pymanager.appinstaller
   ```

   El comando puede terminar sin mostrar un mensaje. Si no aparece texto rojo ni una excepción, continuar. Esta ruta evita depender de una sesión válida de Microsoft Store y está publicada en la [documentación oficial de Python para Windows](https://docs.python.org/3/using/windows.html#advanced-installation).

2. Instalar explícitamente el runtime estable predeterminado:

   ```powershell
   py install default
   ```

   El administrador comprobará su propia versión, verificará la firma del índice oficial y descargará Python. Los números de versión mostrados pueden cambiar.

3. Comprobar el resultado:

   ```powershell
   py -3 --version
   ```

   El criterio de éxito es que la consola muestre `Python 3.x.x`. Si `py` todavía no se reconoce después del primer comando, cerrar PowerShell, abrir una ventana nueva y repetir los pasos 2 y 3.

!!! note "Alternativa mediante WinGet"
    Si Microsoft Store funciona en la sesión interactiva del alumno, también se puede instalar el administrador con:

    ```powershell
    winget install --id 9NQ7512CXL7T --exact --source msstore --accept-source-agreements --accept-package-agreements --authentication-mode interactive
    ```

    Si WinGet devuelve `0x8a15000f` o `0x80070520`, no reiniciar fuentes ni insistir durante la clase: volver a la ruta principal con `Add-AppxPackage`.

### Paso 2 — Crear el directorio del laboratorio

```powershell
New-Item -ItemType Directory -Force C:\HoneypotLab
```

### Paso 3 — Comprobar que el puerto 9999 está libre

```powershell
Get-NetTCPConnection -LocalPort 9999 -State Listen -ErrorAction SilentlyContinue
```

Si no aparece ninguna fila, no existe un proceso escuchando en ese puerto. Si aparece un proceso, usar otro puerto alto —por ejemplo `10000`— tanto al iniciar el script como al probar la conexión.

---

## Desplegar el honeypot

Guardar el siguiente contenido como `C:\HoneypotLab\honeypot-simple.py`:

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

### ¿Qué hace cada componente?

| Componente | Función |
|---|---|
| `HOST = "127.0.0.1"` | Acepta conexiones únicamente desde el mismo equipo |
| `PORT = 9999` | Usa un puerto alto para evitar conflictos con servicios conocidos |
| `server.accept()` | Espera una conexión TCP y devuelve la dirección del cliente |
| `connection.getsockname()` | Obtiene la IP y el puerto destino que recibió la conexión |
| `honeypot.log` | Conserva evidencia aunque se cierre la consola |

!!! warning "No cambiar el alcance durante la práctica"
    No sustituir `127.0.0.1` por `0.0.0.0`. Ese cambio expondría el sensor a otras interfaces y requeriría autorización, una regla de Windows Firewall y un diseño de contención. La práctica principal ocurre exclusivamente en localhost.

---

## Ejecutar y probar

### Paso 4 — Iniciar el honeypot

En una ventana de PowerShell:

```powershell
py -3 C:\HoneypotLab\honeypot-simple.py 9999
```

Si se usa `python` en lugar del lanzador `py`:

```powershell
python C:\HoneypotLab\honeypot-simple.py 9999
```

La consola debe quedar mostrando:

```text
Honeypot iniciado en 127.0.0.1:9999
Log: C:\HoneypotLab\honeypot.log
Esperando conexiones... (Ctrl+C para detener)
```

### Paso 5 — Generar una conexión controlada

Sin cerrar el honeypot, abrir una **segunda** ventana de PowerShell y ejecutar:

```powershell
Test-NetConnection 127.0.0.1 -Port 9999
```

La salida debe contener:

```text
TcpTestSucceeded : True
```

Al mismo tiempo, la primera ventana mostrará un evento similar a:

```text
[2026-08-24T10:15:01-05:00] EVENTO_TCP origen=127.0.0.1:54321 destino=127.0.0.1:9999
```

El número `54321` es un ejemplo de **puerto de origen efímero**, elegido por Windows para esa conexión. El puerto `9999` es el **puerto destino** donde escucha el honeypot. Que los puertos de origen cambien entre conexiones no demuestra por sí solo un escaneo.

### Paso 6 — Consultar el archivo de log

```powershell
Get-Content C:\HoneypotLab\honeypot.log
```

Repetir `Test-NetConnection` tres veces y comprobar que aparecen tres eventos nuevos. Después, volver a la primera ventana y presionar `Ctrl+C` para detener el sensor.

---

## Ejercicio 2 — Honeypot multservicio (ampliación opcional)

> **Duración adicional:** 45–60 minutos | **Requisito:** haber completado y detenido `honeypot-simple.py` | **Evaluación:** no requerida para el examen

En este ejercicio un solo programa simula tres servicios de baja interacción:

| Servicio simulado | Puerto local | Comportamiento |
|---|---:|---|
| SSH | `8022` | Envía un banner SSH ficticio y cierra la conexión |
| HTTP | `8080` | Registra el método y la ruta, y devuelve una página estática |
| FTP | `2121` | Envía un saludo, registra solo el nombre del comando y rechaza la autenticación |

Los puertos son deliberadamente altos para evitar permisos administrativos y conflictos con servicios reales. Los banners solo **representan** servicios: el programa no implementa servidores SSH, web o FTP completos.

### Paso A — Comprobar los tres puertos

En PowerShell:

```powershell
8022, 8080, 2121 | ForEach-Object {
    Get-NetTCPConnection -LocalPort $_ -State Listen -ErrorAction SilentlyContinue
}
```

El resultado esperado es que no aparezca ninguna fila. Si aparece un proceso en cualquiera de los puertos, no continuar hasta consultarlo con el instructor.

### Paso B — Crear el emulador

Guardar el siguiente contenido como `C:\HoneypotLab\honeypot-multiservicio.py`:

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

El programa limita cada lectura a `1024` bytes y a dos segundos. Para reducir la recopilación de datos, HTTP descarta la consulta situada después de `?`, FTP registra únicamente el nombre del comando y SSH no almacena contenido enviado por el cliente.

### Paso C — Iniciar y confirmar las escuchas

En la primera ventana de PowerShell:

```powershell
py -3 C:\HoneypotLab\honeypot-multiservicio.py
```

La salida debe listar los tres servicios y terminar con `Esperando conexiones...`. Sin cerrar esa ventana, confirmar en una segunda consola:

```powershell
Get-NetTCPConnection -LocalPort 8022,8080,2121 -State Listen |
    Select-Object LocalAddress, LocalPort, State
```

Deben aparecer exactamente tres escuchas en `127.0.0.1`, una por cada puerto.

### Paso D — Consultar el banner SSH simulado

En la segunda consola:

```powershell
$cliente = [System.Net.Sockets.TcpClient]::new("127.0.0.1", 8022)
$cliente.ReceiveTimeout = 3000
$lector = [System.IO.StreamReader]::new($cliente.GetStream())
$lector.ReadLine()
$lector.Dispose()
$cliente.Dispose()
```

Resultado esperado:

```text
SSH-2.0-OpenSSH_8.9p1 LabHoneypot
```

El texto es un **banner ficticio**. Verlo no demuestra que exista un servidor OpenSSH real detrás del puerto.

### Paso E — Solicitar la página HTTP simulada

```powershell
$respuesta = Invoke-WebRequest -Uri http://127.0.0.1:8080/admin -UseBasicParsing
$respuesta.StatusCode
$respuesta.Content
```

El código debe ser `200` y el contenido debe incluir `Portal de administracion`. `-UseBasicParsing` evita el análisis de contenido mediante el motor heredado del navegador.

### Paso F — Interactuar con el FTP simulado

```powershell
$cliente = [System.Net.Sockets.TcpClient]::new("127.0.0.1", 2121)
$cliente.ReceiveTimeout = 3000
$flujo = $cliente.GetStream()
$lector = [System.IO.StreamReader]::new($flujo)
$escritor = [System.IO.StreamWriter]::new($flujo)
$escritor.NewLine = "`r`n"
$escritor.AutoFlush = $true
$lector.ReadLine()
$escritor.WriteLine("USER alumno")
$lector.ReadLine()
$escritor.Dispose()
$lector.Dispose()
$cliente.Dispose()
```

La consola debe mostrar primero `220 FTP de laboratorio listo` y después `530 Autenticacion no disponible`. No introducir una contraseña real: el emulador no la necesita ni debe recopilarla.

### Paso G — Comparar los eventos

```powershell
Get-Content C:\HoneypotLab\honeypot-multiservicio.log
```

El archivo debe contener conexiones a los tres puertos, una petición `GET /admin` y un comando `USER`. Los puertos de origen variarán:

```text
[2026-08-25T10:20:01-05:00] servicio=SSH origen=127.0.0.1:54001 destino=127.0.0.1:8022 evento=CONEXION
[2026-08-25T10:20:20-05:00] servicio=HTTP origen=127.0.0.1:54002 destino=127.0.0.1:8080 evento=CONEXION
[2026-08-25T10:20:20-05:00] servicio=HTTP origen=127.0.0.1:54002 destino=127.0.0.1:8080 evento=PETICION detalle="GET /admin"
[2026-08-25T10:20:40-05:00] servicio=FTP origen=127.0.0.1:54003 destino=127.0.0.1:2121 evento=CONEXION
[2026-08-25T10:20:40-05:00] servicio=FTP origen=127.0.0.1:54003 destino=127.0.0.1:2121 evento=COMANDO detalle="USER"
```

Responder:

1. ¿Qué puerto destino corresponde a cada servicio simulado?
2. ¿Por qué un banner no demuestra por sí solo qué software existe detrás del puerto?
3. ¿Qué datos omite deliberadamente el emulador HTTP y FTP, y por qué?
4. ¿Qué evidencia adicional aporta observar tres **puertos destino** frente a observar solamente cambios en los puertos de origen?

Finalmente, presionar `Ctrl+C` en la primera consola. Los tres puertos deben dejar de aparecer en `Get-NetTCPConnection`.

!!! warning "Límites de la emulación"
    No cambiar `HOST`, no abrir reglas de Windows Firewall y no usar estos banners para suplantar servicios en una red real. El ejercicio no autentica usuarios, no ejecuta comandos y no entrega archivos.

---

## Análisis de logs

El honeypot es un sensor, no un protector. Un evento indica que alguien o algo alcanzó el recurso; el analista debe determinar el contexto antes de atribuir intención.

### Campos del evento

```text
[fecha-hora-zona] EVENTO_TCP origen=IP:puerto_origen destino=IP:puerto_destino
```

| Campo | Pregunta que responde |
|---|---|
| Fecha, hora y zona | ¿Cuándo ocurrió? ¿Puede correlacionarse con otros sistemas? |
| IP de origen | ¿Qué equipo inició la conexión? |
| Puerto de origen | ¿Qué puerto efímero utilizó el cliente? |
| IP y puerto destino | ¿Qué sensor o servicio fue contactado? |

### Ejercicio de análisis

El siguiente extracto representa un sensor autorizado dentro de una red de práctica. El inventario indica que `192.168.1.77` pertenece a una estación interna. Analizar los diez eventos:

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

Responder:

1. ¿Cuántos eventos y cuántas IP de origen distintas aparecen?
2. ¿Cuál es la IP más activa? ¿Qué observación temporal puede hacerse sin atribuir todavía una causa?
3. ¿En qué tres ventanas de tiempo se concentra la actividad?
4. ¿Qué hipótesis deben comprobarse para `192.168.1.77` antes de decidir una contención?

!!! note "Límite de la evidencia"
    Las conexiones repetidas pueden indicar automatización, reintentos de una aplicación, monitoreo autorizado o reconocimiento. Como todos los eventos llegan al mismo puerto destino, este extracto **no demuestra un escaneo de puertos**.

---

## Del evento a la respuesta

Cuando el honeypot registra una conexión:

1. **Validar el sensor:** confirmar que el evento no fue generado por la prueba del instructor ni por monitoreo autorizado.
2. **Resolver la fuente:** consultar DHCP, inventario y asignación de direcciones para identificar el equipo.
3. **Evaluar frecuencia y horario:** distinguir una conexión aislada de una ráfaga o recurrencia.
4. **Correlacionar:** revisar alertas de Snort, logs de firewall, proxy y autenticación en el mismo intervalo.
5. **Decidir:** escalar, observar o aislar el equipo según el conjunto de evidencias y el procedimiento institucional.

Una IP interna no prueba por sí sola movimiento lateral ni insider threat. Es una pista que adquiere significado cuando se combina con identidad del activo, autorización y otros logs.

---

## Validación del laboratorio

- [ ] Python 3 está disponible y el script canónico está guardado
- [ ] El sensor escucha únicamente en `127.0.0.1` y en un puerto alto
- [ ] `Test-NetConnection` devuelve `TcpTestSucceeded : True`
- [ ] La consola y `honeypot.log` registran origen y destino
- [ ] El alumno diferencia puerto de origen y puerto destino
- [ ] El alumno responde el ejercicio sin afirmar más de lo que demuestra la evidencia

Si se realiza la ampliación opcional:

- [ ] Los tres servicios escuchan únicamente en `127.0.0.1`
- [ ] Los banners y la página simulada coinciden con los resultados esperados
- [ ] `honeypot-multiservicio.log` diferencia servicio, conexión, petición y comando
- [ ] El alumno explica por qué un banner es una pista y no una identificación definitiva

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Un sensor de engaño autorizado registra varias conexiones desde una estación interna fuera del horario previsto. El analista no declara inmediatamente que existe un troyano: primero descarta un escaneo de vulnerabilidades programado, identifica el equipo en el inventario y correlaciona el intervalo con Snort, firewall y autenticación. Solo si el conjunto de evidencias indica compromiso aplica el procedimiento de aislamiento. El valor del honeypot es producir una señal temprana y concreta para iniciar esa investigación.

---

## Resumen

- El alumno despliega un honeypot didáctico con la biblioteca estándar de Python 3
- `Test-NetConnection` genera el tráfico TCP de prueba
- El log diferencia explícitamente el origen del destino
- Un evento del honeypot es una alerta de alta confianza, no una atribución automática
- La frecuencia y los puertos de origen no bastan para demostrar un escaneo
- La ampliación multservicio permite comparar banners y actividad dirigida a varios puertos destino
- La respuesta correcta combina inventario, autorización y correlación con otros controles

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- [Cowrie](https://github.com/cowrie/cowrie) — honeypot SSH/Telnet de interacción media en su modo shell; requiere Linux o WSL2
- [T-Pot](https://github.com/telekom-security/tpotce) — plataforma avanzada de múltiples honeypots; fuera del alcance de esta práctica

<!-- Solución disponible para el instructor en: docs/instructor/lab-honeypot-solucion.md -->

---

*Siguiente: [Examen Final de la Asignatura](examen-final.md)*
