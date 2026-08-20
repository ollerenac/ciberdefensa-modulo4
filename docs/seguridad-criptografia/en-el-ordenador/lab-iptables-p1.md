---
# Horas asignadas: 1 hr
# Tipo: Laboratorio (versión alumno)
---

# Lab: Filtrado de Paquetes con iptables en Ubuntu (Parte 1)

> **Duración:** 1 hora | **Unidad:** En el Ordenador

!!! warning "Instrucciones generales"
    - Trabajar de forma individual salvo indicación del instructor.
    - Documentar cada paso con capturas de pantalla en su cuaderno.
    - Si un paso falla, **no avanzar** — notificar al instructor.

## Objetivos

Al finalizar este laboratorio, el alumno habrá:

- Preparado y verificado una VM Ubuntu 24.04 con red NAT + Host-only
- Entendido el modelo de tablas y cadenas de iptables con un diagrama visual
- Capturado el ruleset inicial y aplicado un reset seguro antes de trabajar
- Aplicado una política por defecto DROP en INPUT con reglas selectivas de permiso
- Probado cada regla inbound con un servicio real escuchando y una conexión desde Windows
- Demostrado con contadores que el **orden** de las reglas decide el resultado
- Restaurado el ruleset original al terminar

## Preparación previa (antes de la clase)

Esta sección se completa **una sola vez antes del laboratorio**, sobre una VM Ubuntu
24.04 recién instalada en VirtualBox con dos adaptadores de red:

| Adaptador | Modo | Propósito |
|-----------|------|-----------|
| Adaptador 1 | NAT | Salida a internet (actualizaciones, APT) |
| Adaptador 2 | Host-only | Red de laboratorio con el host Windows |

**[VM Ubuntu — Bash]**

```bash
# 1. Inspeccionar y respaldar las fuentes APT (formato deb822 de Ubuntu 24.04)
cat /etc/apt/sources.list.d/ubuntu.sources
sudo cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.bak

# 2. Actualizar el sistema
sudo apt update && sudo apt full-upgrade -y

# 3. Instalar los paquetes del laboratorio
#    (python3 ya viene incluido en Ubuntu 24.04; curl y bash-completion se
#    instalan explícitamente por si la instalación fue mínima)
sudo apt install -y openssh-server netfilter-persistent tcpdump curl bash-completion

# 4. Verificaciones
iptables --version          # se espera: iptables v1.8.x (nf_tables)
sudo ss -lntp | grep :22    # sshd debe estar escuchando
curl -4 -I http://example.com   # debe devolver una respuesta HTTP (exit code 0)

# 5. Dependencias para las Guest Additions (el instalador compila un driver)
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

**6. Guest Additions (integración VM ↔ anfitrión).** Instalarlas desde el CD
virtual de VirtualBox — así la versión coincide exactamente con la del VirtualBox
del host:

1. En la **ventana de VirtualBox** de la VM: menú **Dispositivos → Insertar
   imagen de CD de las «Guest Additions»...** (*Devices → Insert Guest Additions
   CD image...*).
2. El CD aparece montado en `/media/$USER/VBox_GAs_<versión>`. En la terminal
   de la VM:

**[VM Ubuntu — Bash]**

```bash
# Ejecutar el instalador del CD
sudo sh /media/$USER/VBox_GAs_*/VBoxLinuxAdditions.run

# Si el CD no se montó solo:
#   sudo mount /dev/cdrom /mnt && sudo sh /mnt/VBoxLinuxAdditions.run

