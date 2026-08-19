---
# Horas asignadas: 2 hrs
# Tipo: Teoría con demostración guiada
---

# Firewall: Protección Perimetral Local (Parte 1)

> **Duración:** 2 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En el Ordenador

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar la diferencia entre un host firewall y un firewall perimetral de red.
- Describir el filtrado stateful vs. stateless y por qué Windows Defender Firewall es stateful.
- Navegar Windows Defender Firewall with Advanced Security (WFAS) e interpretar las reglas existentes.
- Crear y comprobar reglas inbound y outbound por puerto y protocolo.
- Explicar los tres perfiles de red (Domain, Private, Public) y cuándo aplica cada uno.

---

## ¿Qué es un Firewall?

Un firewall inspecciona el tráfico de red y decide si lo permite o lo bloquea mediante reglas predefinidas.

La analogía más precisa es el portero de un edificio: decide quién entra, controla a dónde pueden ir quienes están dentro y quién puede salir.

Hay dos tipos de firewall que debemos distinguir. No son excluyentes, sino complementarios:

**Host firewall:** corre en el ordenador que protege. Windows Defender Firewall controla el tráfico que llega a ese equipo o sale de él. Es el que estudiamos en esta clase.

**Firewall perimetral de red:** corre en un router, un dispositivo dedicado o una UTM, entre la red interna y el exterior. Protege la red completa, no un único equipo.

**¿Por qué necesitamos ambos?** El firewall perimetral protege la red frente a conexiones procedentes del exterior.

Si el atacante ya comprometió un equipo o conectó un dispositivo no autorizado, ese tráfico puede no cruzar el perímetro. El host firewall limita entonces el movimiento lateral entre equipos internos.

En una red de unidad no todos los dispositivos están bajo el mismo control. Por eso una amenaza interna puede ser tan relevante como una conexión procedente del exterior.

---

## Filtrado Stateful vs. Stateless

La diferencia entre filtrado stateful y stateless es fundamental para entender cómo funciona cualquier firewall moderno.

**Filtrado stateless (sin estado):** el firewall examina cada paquete de forma independiente. Considera datos como las IP, el puerto y el protocolo.

No recuerda los paquetes anteriores ni el contexto de la conversación.

**Filtrado stateful (con estado):** el firewall mantiene una tabla de conexiones activas. Recuerda qué conexión se inició, sus extremos y su estado.

Si el equipo inicia una conexión TCP, el firewall permite las respuestas asociadas porque reconoce que pertenecen a esa conversación.

| Escenario | Stateless | Stateful |
|-----------|-----------|---------|
| El ordenador solicita una página web (puerto 80 saliente) | Debe haber una regla explícita de ENTRADA que permita las respuestas del servidor | Permite automáticamente las respuestas porque recuerda que la petición fue iniciada localmente |
| Un atacante externo envía paquetes al puerto 80 del ordenador | Se evalúa igual que cualquier otro paquete — si hay regla de entrada que permita el puerto 80, entra | Se evalúa como nueva conexión iniciada externamente — si no hay regla de entrada explícita, se bloquea |
| Escaneo de puertos externo | Cada paquete se evalúa sin contexto | Cada intento es una conexión nueva y se evalúa contra la política inbound; mantener estado no equivale a ser un IDS |

**Windows Defender Firewall es stateful.** Si una regla permite iniciar una conexión outbound, no hace falta otra regla inbound para sus respuestas.

El firewall reconoce el estado de esa conversación y permite el tráfico de retorno asociado.

---

## Los Tres Perfiles de Red

Windows Defender Firewall utiliza tres perfiles. El perfil activo determina qué reglas se aplican y Windows lo selecciona según el tipo de red detectado.

| Perfil | Cuándo aplica | Comportamiento por defecto | Relevancia militar |
|--------|--------------|--------------------------|-------------------|
| **Domain** | El equipo está conectado a una red con un controlador de dominio de Active Directory que Windows reconoce | Reglas gestionadas por la política de dominio; tráfico de administración permitido | Aplicable en redes de unidad con servidor de directorio activo |
| **Private** | El usuario marcó manualmente la red como "confiable" (red de hogar, red de oficina conocida) | Descubrimiento de red habilitado; se permite algo de tráfico entrante para compartición de recursos | Red de unidad marcada como privada por el Técnico responsable |
| **Public** | Red desconocida (WiFi de hotel, red de café, cualquier red no marcada como privada o dominio) | Descubrimiento de red deshabilitado; tráfico entrante bloqueado al máximo | Cualquier red no verificada en campo — debe ser siempre el perfil predeterminado en redes externas |

