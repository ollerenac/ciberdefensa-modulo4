---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab Despliegue y Análisis de Honeypot (LAB-05)

!!! danger "Solo para instructores"
    Este documento contiene la configuración esperada, el script de contingencia Python completo, el extracto de log con respuestas modelo y los criterios de calificación. No mostrarlo en el proyector durante el lab.

!!! warning "Contingencia"
    Este lab tiene mayor probabilidad de fallo de instalación que los otros labs de la unidad. Leer la sección **"## Plan B: Si HoneyPy No Instala"** antes del lab. Tener el script Python listo en USB para distribución antes de entrar al aula.

---

## Prerrequisito de Instalación — Verificar ANTES del lab

1. **Verificar que Python 3 está instalado** en los equipos del aula:
   ```cmd
   python --version
   ```
   o en algunos sistemas:
   ```cmd
   python3 --version
   ```
   Debe mostrar `Python 3.x.x`. Si no está instalado, descargar el instalador desde [python.org](https://python.org) (Windows installer 64-bit, versión estable más reciente).

2. **Intentar instalar HoneyPy en el equipo del instructor ANTES del lab:**
   ```cmd
   pip install honeypy
   ```
   Si falla (error de conexión, red corporativa que bloquea PyPI, o proxy que interfiere con pip), cambiar directamente al Plan B — script Python en USB. No perder tiempo de clase en resolver problemas de pip.

3. **Preparar el script `honeypot-simple.py`** en USB para distribución (el código está en la sección Plan B de este documento). Tenerlo listo aunque HoneyPy funcione — es el fallback garantizado.

4. **Verificar que los puertos 22 y 80 no están en uso** en los equipos del aula:
   ```cmd
   netstat -an | findstr ":22 "
   netstat -an | findstr ":80 "
   ```
   Si alguno está ocupado, el Plan A (HoneyPy en esos puertos) no funcionará. Ajustar la configuración de HoneyPy a puertos alternativos (2222 y 8080), o usar directamente el script Python con puerto 9999.

---

## Plan A: HoneyPy Funciona

Si `pip install honeypy` tuvo éxito, usar este flujo.

**Archivo de configuración `honeypy.cfg` esperado:**

```ini
[honeypy]
logdir = C:\HoneyPy\logs

[services]
ssh = true
ssh_port = 22
http = true
http_port = 80
```

Si los puertos 22 y 80 están en uso, cambiar a:
```ini
ssh_port = 2222
http_port = 8080
```

**Salida esperada de consola al iniciar HoneyPy:**
```text
HoneyPy starting...
Listening on port 22 (SSH emulation)
Listening on port 80 (HTTP emulation)
```

**Comandos de prueba para generar conexiones:**
```cmd
:: Probar SSH (con Telnet Client instalado)
telnet localhost 22

:: Probar HTTP
curl http://localhost
```

**Log esperado después de las conexiones de prueba** (ubicado en `C:\HoneyPy\logs\`):
```text
2026-06-16T14:30:00.123 | 127.0.0.1 | 54321 | ssh | connection
2026-06-16T14:30:05.456 | 127.0.0.1 | 54322 | http | connection
```

---

## Plan B: Si HoneyPy No Instala

Esta sección es la guía de contingencia si `pip install honeypy` falla o HoneyPy no inicia correctamente.

### Opción B1 — Script Python básico (distribuir en USB)

Guardar el siguiente script como `C:\honeypot-simple.py` en los equipos de los alumnos. Distribuir desde USB.

```python
# Honeypot minimo de demostracion - detecta y registra conexiones TCP
import socket
import datetime
import sys

HOST = '127.0.0.1'
PORT = int(sys.argv[1]) if len(sys.argv) > 1 else 9999

print(f"Honeypot iniciado en {HOST}:{PORT}")
print("Esperando conexiones... (Ctrl+C para detener)")
print("-" * 50)

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    s.bind((HOST, PORT))
    s.listen(5)
    while True:
        try:
            conn, addr = s.accept()
            timestamp = datetime.datetime.now().isoformat()
            log_entry = f"[{timestamp}] CONEXION desde {addr[0]}:{addr[1]}"
            print(log_entry)
            # Guardar en archivo de log
            with open("honeypot.log", "a") as f:
                f.write(log_entry + "\n")
            conn.close()
        except KeyboardInterrupt:
            print("\nHoneypot detenido.")
            break
```

**Ejecutar el honeypot:**
```cmd
python C:\honeypot-simple.py 9999
```

**Probar la conexión** (desde otra ventana CMD):
```cmd
telnet 127.0.0.1 9999
```

**Salida esperada en la consola del honeypot:**
```text
Honeypot iniciado en 127.0.0.1:9999
Esperando conexiones... (Ctrl+C para detener)
--------------------------------------------------
[2026-06-16T14:30:00.123456] CONEXION desde 127.0.0.1:54321
[2026-06-16T14:30:15.789012] CONEXION desde 127.0.0.1:54325
```

!!! note "Nota de seguridad T-04-02"
    El script usa `HOST = '127.0.0.1'` (solo loopback) para evitar requerir permisos de administrador (puertos < 1024 en Windows requieren elevación). Si un alumno cambia `HOST` a `'0.0.0.0'`, el honeypot aceptará conexiones de toda la red del aula — alertar sobre esto como lección de seguridad: un honeypot expuesto en red real requiere autorización explícita.

### Opción B2 — Cowrie en WSL2 (solo si el aula tiene WSL2 con Ubuntu disponible)

Cowrie es un honeypot SSH de alta interacción que registra todos los comandos que el "atacante" escribe. Es la opción más impactante pedagógicamente, pero requiere WSL2 y aproximadamente 20 minutos de instalación.

```bash
# En Ubuntu WSL2 (abrir desde el menu Inicio: Ubuntu)
sudo apt-get install -y git python3-venv

git clone https://github.com/cowrie/cowrie.git
cd cowrie

python3 -m venv cowrie-env
source cowrie-env/bin/activate
pip install -r requirements/requirements.txt

cp etc/cowrie.cfg.dist etc/cowrie.cfg
# Editar cowrie.cfg: cambiar listen_port a 2222 (no requiere permisos de root)
# Linea a modificar: listen_port = 2222

bin/cowrie start
```

**Probar desde otra terminal WSL2:**
```bash
ssh -p 2222 root@localhost
# Cowrie simula un shell. Escribir: ls, cat /etc/passwd, whoami
# Todos los comandos quedan registrados en cowrie/var/log/cowrie/cowrie.log
```

**Tiempo de instalación:** 15-20 minutos. Solo viable si el grupo va adelantado y WSL2 ya está disponible en los equipos del aula.

---

## Extracto de Log para el Ejercicio de Análisis

El instructor distribuye este extracto impreso o lo proyecta para el ejercicio de análisis en clase. No depende de que el honeypot funcione — el análisis puede hacerse con este extracto independientemente.

```text
[2026-06-16T10:15:01] CONEXION desde 192.168.1.100:1024
[2026-06-16T10:15:45] CONEXION desde 192.168.1.100:1025
[2026-06-16T10:16:02] CONEXION desde 192.168.1.100:1026
[2026-06-16T10:16:03] CONEXION desde 192.168.1.100:1027
[2026-06-16T10:16:04] CONEXION desde 192.168.1.100:1028
[2026-06-16T10:45:22] CONEXION desde 10.0.0.5:2048
[2026-06-16T10:45:23] CONEXION desde 10.0.0.5:2049
[2026-06-16T11:02:15] CONEXION desde 192.168.1.77:3000
[2026-06-16T11:02:18] CONEXION desde 192.168.1.77:3001
[2026-06-16T11:02:20] CONEXION desde 192.168.1.77:3002
```

### Respuestas Modelo del Ejercicio de Análisis

**Pregunta 1 — ¿Cuántas conexiones únicas aparecen en el log? ¿Cuántas IPs distintas?**

Respuesta correcta: 10 conexiones totales. 3 IPs distintas: `192.168.1.100`, `10.0.0.5`, `192.168.1.77`.

**Pregunta 2 — ¿Qué IP es la más activa? ¿Qué patrón de comportamiento sugiere?**

Respuesta correcta: `192.168.1.100` con 5 conexiones en menos de 1 minuto (desde las 10:15:01 hasta las 10:16:04). El patrón de puertos incrementales (1024, 1025, 1026...) sugiere un **escaneo de puertos automatizado** — una herramienta que intenta conectar a puertos secuencialmente.

**Pregunta 3 — ¿Hay patrones en los momentos de tiempo? ¿Qué sugieren?**

Respuesta correcta: Hay dos ráfagas de actividad separadas por un gap de casi 45 minutos (10:15-10:16 y luego 11:02). El gap puede indicar un atacante que pausó la actividad (para evitar detección, o porque estaba ocupado en otra tarea). La tercera IP (`10.0.0.5`) aparece brevemente en el medio — puede ser un actor diferente o el mismo atacante usando otra máquina.

**Pregunta 4 — Una de las IPs pertenece al rango interno de la red (192.168.1.x). ¿Qué implica esto?**

Respuesta correcta: La presencia de `192.168.1.77` (dirección de red interna) en el log del honeypot es un **indicador de movimiento lateral** — un equipo de la misma red está explorando recursos internos. Esto puede indicar: (a) un equipo comprometido que ejecuta código malicioso, (b) un insider threat (alguien con acceso legítimo realizando reconocimiento no autorizado). Acción recomendada: aislar `192.168.1.77` de la red y analizar sus procesos activos.

---

## Errores Comunes y Cómo Manejarlos

| Error | Causa probable | Solución |
|-------|---------------|----------|
| `pip install honeypy` falla con "connection error" o "timeout" | Red corporativa bloquea PyPI o hay proxy que interfiere con pip | Pasar directamente al Plan B — distribuir el script `honeypot-simple.py` desde USB |
| HoneyPy no inicia: `Address already in use` | Puerto 22 o 80 ya está ocupado (IIS, servidor SSH, u otro servicio) | `netstat -an \| findstr ":22 "` para identificar el conflicto, o cambiar `ssh_port = 2222` y `http_port = 8080` en `honeypy.cfg` |
| Script Python falla: `[WinError 10013] An attempt was made to access a socket in a way forbidden by its access permissions` | Binding en puerto < 1024 requiere permisos de administrador en Windows | Ejecutar CMD como Administrador, o cambiar el puerto a 9999 (el script usa 9999 por defecto) |
| `telnet localhost 9999` falla: `'telnet' is not recognized as an internal or external command` | Telnet Client no está instalado en Windows 11 (viene desactivado por defecto) | Panel de control → Programas → Activar características de Windows → Telnet Client → OK |
| El log `honeypot.log` está vacío después de conectar | La conexión fue a `127.0.0.1` y el script tiene `HOST = '0.0.0.0'`, o viceversa — verificar que coincidan | Verificar el `HOST` en el script. Si el script dice `'127.0.0.1'`, conectar con `telnet 127.0.0.1 9999` (no `localhost` si resolución DNS difiere) |

---

## Notas de Dictado

### Timing sugerido (total: ~90 minutos de lab)

**Instalación / fallback (15 min):** Intentar `pip install honeypy` en los primeros equipos. Si hay 2 o más fallos en el grupo, cambiar al script Python inmediatamente para no perder tiempo de clase en debugging de pip. El script Python produce el mismo aprendizaje con menos fricción.

**Configuración y primer inicio (10 min):** Verificar que todos tienen el honeypot corriendo antes de continuar. La señal de éxito: el honeypot imprime el mensaje de "Esperando conexiones..." sin error. Hacer que todos abran una segunda ventana CMD para generar el tráfico de prueba.

**Generación de tráfico de prueba (15 min):** El momento más visual del lab — ver las entradas aparecer en tiempo real en la consola del honeypot mientras otro alumno se "conecta". Organizar si hay red local entre equipos: el instructor conecta desde el proyector al honeypot de un alumno para simular un "atacante externo" (esto es pedagógicamente más impactante que conectarse desde localhost).

**Ejercicio de análisis del log (25 min):** Distribuir el extracto de log impreso (o proyectar). Dar 10 minutos para análisis individual escribiendo las respuestas. Luego 15 minutos de discusión en grupo. Esta es la sección más rica pedagógicamente del lab. Enfatizar la Pregunta 4 (IP interna) — el concepto de movimiento lateral es clave en ciberdefensa.

**Cowrie opcional (15 min):** Solo para grupos avanzados o si terminaron antes. Cowrie registra los comandos que el "atacante" escribe en el shell simulado — el impacto de ver cómo registra `ls`, `cat /etc/passwd`, `whoami` es memorable y demuestra la diferencia entre un honeypot de baja interacción (solo detecta conexiones) y uno de alta interacción (registra comportamiento).

### Mensaje de cierre del lab

Recordar que el honeypot es un **detector, no un protector**. Cuando registra una conexión, el trabajo del analista comienza — no termina. Conectar con la Pregunta 4 del ejercicio: una IP interna en el log del honeypot no es el final de la investigación, es el punto de partida.

---

## Rúbrica de Calificación

| Criterio | Puntos | Cómo verificar |
|----------|--------|----------------|
| Honeypot inicia sin error (HoneyPy o script Python) | 2 | El alumno muestra la consola del honeypot corriendo con el mensaje de inicio |
| Al conectar con telnet o curl, aparece entrada en el log | 2 | Captura de pantalla de la consola con al menos 1 entrada de conexión |
| Log muestra timestamp, IP de origen y puerto | 1 | Revisión de la consola o del archivo `honeypot.log` |
| Ejercicio de análisis Q1: conteo de conexiones e IPs | 1 | Respuesta correcta: 10 conexiones, 3 IPs distintas |
| Ejercicio de análisis Q2: IP más activa con justificación | 1 | `192.168.1.100` con explicación del patrón de puertos incrementales |
| Ejercicio de análisis Q3: patrones de tiempo | 1 | Identificación de 2 ráfagas y el gap de aproximadamente 45 minutos entre ellas |
| Ejercicio de análisis Q4: IP interna sospechosa y acción recomendada | 2 | Identificación de movimiento lateral + recomendación de acción concreta (aislar equipo, analizar procesos) |

**Total: 10 puntos**

---

<!-- Solución instructor para: docs/seguridad-criptografia/en-la-red/honeypots-p2.md -->
