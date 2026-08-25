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

---

## Herramienta del laboratorio

Usaremos un honeypot didáctico construido con el módulo `socket` de Python. El script:

- escucha conexiones TCP en `127.0.0.1:9999`;
- registra fecha y hora, IP y puerto de origen, e IP y puerto destino;
- guarda los eventos en `C:\HoneypotLab\honeypot.log`;
- acepta y cierra cada conexión después de registrar sus extremos;
- utiliza la biblioteca estándar de Python 3.

!!! info "Alcance del sensor"
    Esta práctica se concentra en **detección y registro de conexiones TCP**. La emulación de servicios como SSH o HTTP corresponde a una práctica avanzada.

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
- La respuesta correcta combina inventario, autorización y correlación con otros controles

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- [Cowrie](https://github.com/cowrie/cowrie) — honeypot SSH/Telnet de interacción media en su modo shell; requiere Linux o WSL2
- [T-Pot](https://github.com/telekom-security/tpotce) — plataforma avanzada de múltiples honeypots; fuera del alcance de esta práctica

<!-- Solución disponible para el instructor en: docs/instructor/lab-honeypot-solucion.md -->

---

*Siguiente: [Examen Final de la Asignatura](examen-final.md)*