El perfil Public es la postura más restrictiva y debe aplicarse siempre en entornos no controlados.

!!! example "Aplicación en entorno castrense"
    Un Técnico conecta su laptop a la red WiFi de un hotel. Windows detecta una red nueva, no marcada como privada ni asociada al dominio, y activa el perfil Public.

    El descubrimiento de red queda deshabilitado y el tráfico inbound no solicitado recibe la política más restrictiva.

    Error común: seleccionar «Privada» en una red de hotel o de un cliente relaja el perfil sin justificación.

    En un entorno militar, una red no verificada se marca como Pública.

---

## Reglas de Entrada (Inbound) vs. Reglas de Salida (Outbound)

La dirección siempre se determina desde el equipo protegido. En esta clase, ese equipo es la VM Windows.

**Inbound:** otra máquina inicia una conexión cuyo destino es Windows. La regla decide si esa conexión puede llegar a un servicio local.

**Outbound:** Windows inicia una conexión cuyo destino es otra máquina. La regla decide si esa conexión puede salir del equipo.

La postura usual bloquea inbound no solicitado y permite outbound. Esto implica que el firewall no detiene por defecto todas las conexiones salientes de un malware.

!!! warning "El outbound permisivo es explotado por el malware moderno"
    Muchos administradores se concentran en inbound. Sin embargo, un troyano instalado puede iniciar conexiones outbound hacia su servidor de Comando y Control.

    El malware puede usar TCP 443 porque ese puerto también transporta HTTPS legítimo y suele estar permitido.

    En equipos de alta seguridad, las reglas outbound pueden limitar qué aplicaciones y destinos están autorizados. Esta medida complementa al antivirus y a los controles de red.

**Comportamiento predeterminado resumido:**

- **Inbound sin regla de permiso:** Bloqueado silenciosamente.
- **Outbound sin regla de bloqueo:** Permitido.
- **Regla de bloqueo vs. regla de permiso:** Las reglas de bloqueo tienen precedencia sobre las de permiso. Si existe una regla de bloqueo y una de permiso para el mismo tráfico, el bloqueo gana.

---

## Demostración visible de Inbound y Outbound

La VM conserva su adaptador NAT para Internet y utiliza un segundo adaptador Host-only para esta demostración. La red Host-only comunica la VM con el anfitrión, pero no con la red física.

```text
INBOUND:  Anfitrión 192.168.56.1 ──► VM Windows 192.168.56.101:18080
          El anfitrión inicia; la conexión entra en la VM.

OUTBOUND: VM Windows 192.168.56.101 ──► Anfitrión 192.168.56.1:18081
          La VM inicia; la conexión sale de la VM.
```

Los nombres inbound y outbound no describen la ubicación del servidor. Describen quién inicia la conexión respecto de la VM Windows, que es el equipo cuyo firewall se está probando.

!!! note "Por qué no usamos localhost"
    El tráfico sobre `127.0.0.1` no representa una conexión entre dos equipos. Host-only proporciona extremos y direcciones diferentes, por lo que el sentido del flujo puede comprobarse.

| Prueba | Iniciador | Destino | Dirección para la VM | Puerto que se filtra |
|---|---|---|---|---|
| 1 | VM Windows | Servidor en el anfitrión | Outbound | Remoto `18081` |
| 2 | Anfitrión | Servicio en la VM | Inbound | Local `18080` |

### Preparación

La demostración utiliza tres consolas de PowerShell. Cada instrucción indica en cuál debe ejecutarse:

1. **PowerShell — Anfitrión:** se abre **en el Windows anfitrión** (fuera de la VM), **como Administrador**. Ejecuta el segundo extremo de cada conexión.
2. **PowerShell — Alumno:** se abre dentro de la VM Windows. Crea servicios y conexiones.
3. **PowerShell — Administrador:** se abre dentro de la VM Windows, como Administrador. Administra exclusivamente las reglas del firewall de la VM.