# Apagar (no reiniciar): con la VM apagada se ajusta su configuración en VirtualBox
sudo poweroff
```

**7. Con la VM apagada**, en el **VirtualBox Manager** seleccionar la VM →
**Configuración → Pantalla** (*Settings → Display*) y subir **Memoria de vídeo**
(*Video Memory*) a **128 MB** (el valor por defecto puede quedarse corto para el
escritorio). Verificar también en **Configuración → Red** que el Adaptador 2 esté
en modo **Solo-anfitrión** (*Host-only*). Luego **encender la VM**.

!!! warning "No usar los paquetes virtualbox-guest-* de APT"
    Los paquetes `virtualbox-guest-utils` / `virtualbox-guest-x11` de los
    repositorios de Ubuntu suelen ser de una versión más antigua que el
    VirtualBox del host, y con esa mezcla el redimensionado de pantalla y el
    portapapeles compartido no funcionan. Si ya se instalaron, retirarlos antes
    de usar el CD: `sudo apt remove -y virtualbox-guest-utils virtualbox-guest-x11`.

Ya con la VM encendida de nuevo, en la **ventana de VirtualBox** de la VM:

- **Dispositivos → Portapapeles compartido → Bidireccional** (*Devices → Shared
  Clipboard → Bidirectional*) — copiar y pegar texto entre host y VM en ambos
  sentidos.
- **Ver → Ajustar automáticamente el tamaño de pantalla del invitado** (*View →
  Auto-resize Guest Display*) debe estar marcado — el escritorio de la VM sigue
  la forma de la ventana al redimensionarla.

!!! tip "Si copiar/pegar o el ajuste de pantalla no funcionan"
    Ubuntu 24.04 inicia sesión con Wayland por defecto y ahí el portapapeles
    compartido y el redimensionado pueden fallar. Solución: cerrar sesión, y en
    la pantalla de inicio de sesión pulsar el **engranaje** (abajo a la derecha)
    y elegir **"Ubuntu on Xorg"** antes de entrar. Probar de nuevo.

!!! note "No modificar las fuentes APT"
    Ubuntu 24.04 usa el formato deb822 en `/etc/apt/sources.list.d/ubuntu.sources`.
    El respaldo es una precaución: **no** sobrescribir el archivo legado
    `/etc/apt/sources.list` ni añadir repositorios de terceros. Los paquetes del lab
    vienen todos de los repositorios oficiales.

    Dentro del repositorio oficial, los paquetes se agrupan en cuatro componentes
    según licencia y quién los mantiene:

    | Componente | Licencia | Mantenimiento |
    |------------|----------|---------------|
    | `main` | libre | Canonical (garantiza parches) |
    | `universe` | libre | comunidad |
    | `restricted` | propietario | Canonical (drivers NVIDIA, firmware) |
    | `multiverse` | propietario | comunidad |

    Todos los paquetes de este lab provienen de `main`.

!!! note "¿Qué es iptables?"
    `iptables` es el firewall de Linux: el programa con el que se decide qué
    conexiones de red pueden entrar o salir de la máquina. Se le dan **reglas** —
    "lo que venga a este puerto, déjalo pasar; todo lo demás, descártalo" — y el
    sistema las revisa en orden con cada paquete que llega. Cumple el mismo papel
    que el Firewall de Windows visto en el laboratorio anterior, pero se maneja
    por completo desde la terminal.

    Al verificar la versión aparecerá `(nf_tables)`: es el nombre del motor
    interno que iptables usa por debajo en Ubuntu 24.04. No afecta al laboratorio
    — aquí se usan únicamente comandos `iptables`.

!!! tip "Autocompletado con Tab (bash-completion)"
    Con `bash-completion` instalado, la tecla **Tab** completa comandos, rutas y
    subcomandos: escribir `sudo netf` + Tab completa a `netfilter-persistent`;
    `systemctl sta` + Tab ofrece `start`/`status`. **Doble Tab** lista todas las
    opciones posibles. No requiere configuración: Ubuntu lo activa solo al abrir
    una terminal nueva. Verificar con `type _init_completion` — debe responder
    "es una función" (si no, cerrar y abrir la terminal).

!!! warning "netfilter-persistent al instalar"
    Si el instalador pregunta si desea guardar las reglas actuales (IPv4/IPv6),
    responder **Sí**. Este paquete se usará en la Parte 2.

Al terminar: apagar la VM y tomar un **snapshot de VirtualBox** llamado `base-limpia`.
Ese snapshot es el punto de partida garantizado de ambas partes del laboratorio.

## Entorno de laboratorio

```
Host Windows 11 (VirtualBox)
├── Adaptador NAT       → VM: internet (APT, curl)
└── Adaptador Host-only → VM: red de laboratorio
        Host Windows ←──────→ VM Ubuntu 24.04
        (segundo extremo         (iptables/Netfilter
         de prueba)               en el kernel Linux)