Mantener las tres consolas abiertas. Las variables de PowerShell solo existen en la consola donde fueron definidas.

#### Preparar la red Host-only

Con la VM apagada, conservar **Adaptador 1: NAT**. Habilitar **Adaptador 2**, seleccionar **Adaptador solo-anfitrión**, elegir **VirtualBox Host-Only Ethernet Adapter** y marcar **Cable conectado**.

En VirtualBox (**Archivo → Herramientas → Network Manager**), esa red Host-only debe utilizar `192.168.56.1/24`. El DHCP debe entregar direcciones desde `192.168.56.101`. Esta red no reemplaza al adaptador NAT.

En **PowerShell — Anfitrión**, confirmar la dirección del adaptador Host-only:

```powershell
Get-NetIPAddress -AddressFamily IPv4 -InterfaceAlias '*VirtualBox*' |
    Format-List InterfaceAlias, IPAddress, PrefixLength
```

**Se espera ver:** `IPAddress = 192.168.56.1` con `PrefixLength = 24`. Si no aparece, detener la demostración y revisar la configuración Host-only.

En **PowerShell — Alumno**, identificar las direcciones de la VM:

```powershell
Get-NetIPConfiguration |
    Format-List InterfaceAlias, IPv4Address, IPv4DefaultGateway
```

**Se espera ver:** `192.168.56.101` sin puerta de enlace y `10.0.2.15` con puerta de enlace. La primera pertenece a Host-only; la segunda pertenece a NAT.

Una dirección `169.254.x.x` pertenece a otra interfaz sin DHCP. No debe utilizarse en esta demostración.

Si la dirección Host-only no es `192.168.56.101`, reemplazar ese valor en todos los comandos siguientes. No sustituirlo por la dirección NAT.

#### Comprobar privilegios y estado del firewall

Abrir **PowerShell — Administrador** y confirmar la elevación:

```powershell
([Security.Principal.WindowsPrincipal]::new(
    [Security.Principal.WindowsIdentity]::GetCurrent()
)).IsInRole(
    [Security.Principal.WindowsBuiltInRole]::Administrator
)
```

**Se espera ver:** `True`. Si devuelve `False`, no crear ni eliminar reglas.

Repetir la misma comprobación en **PowerShell — Anfitrión**: también debe devolver `True`, porque esa consola creará una regla en el firewall del anfitrión y levantará un servidor HTTP.

En esa misma consola, confirmar que los perfiles del firewall están habilitados:

```powershell
Get-NetFirewallProfile |
    Format-Table Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

**Se espera ver:** `Enabled = True` en los perfiles. `NotConfigured` en una acción indica que Windows usa la política heredada; no significa que el firewall esté desactivado.

Comprobar que no quedaron reglas de una ejecución anterior:

```powershell
$ReglasAnteriores = Get-NetFirewallRule `
    -DisplayName 'LAB-FW-*' `
    -ErrorAction SilentlyContinue

if ($ReglasAnteriores) {
    throw 'Existen reglas LAB-FW de una ejecucion anterior. Avisar al instructor.'
}
```

### Prueba 1 — Regla Outbound

La VM Windows solicitará una página a un servidor HTTP del anfitrión. Como la VM inicia la conexión, el flujo es outbound para la VM.

#### Paso 1 — Levantar el servidor de destino en el anfitrión

El anfitrión también ejecuta Windows Defender Firewall, y su política inbound bloquearía la conexión de la VM hacia el puerto `18081`. Antes de levantar el servidor, crear un permiso limitado al laboratorio.

En **PowerShell — Anfitrión**:

```powershell
New-NetFirewallRule `
    -DisplayName 'LAB-FW-HOST-ALLOW-18081' `
    -Direction Inbound `
    -Action Allow `
    -Protocol TCP `
    -LocalAddress 192.168.56.1 `
    -RemoteAddress 192.168.56.101 `
    -LocalPort 18081 `
    -Profile Any