```

!!! note ""
    Las reglas iptables que configuramos operan sobre Netfilter, el firewall del
    kernel **de la VM Ubuntu**. No afectan el firewall de Windows ni el tráfico del
    equipo anfitrión. El host Windows participa solo como cliente de prueba a través
    de la red Host-only.

!!! warning "Alcance: solo IPv4"
    `iptables` administra reglas **IPv4**. El filtrado IPv6 requiere `ip6tables` (o
    nftables en familia `inet`) y queda fuera de este laboratorio. Por eso todas las
    pruebas fuerzan IPv4 con la opción `-4`. Una política DROP en iptables **no**
    protege el tráfico IPv6.

## Conceptos Clave: Tablas y Cadenas

Antes de ejecutar cualquier comando, es importante entender el modelo mental de iptables.
El tráfico de red pasa por **cadenas** (chains), y en cada cadena hay reglas que deciden
qué hacer con cada paquete.

| Cadena | Descripción | Uso típico en este lab |
|--------|-------------|------------------------|
| INPUT | Tráfico destinado a este equipo | Controlar quién puede conectarse |
| OUTPUT | Tráfico originado en este equipo | Controlar qué puede salir |
| FORWARD | Tráfico que pasa por el equipo como router | No usado en este lab |

**Flujo de paquetes (simplificado):**

```
Red → [INPUT] → Proceso local
                     │
                     ▼
Red ← [OUTPUT] ← Proceso local
```

!!! note "Tabla filter"
    En este lab usamos solo la tabla `filter` (la predeterminada de iptables). iptables
    tiene también las tablas `nat` y `mangle` — las mencionamos brevemente en la Parte 2.
    Cuando ejecutamos `iptables` sin especificar `-t tabla`, siempre operamos sobre `filter`.

## Pasos del Laboratorio

### Parte 1: Identificar el entorno de red

**Paso 1 — Identificar interfaces y direcciones**

**[VM Ubuntu — Bash]**

```bash
# Ver las interfaces IPv4 de la VM (una por adaptador + loopback)
ip -4 addr
```

Identificar cuál interfaz pertenece a la red **Host-only** (comparte subred con el
host Windows; en VirtualBox suele ser la segunda, con un nombre como `enp0s8`, y una
IP tipo `192.168.56.x` — el nombre y el rango reales pueden variar). Definir las
variables del lab:

```bash
# Sustituir enp0s8 por el nombre real de la interfaz Host-only
LAB_IF=enp0s8
LAB_IP=$(ip -4 addr show "$LAB_IF" | awk '/inet /{print $2}' | cut -d/ -f1)
echo "Interfaz: $LAB_IF — IP de la VM: $LAB_IP"
```

**[Windows — PowerShell]**

```powershell
# IP del host Windows en la red Host-only (adaptador "VirtualBox Host-Only Network")
ipconfig
# Anotar esa IP como WIN_IP. Verificar conectividad hacia la VM:
ping <IP-de-la-VM>
```

!!! question "Verificación"
    ¿El ping desde Windows hacia la IP Host-only de la VM responde? Sin esta
    conectividad base, las pruebas inbound de los pasos siguientes no funcionan.
    Anotar en el cuaderno: nombre de interfaz, IP de la VM, IP del host Windows.

### Parte 2: Estado inicial y reset seguro

**Paso 2 — Capturar el ruleset inicial (OBLIGATORIO)**

Antes de tocar cualquier regla, guardar el estado actual para restaurarlo al final:

**[VM Ubuntu — Bash]**

```bash
# Exportar el ruleset actual a un archivo
sudo iptables-save | sudo tee ~/ruleset-inicial.txt
# Ver el estado actual con detalles
sudo iptables -L -v -n
```

!!! note "Por qué capturar primero"
    Una máquina real no siempre arranca con el firewall vacío. Trabajar sin snapshot
    significa no poder volver al estado anterior. Regla profesional: **capturar antes
    de modificar** — igual que un backup antes de un cambio en producción.

**Paso 3 — Reset seguro: políticas primero, flush después**

**[VM Ubuntu — Bash]**

```bash
# 1. PRIMERO asegurar políticas ACCEPT en las cadenas integradas
sudo iptables -P INPUT ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
# 2. DESPUÉS eliminar reglas, cadenas personalizadas y contadores
sudo iptables -F
sudo iptables -X
sudo iptables -Z
# Confirmar estado limpio
sudo iptables -L -v -n
```

!!! warning "El orden del reset importa"
    `iptables -F` elimina reglas pero **no** cambia las políticas. Si la política
    INPUT ya fuera DROP y se hiciera flush primero, desaparecerían las reglas ACCEPT
    y todo el tráfico entrante (incluida una sesión SSH) quedaría cortado.
    Por eso: **políticas en ACCEPT primero, flush después.**

!!! question "Verificación"
    Las tres cadenas deben mostrar política `ACCEPT`, sin reglas, y contadores en 0.

### Parte 3: Construir la política DROP

**Paso 4 — Agregar regla de loopback primero (OBLIGATORIO)**

**[VM Ubuntu — Bash]**

```bash
# Permitir todo el tráfico en la interfaz loopback (lo)
# Sin esta regla, muchos procesos locales dejan de funcionar
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
```

!!! warning "Atención: el orden importa"
    Si cambias la política a DROP sin agregar esta regla primero, comandos como
    `ping localhost` y procesos internos del sistema que usan la interfaz loopback
    dejarán de funcionar. **Siempre agregar la regla de loopback antes del DROP.**

**Paso 5 — Permitir conexiones establecidas**

**[VM Ubuntu — Bash]**

```bash
# Permitir tráfico de respuesta a conexiones que el equipo inició
# Sin esta regla, curl no puede recibir la respuesta del servidor
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```

!!! note "Filtrado stateful (seguimiento de conexiones)"
    Esta regla usa el módulo `conntrack`. Le dice a iptables: "si este paquete es
    parte de una conexión que yo inicié, déjalo pasar." Es la base del filtrado
    stateful — el kernel recuerda las conexiones activas y permite sus respuestas
    aunque la política general sea DROP.
    (El módulo antiguo `state` sigue existiendo, pero es un subconjunto de
    `conntrack`; usamos la forma moderna.)

**Paso 6 — Permitir SSH y probarlo de verdad**

Una regla inbound solo está probada cuando hay **un servicio escuchando** y **una
conexión desde otro equipo**. sshd ya está instalado desde la preparación previa:

**[VM Ubuntu — Bash]**

```bash
# Confirmar que sshd escucha en el puerto 22
sudo ss -lntp | grep :22
# Permitir SSH entrante
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

!!! note "Leer la salida de `ss` — el equivalente Linux de `netstat -ano`"
    En la VM Windows asociamos cada conexión de `netstat -ano` con su proceso en
    dos pasos: anotar el PID y buscarlo con `tasklist`. En Linux, `ss` hace ambas
    cosas en un solo comando. Cada flag:

    | Flag | Significado |
    |------|-------------|
    | `-l` | Solo sockets en escucha (**l**istening) — servicios esperando conexiones |
    | `-n` | Mostrar **n**úmeros: puerto `22`, no el nombre `ssh` |
    | `-t` | Solo **T**CP |
    | `-p` | Mostrar el **p**roceso dueño de cada socket (requiere `sudo`) |

    Salida típica:

    ```
    LISTEN  0  128  0.0.0.0:22  0.0.0.0:*  users:(("sshd",pid=812,fd=3))
    ```

    La parte `users:(("sshd",pid=812,fd=3))` responde de inmediato la pregunta que
    en Windows requería dos comandos: **¿qué proceso está detrás de este puerto?**
    Aquí es `sshd` con PID 812. Para ver más detalle de ese proceso:
    `ps -p 812 -o pid,user,cmd`. Sin `sudo`, la columna del proceso aparece vacía
    para servicios del sistema — por eso el comando lleva `sudo`.

    Para ver también las conexiones **establecidas** (como ESTABLISHED en
    netstat), quitar la `-l`: `sudo ss -ntp`.