```

Esta regla vive en el firewall del anfitrión, no en el de la VM. La ruta completa atraviesa dos firewalls: el outbound de la VM y el inbound del anfitrión.

En la misma consola, levantar el servidor HTTP:

```powershell
$Listener = [System.Net.HttpListener]::new()
$Listener.Prefixes.Add('http://192.168.56.1:18081/')
$Listener.Start()

'Servidor OUTBOUND escuchando en http://192.168.56.1:18081/'

$Cuerpo = [System.Text.Encoding]::UTF8.GetBytes("OUTBOUND PERMITIDO`n")

while ($Listener.IsListening) {
    $Tarea = $Listener.GetContextAsync()

    while (-not $Tarea.AsyncWaitHandle.WaitOne(500)) { }

    $Contexto = $Tarea.GetAwaiter().GetResult()
    "GET $($Contexto.Request.RawUrl) desde $($Contexto.Request.RemoteEndPoint)"

    $Contexto.Response.ContentType = 'text/plain'
    $Contexto.Response.OutputStream.Write($Cuerpo, 0, $Cuerpo.Length)
    $Contexto.Response.Close()
}
```

Mantener la consola abierta con el bucle en ejecución. Cada conexión permitida producirá una línea `GET /` visible en el anfitrión. El bucle se detendrá con `Ctrl+C` durante la limpieza.

#### Paso 2 — Confirmar la línea base permitida

En **PowerShell — Alumno**:

```powershell
$AnfitrionIp = '192.168.56.1'
$OutboundPort = 18081
$OutboundUri = "http://${AnfitrionIp}:$OutboundPort/"

Test-NetConnection $AnfitrionIp -Port $OutboundPort |
    Format-List ComputerName, RemotePort, InterfaceAlias, `
        SourceAddress, TcpTestSucceeded

$Respuesta = Invoke-WebRequest `
    -Uri $OutboundUri `
    -UseBasicParsing `
    -TimeoutSec 5

"RESULTADO OUTBOUND: PERMITIDA (HTTP $($Respuesta.StatusCode))"
```

**Se espera ver:** origen `192.168.56.101`, `TcpTestSucceeded = True` y `HTTP 200`. El anfitrión registra una línea `GET /`.

Esta línea base demuestra que el servidor y la ruta funcionan antes de modificar el firewall. Sin ella, un fallo posterior sería ambiguo.

#### Paso 3 — Crear el bloqueo outbound

En **PowerShell — Administrador**:

```powershell
$ReglaSalida = 'LAB-FW-OUT-BLOCK-18081'

New-NetFirewallRule `
    -DisplayName $ReglaSalida `
    -Direction Outbound `
    -Action Block `
    -Protocol TCP `
    -LocalAddress 192.168.56.101 `
    -RemoteAddress 192.168.56.1 `
    -RemotePort 18081 `
    -Profile Any

Get-NetFirewallRule -DisplayName $ReglaSalida |
    Format-List DisplayName, Enabled, Direction, Action, Profile
```

`RemotePort` es `18081` porque el puerto pertenece al servidor remoto. Las direcciones limitan la regla a la red Host-only y evitan afectar otras conexiones.

#### Paso 4 — Comprobar el bloqueo

En **PowerShell — Alumno**:

```powershell
Test-NetConnection 192.168.56.1 -Port 18081 |
    Format-List SourceAddress, RemoteAddress, RemotePort, `
        TcpTestSucceeded

try {
    Invoke-WebRequest `
        -Uri 'http://192.168.56.1:18081/' `
        -UseBasicParsing `
        -TimeoutSec 5 `
        -ErrorAction Stop

    'ERROR: la conexion todavia esta permitida.'
}
catch {
    'RESULTADO OUTBOUND: BLOQUEADA por la regla.'
}
```

**Se espera ver:** `TcpTestSucceeded = False` y `RESULTADO OUTBOUND: BLOQUEADA`. El anfitrión no registra otro `GET` porque la conexión no salió de la VM.

#### Paso 5 — Deshabilitar la regla y comprobar la recuperación

En **PowerShell — Administrador**:

```powershell
Disable-NetFirewallRule -DisplayName 'LAB-FW-OUT-BLOCK-18081'
```

En **PowerShell — Alumno**:

```powershell
$Respuesta = Invoke-WebRequest `
    -Uri 'http://192.168.56.1:18081/' `
    -UseBasicParsing `
    -TimeoutSec 5

"RESULTADO OUTBOUND: RECUPERADA (HTTP $($Respuesta.StatusCode))"
```

**Se espera ver:** `HTTP 200` y un nuevo `GET /` en el anfitrión. El servidor no cambió; solo cambió el estado de la regla outbound.

### Prueba 2 — Regla Inbound

El anfitrión enviará un mensaje a un servicio TCP de la VM Windows. Como el anfitrión inicia la conexión y la VM la recibe, el flujo es inbound para la VM.

#### Paso 1 — Preparar el permiso antes de levantar el servicio

En **PowerShell — Administrador**, crear primero el permiso limitado al laboratorio. Después identificar exclusivamente las reglas automáticas `TCP/UDP Query User` que Windows creó al cancelar el aviso de acceso para PowerShell. Estos bloqueos explícitos prevalecen sobre el permiso, por lo que se deshabilitan temporalmente desde su almacén de origen.

```powershell
$WindowsLabIp = '192.168.56.101'
$AnfitrionIp = '192.168.56.1'
$InboundPort = 18080
$ReglaEntradaPermitida = 'LAB-FW-IN-ALLOW-18080'
$PowerShellExe = "$env:SystemRoot\System32\WindowsPowerShell\v1.0\powershell.exe"

New-NetFirewallRule `
    -DisplayName $ReglaEntradaPermitida `
    -Direction Inbound `
    -Action Allow `
    -Protocol TCP `
    -LocalAddress $WindowsLabIp `
    -RemoteAddress $AnfitrionIp `
    -LocalPort $InboundPort `
    -Profile Any

Get-NetFirewallRule `
    -PolicyStore ActiveStore `
    -DisplayName $ReglaEntradaPermitida |
    Format-List DisplayName, Enabled, Direction, Action, `
        Profile, EnforcementStatus

$BloqueosPowerShell = @(
    Get-NetFirewallApplicationFilter `
        -PolicyStore PersistentStore |
        Where-Object {
            $_.Program -ieq $PowerShellExe
        } |
        Get-NetFirewallRule |
        Where-Object {
            $_.Name -match '^(TCP|UDP) Query User\{' -and
            $_.Enabled -eq 'True' -and
            $_.Direction -eq 'Inbound' -and
            $_.Action -eq 'Block' -and
            $_.Profile -eq 'Public'
        }
)

$NombresBloqueoPowerShell = @(
    $BloqueosPowerShell |
        Select-Object -ExpandProperty Name
)

if ($NombresBloqueoPowerShell.Count -gt 0) {
    Disable-NetFirewallRule `
        -PolicyStore PersistentStore `
        -Name $NombresBloqueoPowerShell

    Get-NetFirewallRule `
        -PolicyStore PersistentStore `
        -Name $NombresBloqueoPowerShell |
        Format-Table DisplayName, Enabled, Direction, Action, Profile
}
```

**Se espera ver:** si existían los dos bloqueos `Windows PowerShell`, deben aparecer con `Enabled = False`. La regla `LAB-FW-IN-ALLOW-18080` debe mostrar `Enabled = True`, `Action = Allow` y un estado que incluya `Enforced`.

No cerrar esta consola: `$NombresBloqueoPowerShell` conserva exactamente las reglas que estaban habilitadas y que deben restaurarse durante la limpieza. Si la lista estaba vacía, no se modifica ninguna regla de PowerShell.

!!! danger "Restauración de emergencia"
    Si la práctica se interrumpe después de deshabilitar los bloqueos, ejecutar esto **antes de cerrar PowerShell — Administrador**:

    ```powershell
    if ($NombresBloqueoPowerShell.Count -gt 0) {
        Enable-NetFirewallRule `
            -PolicyStore PersistentStore `
            -Name $NombresBloqueoPowerShell
    }

    Get-NetFirewallRule `
        -DisplayName 'LAB-FW-*' `
        -ErrorAction SilentlyContinue |
        Remove-NetFirewallRule
    ```

#### Paso 2 — Levantar el servicio local en Windows

En **PowerShell — Alumno**:

```powershell
$WindowsLabIp = '192.168.56.101'
$InboundPort = 18080