**[Windows — PowerShell]**

```powershell
# Probar la conexión SSH desde el host Windows (sustituir usuario e IP de la VM)
ssh usuario@<IP-de-la-VM>
# Alternativa sin sesión interactiva:
Test-NetConnection <IP-de-la-VM> -Port 22
```

!!! question "Verificación"
    `Test-NetConnection` debe mostrar `TcpTestSucceeded : True`. En la VM,
    `sudo iptables -L INPUT -v -n` debe mostrar `pkts > 0` en la regla tcp:22 —
    el contador es la evidencia de que **esa** regla capturó la conexión.

**Paso 7 — Permitir HTTP y probarlo de verdad**

**[VM Ubuntu — Bash]**

```bash
# Levantar un servidor HTTP real en el puerto 80 (dejarlo corriendo en esta terminal)
sudo python3 -m http.server 80
```

En una **segunda terminal** de la VM:

```bash
# Confirmar que escucha (¿qué proceso y PID aparecen esta vez?)
sudo ss -lntp | grep :80
# Permitir HTTP entrante
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

**[Windows — PowerShell]**

```powershell
# Probar desde el host Windows (curl.exe, no el alias de PowerShell)
curl.exe -4 -I http://<IP-de-la-VM>/
# Alternativa:
Test-NetConnection <IP-de-la-VM> -Port 80
```

!!! question "Verificación"
    La respuesta debe ser un encabezado HTTP válido del servidor de Python
    (`HTTP/1.0 200 OK` u otro código válido). El contador de la regla tcp:80 debe
    incrementarse. Si se prefiere no usar sudo para el listener, puede usarse el
    puerto 8080 — ajustando la regla y la prueba en ambos lados.

**Paso 8 — Aplicar política por defecto DROP**

**[VM Ubuntu — Bash]**

```bash
# CAMBIAR política por defecto: bloquear todo lo que no tenga regla explícita
sudo iptables -P INPUT DROP
# OUTPUT queda en ACCEPT (podemos salir a cualquier destino)
sudo iptables -P OUTPUT ACCEPT
```

!!! warning "Principio de mínimo privilegio aplicado a la red"
    Este es el momento clave del laboratorio. A partir de ahora, **nada entra** a menos
    que lo hayamos permitido explícitamente con una regla. Todo lo que no coincida con
    una regla de ACCEPT cae a la política DROP y es silenciosamente descartado.

Prueba negativa observable:

**[Windows — PowerShell]**

```powershell
# Un puerto sin regla ACCEPT debe fallar ahora
Test-NetConnection <IP-de-la-VM> -Port 9999
```

!!! question "Verificación"
    `TcpTestSucceeded : False`. Mientras tanto, SSH (22) y HTTP (80) deben seguir
    funcionando — repetir sus pruebas para confirmarlo.

### Parte 4: Leer, demostrar el orden y restaurar

**Paso 9 — Leer el ruleset completo**

**[VM Ubuntu — Bash]**

```bash
# Ver todas las reglas con estadísticas de paquetes y bytes
sudo iptables -L -v -n --line-numbers
```

Anotar en el cuaderno el significado de cada columna:

| Columna | Significado |
|---------|-------------|
| pkts | Número de paquetes que coincidieron con esta regla |
| bytes | Bytes totales de los paquetes que coincidieron |
| target | Acción a tomar (ACCEPT, DROP, LOG, REJECT) |
| prot | Protocolo (tcp, udp, icmp, all) |
| in/out | Interfaz de entrada/salida (enp0s8, lo, etc.) |
| source | IP origen del paquete (0.0.0.0/0 = cualquiera) |
| destination | IP destino del paquete |

**Paso 10 — Demostrar que el orden decide: bloqueo con `-I`**

Vamos a bloquear las respuestas de `8.8.8.8`. La posición de la regla es lo que
importa: si se agregara con `-A` (al final), la respuesta al ping — que pertenece a
una conexión ya rastreada — coincidiría **antes** con la regla ESTABLISHED y el
bloqueo no tendría efecto. Por eso se inserta **al inicio** con `-I INPUT 1`:

**[VM Ubuntu — Bash]**

```bash
# Insertar el bloqueo en la posición 1 (antes que loopback y ESTABLISHED)
sudo iptables -I INPUT 1 -s 8.8.8.8 -j DROP
# Probar: el ping debe fallar (forzamos IPv4)
ping -4 -c 3 8.8.8.8
# La evidencia: el contador de la regla 1 debe incrementarse
sudo iptables -L INPUT -v -n --line-numbers
```

!!! question "Verificación"
    - El ping muestra `100% packet loss`.
    - La regla `num 1` (DROP para 8.8.8.8) muestra `pkts` ≥ 3 — cada respuesta
      descartada suma al contador.
    - Reflexión para el cuaderno: ¿qué pasaría si la misma regla se agregara con
      `-A` al final mientras hay un ping activo? ¿Qué regla capturaría los paquetes?

```bash
# Eliminar la regla de prueba al terminar
sudo iptables -D INPUT -s 8.8.8.8 -j DROP
```

**Paso 11 — Restaurar el ruleset original (OBLIGATORIO)**

Cerrar el servidor HTTP de Python (Ctrl+C en su terminal) y devolver el firewall a
su estado inicial:

**[VM Ubuntu — Bash]**

```bash
# Restaurar el snapshot capturado en el Paso 2
sudo iptables-restore < ~/ruleset-inicial.txt
# Verificar que coincide con el estado inicial
sudo iptables -L -v -n
```

!!! note "Ciclo completo"
    Capturar → trabajar → restaurar. Ningún laboratorio debe dejar el sistema en un
    estado distinto al que tenía al empezar. La Parte 2 repite este mismo patrón.

## Validación final

Al terminar todas las partes, verificar:

- [ ] `iptables --version` muestra la versión instalada (v1.8.x, backend nf_tables)
- [ ] Existe `~/ruleset-inicial.txt` con el estado previo al laboratorio
- [ ] Con la política DROP activa: SSH (22) y HTTP (80) accesibles desde Windows,
      puerto 9999 inaccesible
- [ ] La regla DROP insertada con `-I INPUT 1` bloqueó el ping a 8.8.8.8 y su
      contador lo demuestra
- [ ] El ruleset final (tras `iptables-restore`) coincide con el inicial

## Complemento OFFen

!!! info "Ejercicio complementario (opcional)"
    Este laboratorio se complementa con cualquier desafío de reconocimiento y
    enumeración en la plataforma OFFen. Un escáner externo que intente conectarse
    a los puertos no listados en las reglas de ACCEPT descubrirá que no responden —
    esto es exactamente la perspectiva del atacante que los controles defensivos de
    este lab buscan neutralizar. El comportamiento del firewall desde afuera confirma
    que las reglas DROP funcionan como se diseñaron.

    Acceder desde: `http://[ip-offen-server]` con las credenciales del curso.

## Entrega

El alumno debe entregar:

1. Captura de `sudo iptables -L -v -n --line-numbers` con la política DROP y las
   4 reglas aplicadas (loopback, conntrack ESTABLISHED, tcp:22, tcp:80)
2. Captura de las pruebas inbound desde PowerShell: `Test-NetConnection` al puerto
   22 y al 80 (`True`) y al 9999 (`False`)
3. Captura del Paso 10: ping fallido a 8.8.8.8 **y** el contador de la regla
   insertada con `pkts` > 0
4. Captura del ruleset restaurado (Paso 11) junto al contenido de
   `~/ruleset-inicial.txt`

---

<!-- Solución disponible para el instructor en: docs/instructor/lab-iptables-p1-solucion.md -->

*Continúa en: [Lab iptables (Parte 2)](lab-iptables-p2.md)*