$ServidorWindows = Start-Job `
    -ArgumentList $WindowsLabIp, $InboundPort `
    -ScriptBlock {
        param($Address, $Port)

        $Ip = [System.Net.IPAddress]::Parse($Address)
        $Listener = [System.Net.Sockets.TcpListener]::new($Ip, $Port)
        $Listener.Start()

        try {
            while ($true) {
                $Client = $Listener.AcceptTcpClient()

                try {
                    $Remote = $Client.Client.RemoteEndPoint
                    $Reader = [System.IO.StreamReader]::new(
                        $Client.GetStream()
                    )

                    $Mensaje = $Reader.ReadLine()
                    "ACEPTADA desde $Remote | Mensaje: $Mensaje"
                    $Reader.Dispose()
                }
                finally {
                    $Client.Dispose()
                }
            }
        }
        finally {
            $Listener.Stop()
        }
    }

Start-Sleep -Seconds 2

Get-NetTCPConnection `
    -LocalAddress $WindowsLabIp `
    -LocalPort $InboundPort `
    -State Listen |
    Format-Table LocalAddress, LocalPort, State
```

**Se espera ver:** una fila `Listen` en `192.168.56.101:18080`. Esto prueba que el servicio existe, pero todavía no demuestra que el anfitrión pueda alcanzarlo.

El permiso se creó antes de iniciar el listener para evitar el aviso automático. **No cancelar ese aviso si apareciera:** Windows crearía dos reglas `Block` para `powershell.exe`, una TCP y otra UDP, que anularían el permiso del laboratorio. En ese caso, detener la práctica y avisar al instructor.

`LocalPort` es `18080` porque el servicio se ejecuta en Windows. `RemoteAddress` restringe el permiso al anfitrión del laboratorio.

#### Paso 3 — Confirmar que el anfitrión puede entrar

En **PowerShell — Anfitrión** (el servidor HTTP de la Prueba 1 sigue en su bucle; abrir una segunda pestaña o consola de anfitrión para este comando):

```powershell
$Cliente = [System.Net.Sockets.TcpClient]::new()

try {
    if ($Cliente.ConnectAsync('192.168.56.101', 18080).Wait(5000)) {
        $Escritor = [System.IO.StreamWriter]::new($Cliente.GetStream())
        $Escritor.WriteLine('INBOUND_DESDE_ANFITRION')
        $Escritor.Flush()
        $Escritor.Dispose()

        'RESULTADO INBOUND: PERMITIDA'
    }
    else {
        'RESULTADO INBOUND: BLOQUEADA'
    }
}
catch {
    'RESULTADO INBOUND: BLOQUEADA'
}
finally {
    $Cliente.Dispose()
}
```

**Se espera ver en el anfitrión:** `RESULTADO INBOUND: PERMITIDA`.

En **PowerShell — Alumno**, consumir la evidencia recibida por el servicio:

```powershell
Start-Sleep -Seconds 1
Receive-Job -Job $ServidorWindows
```

**Se espera ver en la VM:** `ACEPTADA desde 192.168.56.1` y `Mensaje: INBOUND_DESDE_ANFITRION`.

Esta doble evidencia prueba que el anfitrión inició la conexión y que el proceso local de la VM recibió su contenido.

#### Paso 4 — Crear un bloqueo inbound

En **PowerShell — Administrador**:

```powershell
$ReglaEntradaBloqueada = 'LAB-FW-IN-BLOCK-18080'

New-NetFirewallRule `
    -DisplayName $ReglaEntradaBloqueada `
    -Direction Inbound `
    -Action Block `
    -Protocol TCP `
    -LocalAddress 192.168.56.101 `
    -RemoteAddress 192.168.56.1 `
    -LocalPort 18080 `
    -Profile Any

Get-NetFirewallRule -DisplayName 'LAB-FW-IN-*' |
    Format-Table DisplayName, Enabled, Direction, Action
```

Ahora existen un permiso y un bloqueo que coinciden con el mismo flujo. El bloqueo tiene precedencia, por lo que la siguiente conexión debe fallar.

#### Paso 5 — Comprobar el bloqueo inbound

Repetir en **PowerShell — Anfitrión** el comando del Paso 3.

**Se espera ver:** `RESULTADO INBOUND: BLOQUEADA` después del tiempo de espera de 5 segundos.

En **PowerShell — Alumno**, confirmar que el servicio sigue escuchando y no recibió otro mensaje:

```powershell
Get-NetTCPConnection `
    -LocalAddress 192.168.56.101 `
    -LocalPort 18080 `
    -State Listen |
    Format-Table LocalAddress, LocalPort, State

$NuevaEvidencia = Receive-Job -Job $ServidorWindows

if ($NuevaEvidencia) {
    $NuevaEvidencia
}
else {
    'SIN MENSAJE NUEVO: el firewall detuvo la conexion.'
}
```

El estado `Listen` demuestra que el servicio no se detuvo. La ausencia de un mensaje nuevo atribuye el fallo a la regla, no al proceso.

#### Paso 6 — Deshabilitar el bloqueo y recuperar la entrada

En **PowerShell — Administrador**:

```powershell
Disable-NetFirewallRule -DisplayName 'LAB-FW-IN-BLOCK-18080'
```

Repetir en **PowerShell — Anfitrión** el comando del Paso 3. Debe mostrar `RESULTADO INBOUND: PERMITIDA`.

En **PowerShell — Alumno**:

```powershell
Start-Sleep -Seconds 1
Receive-Job -Job $ServidorWindows
```

**Se espera ver:** un segundo mensaje `ACEPTADA`. El servicio no cambió; solo se deshabilitó la regla inbound de bloqueo.

### Qué se acaba de comprobar

| Flujo respecto de la VM | Puerto evaluado | Regla aplicada | Evidencia |
|---|---:|---|---|
| Outbound: VM → Anfitrión | Remoto `18081` | Línea base; Block; Disabled | HTTP 200; fallo; HTTP 200. |
| Inbound: Anfitrión → VM | Local `18080` | Allow; Block; Block disabled | Mensaje recibido; fallo; mensaje recibido. |

Las respuestas de una conexión permitida pueden regresar sin crear una regla en la dirección contraria. Windows Defender Firewall recuerda el estado de la conversación.

### Limpieza obligatoria

En **PowerShell — Anfitrión**, pulsar `Ctrl+C` en la consola del servidor HTTP. Después liberar el puerto y retirar la regla del anfitrión:

```powershell
$Listener.Stop()
$Listener.Close()

Remove-NetFirewallRule `
    -DisplayName 'LAB-FW-HOST-ALLOW-18081' `
    -ErrorAction SilentlyContinue
```

En **PowerShell — Administrador** (dentro de la VM), retirar solo las reglas de esta demostración:

```powershell
@(
    'LAB-FW-OUT-BLOCK-18081',
    'LAB-FW-IN-ALLOW-18080',
    'LAB-FW-IN-BLOCK-18080'
) | ForEach-Object {
    Remove-NetFirewallRule -DisplayName $_ -ErrorAction SilentlyContinue
}

Get-NetFirewallRule `
    -DisplayName 'LAB-FW-*' `
    -ErrorAction SilentlyContinue

if ($NombresBloqueoPowerShell.Count -gt 0) {
    Enable-NetFirewallRule `
        -PolicyStore PersistentStore `
        -Name $NombresBloqueoPowerShell

    Get-NetFirewallRule `
        -PolicyStore PersistentStore `
        -Name $NombresBloqueoPowerShell |
        Format-Table DisplayName, Enabled, Direction, Action, Profile
}
```

En **PowerShell — Alumno**, detener el receptor y comprobar el puerto:

```powershell
Stop-Job -Job $ServidorWindows -ErrorAction SilentlyContinue
Remove-Job -Job $ServidorWindows -Force -ErrorAction SilentlyContinue

Get-NetTCPConnection `
    -State Listen `
    -LocalPort $InboundPort `
    -ErrorAction SilentlyContinue
```

**Se espera ver:** ninguna regla `LAB-FW-*`, ningún receptor en `18080` y, si al comenzar existían bloqueos de Windows PowerShell, todos nuevamente con `Enabled = True`. El adaptador Host-only puede conservarse para repetir la práctica.

---

## Navegando Windows Defender Firewall with Advanced Security

Para abrir la consola avanzada: `Win+R → wf.msc` (o buscar "Windows Defender Firewall with Advanced Security" en el menú Inicio).

La consola WFAS tiene cuatro secciones principales en el panel izquierdo:

- **Inbound Rules:** Lista de todas las reglas de entrada configuradas.
- **Outbound Rules:** Lista de todas las reglas de salida configuradas.
- **Connection Security Rules:** Reglas IPsec — fuera del alcance de esta clase.
- **Monitoring:** Vista del estado actual, conexiones activas y reglas activas por perfil.

**Leyendo una regla existente:** Cada regla en la lista de Inbound o Outbound Rules tiene columnas:

| Columna | Qué muestra |
|---------|------------|
| Name | Nombre descriptivo de la regla |
| Group | Agrupación lógica (por ejemplo, "Remote Desktop") |
| Profile | A qué perfil(es) aplica (Domain, Private, Public o combinaciones) |
| Enabled | Yes/No — si la regla está activa |
| Action | Allow o Block |
| Override | Si la regla puede ser anulada por otra regla |
| Program | Ejecutable específico al que aplica (si aplica) |
| Local Address | IP o rango local |
| Remote Address | IP o rango remoto |
| Protocol | TCP, UDP, Any |
| Local Port | Puerto local |
| Remote Port | Puerto remoto |

!!! tip "Filtrar reglas para ver solo las activas"
    La lista de Inbound Rules puede tener cientos de entradas. Ordenar por la columna `Enabled` agrupa primero las reglas habilitadas.

    También se puede usar **Action → Filter by Profile** para mostrar únicamente el perfil que se está investigando.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Los equipos de los oficiales reciben administración remota mediante RDP, TCP 3389, pero solo desde el servidor autorizado `192.168.10.5`.

    El Técnico crea una regla en WFAS:

    - **Tipo:** Inbound Rule → New Rule → Port
    - **Protocol and Ports:** TCP, Specific local ports: 3389
    - **Action:** Allow the connection
    - **Profile:** Domain, Private (no Public)
    - **Remote IP (en Advanced → Remote IP address):** This IP address or subnet: 192.168.10.5
    - **Name:** "RDP desde servidor de administración únicamente"

    No se añade una regla `Block` para cualquier IP. Esa regla también coincidiría con `192.168.10.5` y el bloqueo tendría precedencia sobre el permiso.

    La regla `Allow` está limitada al servidor autorizado. La política inbound predeterminada bloquea las conexiones procedentes de las demás direcciones.

    El resultado es que solo `192.168.10.5` puede iniciar RDP. Cualquier otro equipo recibe un bloqueo silencioso.

---

## Resumen

1. Un **host firewall** como Windows Defender Firewall protege el equipo individual; el **firewall perimetral de red** protege toda la red — se necesitan ambos porque el host firewall defiende contra movimiento lateral dentro de la red.
2. El filtrado **stateful** recuerda el estado de las conexiones y permite respuestas automáticamente; el **stateless** evalúa cada paquete de forma independiente — Windows Defender Firewall es stateful.
3. Los **tres perfiles** (Domain, Private, Public) definen qué tan restrictivo es el firewall según el entorno — en redes no verificadas siempre debe usarse el perfil Public.
4. Por defecto, todo el **tráfico entrante** sin regla de permiso se bloquea; todo el **tráfico saliente** sin regla de bloqueo se permite — las reglas de bloqueo siempre tienen precedencia sobre las de permiso.
5. WFAS se abre con `wf.msc` — las reglas se leen por columnas: Enabled, Action, Protocol, Local Port, Remote Port, Program.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- [Oracle VirtualBox: redes Host-only](https://docs.oracle.com/en/virtualization/virtualbox/7.0/user/networkingdetails.html#networkingdetails-hostonly)
- [Microsoft Learn: `New-NetFirewallRule`](https://learn.microsoft.com/es-es/powershell/module/netsecurity/new-netfirewallrule)

---

*Siguiente: [Firewall (Parte 2)](cortafuegos-p2.md)*
