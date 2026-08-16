---
# Horas asignadas: 4 hrs
# Tipo: Teoría + Laboratorio
---

# Controles Defensivos en el Ordenador (Parte 1)

> **Duración:** 4 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En el Ordenador

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Definir qué es un control de seguridad y distinguir entre controles preventivos, detectivos y correctivos.
- Identificar la superficie de ataque de un ordenador con Windows 11 (puertos abiertos, servicios en ejecución, cuentas de usuario, software instalado).
- Aplicar un checklist básico de hardening basado en los conceptos del CIS Benchmark Level 1.
- Explicar el principio de mínimo privilegio y verificar su aplicación auditando las cuentas locales del equipo.

---

## Entorno de laboratorio

Cada tema principal de esta clase termina con un bloque **▸ Práctica**, que se ejecuta sobre la máquina virtual **VM-WIN-SI**. La estación virtual llega deliberadamente con defectos de configuración: el trabajo del alumno es evaluarla, no dar todo por bueno.

!!! info "Credenciales, arranque de la VM y capturas de pantalla"
    Todo eso está en la página **[Laboratorio virtual — accesos y credenciales](laboratorio-vm.md)**. Conviene tenerla abierta en otra pestaña durante la clase.

| Requisito | Verificación |
|-----------|-------------|
| VM-WIN-SI iniciada desde el estado preparado | El instructor confirma la restauración de `01-MISION-CONTROLES` |
| Sesión abierta como cuenta estándar | `operador_alumno` — **no abrir PowerShell como administrador en esta Parte 1** |
| Archivo de incidentes disponible | `Test-Path C:\Mision\incidentes-CIA.txt` devuelve `True` |
| Windows PowerShell 5.1 de 64 bits | Abrir **Windows PowerShell** desde el menú Inicio |

!!! warning "Reglas del laboratorio"
    - Trabajar de forma individual salvo indicación del instructor.
    - Toda la Parte 1 se ejecuta **sin elevación**. Lo que no se pueda verificar sin privilegios se anota como tal — esa limitación es parte de la lección.
    - Guardar cada evidencia con el nombre exacto indicado.
    - Si un paso falla, **no improvisar otro comando**: registrar el hallazgo y notificar al instructor.

### ▸ Práctica 0 — Preparar el registro de evidencia

**Tiempo:** 10 minutos.

Toda actuación sobre un equipo debe quedar registrada. Antes de tocar nada, se abre una transcripción que grabará cada comando y su resultado.

```powershell
New-Item -ItemType Directory -Path C:\Lab\Evidencias -Force | Out-Null
Start-Transcript -Path C:\Lab\Evidencias\01-reconocimiento.txt -Force
```

**Se espera ver:** un mensaje que confirme el inicio de la transcripción en `C:\Lab\Evidencias\01-reconocimiento.txt`.

**Si no aparece:** comprobar que la sesión corresponde a VM-WIN-SI y avisar al instructor. No elegir otra ruta ni continuar sin transcripción.

!!! warning "Mantener la ventana abierta"
    Esta ventana de PowerShell permanece abierta durante toda la Parte 1. Si se cierra, la transcripción se corta y se pierde la evidencia.

---

## Qué es un control de seguridad

Un **control de seguridad** es cualquier medida —técnica, organizativa o física— que se implanta para reducir un riesgo. El cortafuegos es un control, pero también lo son la política de contraseñas, el registro de auditoría y la orden que prohíbe conectar memorias USB ajenas.

Los controles se clasifican según **el momento en que actúan** respecto al incidente:

| Tipo | Cuándo actúa | Qué hace | Ejemplo en la estación |
|------|-------------|----------|------------------------|
| **Preventivo** | Antes | Impide que el incidente llegue a ocurrir | Cuenta Invitado deshabilitada; SMBv1 retirado; contraseña de 14 caracteres |
| **Detectivo** | Durante o después | No impide el hecho, pero deja constancia de que ocurrió | Auditoría de inicio de sesión; registro del cortafuegos; transcripción de PowerShell |
| **Correctivo** | Después | Restablece el estado correcto una vez detectado el problema | Cuarentena del antivirus; restauración de un snapshot; retirada de privilegios de una cuenta |

!!! note "Ninguno basta por sí solo"
    Un control preventivo que falla y no deja rastro es un incidente invisible: nadie sabrá nunca que ocurrió. Un control detectivo sin corrección es un registro de derrotas. La defensa útil combina los tres — a esto se le llama **defensa en profundidad**, y es el eje de toda la Asignatura 2.

!!! example "Aplicación en entorno castrense"
    Un centinela en la puerta del cuartel es **preventivo**: impide la entrada. El libro de registro de visitas es **detectivo**: no impide nada, pero permite reconstruir quién entró. La orden de cambiar la cerradura tras extraviarse una llave es **correctiva**: repara la situación una vez conocida. Ningún jefe de seguridad renunciaría a ninguno de los tres.

Durante todo el curso, cada vez que se configure una protección conviene preguntarse dos cosas: **qué dimensión de la Triada CIA protege** y **de qué tipo de control se trata**.

---

## Triada CIA

Todo lo que estudiaremos en estas 91 horas gira en torno a tres conceptos. Si alguna vez una amenaza, un incidente o una decisión de configuración le genera duda, pregúntese: ¿cuál de las tres dimensiones de la Triada CIA está afectada?

La Triada CIA es como las tres patas de un banco: si falta una, el banco cae. Un sistema puede ser confidencial e íntegro, pero si no está disponible cuando lo necesita la misión, falló. Un sistema puede estar disponible e íntegro, pero si el enemigo puede leer los datos, también falló.

!!! note "Definición"
    La **Triada CIA** es el modelo fundamental de seguridad de la información que establece tres propiedades que todo sistema debe garantizar: **Confidencialidad** (solo los autorizados acceden), **Integridad** (los datos no son alterados sin autorización) y **Disponibilidad** (el sistema funciona cuando se necesita).

| Dimensión | Definición | Ejemplo en Windows 11 |
|-----------|-----------|----------------------|
| **Confidencialidad** | La información solo es accesible para quienes están autorizados a verla. | Una carpeta con documentos clasificados está protegida con contraseña y permisos NTFS — solo el oficial habilitado puede abrirla. |
| **Integridad** | La información y los sistemas no han sido alterados sin autorización. | El hash SHA-256 de un archivo de configuración crítico coincide con el valor registrado en la auditoría — nadie lo modificó. |
| **Disponibilidad** | Los sistemas y datos están accesibles cuando los usuarios autorizados los necesitan. | El ordenador del puesto de mando arranca correctamente, los servicios de red están activos y las comunicaciones cifradas funcionan durante la operación. |

Cada vez que se configure una nueva protección en este curso, se identificará a qué dimensión de la Triada CIA contribuye. Los controles no son independientes: una misma configuración puede proteger las tres dimensiones simultáneamente.

**Ejemplo integrado:** El ransomware es el ataque perfecto contra la Triada CIA completa. El atacante exfiltra datos antes de cifrar (vulnera Confidencialidad), modifica y cifra los archivos (vulnera Integridad) y hace el sistema inutilizable (vulnera Disponibilidad). Por eso el ransomware es la amenaza más devastadora para infraestructuras críticas.

### ▸ Práctica 1 — Clasificar incidentes reales según la Triada CIA

**Tiempo:** 25 minutos.

En `C:\Mision` la estación tiene un parte con cuatro incidentes reportados. El alumno debe determinar qué dimensión de la Triada afecta cada uno y justificarlo.

```powershell
Get-Content C:\Mision\incidentes-CIA.txt

@(
    'Incidente 1 — dimensión afectada y justificación:',
    'Incidente 2 — dimensión afectada y justificación:',
    'Incidente 3 — dimensión afectada y justificación:',
    'Incidente 4 — dimensión afectada y justificación:'
) | Set-Content C:\Lab\Evidencias\01-CIA.txt

notepad.exe C:\Lab\Evidencias\01-CIA.txt
```

Completar cada línea en el Bloc de notas y **guardar antes de cerrar**.

**Se espera ver:** cuatro incidentes en el archivo de misión y cuatro respuestas en `01-CIA.txt`, cada una con su dimensión y su justificación. El caso de ransomware debe considerar las tres dimensiones.

**Si no aparece:** si falta `incidentes-CIA.txt`, no reconstruir los casos de memoria. Detenerse y pedir al instructor que revise el estado inicial de la VM.

!!! question "Punto de control"
    ¿Cada respuesta explica **por qué** se afecta la dimensión elegida? Una sigla sin justificación no cumple el criterio de aceptación.

!!! note "Por qué `C:\Mision` es de solo lectura"
    Intentar escribir en esa carpeta devuelve *Acceso denegado*. Es intencional: el parte de misión es evidencia, y la evidencia no se altera. Las respuestas van a `C:\Lab\Evidencias`.

---

## La Superficie de Ataque del Ordenador

Un atacante que apunta a un ordenador con Windows 11 no ve lo mismo que el usuario que trabaja frente a él. El usuario ve su escritorio, sus documentos y sus aplicaciones. El atacante ve una colección de puntos de entrada posibles — cada uno una oportunidad para establecer acceso, moverse lateralmente o extraer información.

La **superficie de ataque** es el conjunto de todos los vectores posibles mediante los cuales un atacante no autorizado puede intentar entrar, interactuar con, o extraer datos de un sistema.

Pensemos en analogía militar: cuando se establece un perímetro de seguridad alrededor de un campamento, no solo se aseguran las puertas principales. Se identifican todas las vías de entrada posibles — caminos secundarios, ríos, accesos aéreos — y se aplican controles a cada uno. La superficie de ataque de un ordenador funciona igual.

### Los cuatro componentes de la superficie de ataque

**1. Puertos abiertos:** Cada puerto que escucha conexiones de red es una puerta potencial. **Un puerto abierto indica que hay un servicio a la escucha, esperando que un cliente se conecte y le envíe datos conforme a su protocolo.** El servicio interpreta esos datos — y ahí está el riesgo: si tiene una vulnerabilidad, unos datos construidos a propósito se convierten en un punto de entrada.

Para visualizar los puertos que escucha un equipo Windows 11, abrir CMD y ejecutar:

```powershell
netstat -aon
```

Para numerar cada línea:

```powershell
$i = 0; netstat -ano | ForEach-Object { "{0,4}: {1}" -f ++$i, $_ }
```

!!! note "No hace falta elevación para mirar"
    Enumerar puertos es una operación de lectura: funciona con una cuenta estándar. Se necesitan privilegios para *cambiar* la configuración, no para observarla.

Salida real de la VM del curso, recortada (la salida completa supera las 90 líneas; en clase no coincidirá con esta — los PID y los puertos dinámicos cambian en cada arranque):

```text
Conexiones activas

  Proto  Dirección local          Dirección remota        Estado           PID
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       916
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:5040           0.0.0.0:0              LISTENING       4484
  TCP    0.0.0.0:7680           0.0.0.0:0              LISTENING       6760
  TCP    0.0.0.0:49664          0.0.0.0:0              LISTENING       880
  TCP    0.0.0.0:49668          0.0.0.0:0              LISTENING       2960
  TCP    10.0.2.15:139          0.0.0.0:0              LISTENING       4
  TCP    10.0.2.15:49672        20.190.173.65:443      TIME_WAIT       0
  TCP    10.0.2.15:51824        72.154.7.110:443       ESTABLISHED     6760
  TCP    [::]:445               [::]:0                 LISTENING       4
  UDP    0.0.0.0:5355           *:*                                    2036
  UDP    10.0.2.15:137          *:*                                    4
```

Seis lecturas salen de esta tabla — una por columna, y una más al final:

1. **Proto.** 
    - TCP y UDP son los dos protocolos de transporte de Internet, y son los únicos que usan puertos — por eso `netstat` solo lista estos dos (otros protocolos, como ICMP, no aparecen en esta tabla). 
    - TCP establece una conexión antes de intercambiar datos y garantiza entrega y orden: es el transporte de la web, SMB o RDP. 
    - UDP envía datagramas sueltos, sin conexión ni confirmación: DNS, resolución de nombres NetBIOS, streaming.
2. **Dirección local** 
    - Dice *dónde* escucha el servicio. `0.0.0.0` es el comodín que significa «en todas las direcciones IPv4 de esta máquina a la vez», lo que incluye:
        - `127.0.0.1` (*localhost*, alcanzable solo desde el propio equipo) y 
        - `10.0.2.15` (la IP de la interfaz de red, alcanzable desde fuera). 
    - Una IP concreta limita la escucha: `10.0.2.15:139`, el servicio que toma control de ese puerto atiende solo por esa interfaz, y
    - un servicio ligado únicamente a `127.0.0.1` no es alcanzable desde la red.
    - `[::]` es el mismo comodín en IPv6. 
    - Cuanto más amplio el enlace, mayor la exposición.
3. **Dirección remota.** 
    - En una línea `LISTENING`, dado que el puerto origen está a la escucha, la dirección remota sera `0.0.0.0:0` el cual es el valor «aún no hay interlocutor» (el puerto `0` ni siquiera es un puerto válido). 
    - Cuando un cliente se conecta, aparece otra línea con la dirección remota concreta — las `ESTABLISHED` de la salida. 
    - El `*:*` de las líneas UDP dice lo mismo a su manera: como UDP no tiene conexiones, el socket simplemente acepta datagramas de cualquier origen.
4. **Estado.** 
    - `LISTENING` es una puerta abierta esperando clientes. 
    - `ESTABLISHED` es una conversación en curso — la dirección remota dice con quién. 
    - `TIME_WAIT` es una conexión ya cerrada, en periodo de drenaje; nótese su PID `0`: ya no tiene proceso dueño.
    - Las líneas UDP no tienen estado: sin conexión, no hay nada que rastrear.
5. **PID** permite la atribución: qué proceso abre cada puerto. 
    - El PID `4` es siempre el proceso *System* — SMB (445) y NetBIOS (139, 137) los sirve el propio núcleo de Windows, no una aplicación.
6. **El número de puerto no identifica el servicio.** 
    - Los puertos `49664`–`49669` de la salida pertenecen al rango dinámico: el sistema los asigna en cada arranque. 
    - Deducir el servicio por el número solo funciona con las convenciones de IANA para *puertos bien conocidos* (445 SMB, 3389 RDP, 135 RPC...), y cualquier servicio puede configurarse en un puerto arbitrario — el número **sugiere, pero no demuestra**.

`netstat -aon` entrega el PID, pero no el nombre del proceso: cruzarlo exige abrir el Administrador de tareas (pestaña *Detalles*) o ejecutar `tasklist /fi "PID eq 916"`. La práctica de esta clase usa el equivalente en PowerShell, que resuelve puerto, PID y nombre de proceso en una sola consulta.

Con esa herramienta, el Técnico debe poder responder: ¿por qué está abierto este puerto? ¿Qué proceso lo abre? ¿Esa funcionalidad es necesaria en este equipo?

**2. Servicios en ejecución:** Cada servicio de Windows que corre en segundo plano es código que procesa datos, recibe entradas y tiene acceso al sistema operativo. Si un servicio tiene un error de programación (vulnerabilidad), un atacante puede aprovecharlo para ejecutar código malicioso.

**3. Cuentas de usuario:** Cada cuenta de usuario es una identidad que puede ser suplantada. Cuentas con contraseñas débiles, cuentas sin uso activo (huérfanas), o cuentas con privilegios excesivos son vectores de ataque de alto valor para los atacantes.

**4. Software instalado:** Cada aplicación instalada es código adicional que puede contener vulnerabilidades. El software desactualizado es especialmente peligroso porque sus vulnerabilidades ya son públicas y los atacantes las conocen.

!!! example "Aplicación en entorno castrense"
    Un equipo de reconocimiento despliega un puesto de mando avanzado. Antes de conectar el ordenador a la red de unidad, el Técnico de comunicaciones realiza una evaluación rápida de superficie de ataque: ejecuta `netstat -ano` en CMD y verifica qué puertos están escuchando. Identifica que el puerto 445 (SMB) está abierto — necesario para compartir archivos en red, pero también el vector del ransomware WannaCry que en 2017 afectó a hospitales y empresas en todo el mundo. El Técnico confirma que la versión SMBv1 está deshabilitada antes de conectar el equipo. Esta verificación de cinco minutos puede evitar que un incidente de seguridad interrumpa la misión.

### ▸ Práctica 2 — Enumerar la superficie de ataque de la estación

**Tiempo:** 45 minutos.

Los cuatro componentes que se acaban de explicar se van a enumerar ahora sobre la VM, uno por uno. Se usa PowerShell en lugar de `netstat -an` porque permite relacionar cada puerto con el proceso que lo tiene abierto — el dato que de verdad permite justificarlo.

**Paso 1 — Puertos TCP en escucha, con su proceso**

```powershell
Get-NetTCPConnection -State Listen |
    Sort-Object LocalPort |
    Select-Object LocalAddress,
                  LocalPort,
                  OwningProcess,
                  @{Name='Proceso';Expression={
                      (Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue).ProcessName
                  }}
```

**Se espera ver:** una tabla ordenada por puerto, con dirección local, puerto, PID y nombre del proceso.

**Si no aparece:** si algún nombre de proceso queda vacío, conservar el PID como evidencia. Si la consulta entera falla, no avanzar y avisar al instructor.

!!! question "Interpretación"
    Elegir al menos **dos** entradas que requieran justificación. Para cada una: ¿qué proceso la abre? ¿qué función cumpliría en una estación militar? ¿qué información adicional haría falta antes de llamarla vulnerabilidad?

**Paso 2 — Servicios en ejecución, cuentas locales y software instalado**

```powershell
Get-CimInstance Win32_Service -Filter "State='Running'" |
    Select-Object Name, DisplayName, StartMode |
    Sort-Object Name

Get-LocalUser |
    Select-Object Name, Enabled, LastLogon, SID

$RutasSoftware = @(
    'HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*',
    'HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*'
)

Get-ItemProperty $RutasSoftware -ErrorAction SilentlyContinue |
    Where-Object DisplayName |
    Select-Object DisplayName, DisplayVersion, Publisher |
    Sort-Object DisplayName -Unique
```

**Se espera ver:** la lista de servicios en ejecución, las cuentas locales con su SID, y el software instalado con versión y editor.

**Si no aparece:** no sustituir estos cmdlets por comandos que dependan del idioma del sistema. Si `Get-LocalUser` falla o alguna lista queda vacía, detenerse y avisar al instructor.

!!! note "Hallazgo no equivale a vulnerabilidad"
    Un puerto en escucha o un servicio activo es un **hallazgo**, y un hallazgo exige justificación. Solo después de evaluar su necesidad, su configuración y su exposición se determina si constituye una vulnerabilidad. Informar «puerto en escucha»; no informar «vulnerabilidad» sin demostrar la condición que la produce.

!!! tip "Los identificadores no dependen del idioma"
    La columna SID es la que importa. El sistema está en español: la cuenta *Guest* aparece como **Invitado** y el grupo *Administrators* como **Administradores**. Los nombres cambian con el idioma; los SID no. En las prácticas siguientes se identifican las cuentas por su SID.

---

## Windows Security Center

Windows Security Center (también llamado Windows Security o Seguridad de Windows) es el panel de control unificado de seguridad de Windows 11. Proporciona una vista de estado de todas las protecciones activas en el equipo en un solo lugar.

Para abrirlo: menú Inicio → "Seguridad de Windows" (o buscar "Windows Security").

El panel principal muestra seis áreas de protección, cada una representada por un ícono con indicador de color:

| Panel | Qué protege | Relación con CIA |
|-------|------------|-----------------|
| **Virus & threat protection** | Antivirus (Microsoft Defender) y gestión de amenazas | Integridad (previene modificaciones maliciosas) |
| **Account protection** | Contraseña, Windows Hello, opciones de inicio de sesión | Confidencialidad (controla quién accede) |
| **Firewall & network protection** | Estado del cortafuegos para cada perfil de red | Disponibilidad (bloquea ataques de red) |
| **App & browser control** | SmartScreen, protección de reputación, exploit protection | Integridad (previene ejecución de código malicioso) |
| **Device security** | TPM, Secure Boot, Core Isolation | Integridad (garantiza integridad del hardware y arranque) |
| **Device performance & health** | Estado general del sistema, actualizaciones, almacenamiento | Disponibilidad (sistema funciona correctamente) |

### Interpretando los colores

- **Verde con paloma (checkmark):** El área está protegida y configurada correctamente. Sin acción requerida.
- **Amarillo con exclamación:** Hay una recomendación — una configuración podría mejorarse pero el sistema sigue protegido. Revisar y decidir si aplicar la recomendación.
- **Rojo con X:** Hay un problema que requiere acción inmediata. Por ejemplo: protección en tiempo real deshabilitada, definiciones de virus muy desactualizadas, o el firewall está apagado.

La práctica correcta es que un Técnico revise el Windows Security Center al inicio de cada jornada en equipos críticos. Un panel rojo indica que el equipo no debe conectarse a la red de unidad hasta resolver el problema.

### ▸ Práctica 3 — Revisar el panel y contrastarlo con los datos del sistema

**Tiempo:** 20 minutos.

El panel gráfico resume; los cmdlets dan el dato exacto. Un Técnico usa los dos y comprueba que coinciden.

**Paso 1 — Abrir Seguridad de Windows y capturar el panel principal**

```powershell
Start-Process 'windowsdefender:'
```

Revisar el panel principal y guardar una captura con el nombre exacto `C:\Lab\Evidencias\01-seguridad-windows.png`:

1. Pulsar `Win + Shift + S` y seleccionar la zona con el ratón.
2. **Hacer clic en el aviso** que aparece en la esquina inferior derecha. Si ya desapareció, abrir **Recorte y anotación** desde el menú Inicio.
3. En esa ventana, pulsar `Ctrl + S`.
4. Navegar a `C:\Lab\Evidencias`, escribir el nombre exacto y guardar.

!!! warning "El paso 2 es el que se olvida"
    Sin hacer clic en el aviso, la captura se queda solo en el portapapeles y **no existe como archivo**. Comprobarlo con `Test-Path C:\Lab\Evidencias\01-seguridad-windows.png`, que debe devolver `True`.

**Paso 2 — Contrastar el panel con el estado real de Defender y del cortafuegos**

```powershell
Get-MpComputerStatus |
    Format-List AntivirusEnabled,
                RealTimeProtectionEnabled,
                AntivirusSignatureLastUpdated,
                DefenderSignaturesOutOfDate

Get-NetFirewallProfile |
    Format-Table Name, Enabled
```

**Se espera ver:** `AntivirusEnabled` y `RealTimeProtectionEnabled` en `True`, `DefenderSignaturesOutOfDate` en `False`, y los tres perfiles del cortafuegos (Dominio, Privado, Público) en `True`. La fecha de firmas depende del día de la práctica.

**Si no aparece:** en esta Parte 1 **no se corrige nada**. Registrar el hallazgo, detenerse y avisar al instructor. La corrección corresponde a la Parte 2.

!!! question "Verificación cruzada"
    ¿El color que muestra el panel coincide con lo que devuelven los cmdlets? Si el panel está verde pero un dato dice lo contrario, ¿cuál de los dos se informa en el parte, y por qué?

---

## El Principio de Mínimo Privilegio

El principio de mínimo privilegio establece que cada usuario, proceso o sistema debe operar con el mínimo nivel de acceso necesario para cumplir su función — nada más.

Pensemos en analogía militar: en un cuartel, un soldado raso no tiene acceso a los archivos de inteligencia del Estado Mayor. Un conductor no necesita la llave de la armería. Un cocinero no necesita permisos para entrar al centro de comunicaciones. Cada persona tiene acceso exactamente a lo que necesita para su función. Lo mismo aplica a los sistemas informáticos.

En Windows 11, el principio de mínimo privilegio se viola con frecuencia cuando los usuarios trabajan con cuentas de Administrador para tareas cotidianas. Esto significa que:

- Cualquier malware que el usuario ejecute accidentalmente hereda los privilegios de Administrador.
- Un atacante que compromete la sesión del usuario tiene acceso completo al sistema.
- Los cambios accidentales (borrar archivos del sistema, modificar configuración crítica) son posibles e inmediatos.

La práctica correcta es que los Técnicos tengan **dos cuentas**: una cuenta estándar para el trabajo diario (navegar la web, redactar documentos, leer correo) y una cuenta de Administrador separada que solo se usa cuando se necesitan privilegios elevados para tareas específicas.

!!! warning "Riesgo de trabajar siempre como Administrador"
    Si un usuario trabaja con una cuenta de Administrador y hace clic accidentalmente en un adjunto malicioso de correo electrónico, el malware se ejecuta con privilegios de Administrador. Puede deshabilitar el antivirus, instalar puertas traseras persistentes, exfiltrar archivos del sistema, y modificar la política de seguridad. Con una cuenta estándar, el malware está limitado a lo que puede hacer sin privilegios elevados — lo que reduce drásticamente el daño potencial.

La **superficie de privilegio** es el conjunto de recursos del sistema a los que un proceso o usuario tiene acceso autorizado. El objetivo del hardening es minimizar la superficie de privilegio de cada entidad en el sistema.

### ▸ Práctica 4 — Auditar las cuentas locales de la estación

**Tiempo:** 25 minutos.

La lista de cuentas ya se obtuvo en la Práctica 2. Ahora se interpreta. El **RID** es el tramo final del SID e identifica el rol de la cuenta con independencia de su nombre y del idioma del sistema.

```powershell
Get-LocalUser |
    Select-Object Name,
                  Enabled,
                  LastLogon,
                  @{Name='RID';Expression={ ($_.SID.Value -split '-')[-1] }} |
    Sort-Object RID |
    Format-Table -AutoSize
```

| RID | Cuenta | Qué debe cumplir |
|-----|--------|------------------|
| 500 | Administrador integrado | Deshabilitada |
| 501 | Invitado (*Guest*) | Deshabilitada — control CIS L1 nº 1 |
| 503 | DefaultAccount | Deshabilitada, es de sistema |
| 504 | WDAGUtilityAccount | Deshabilitada, es de sistema |
| ≥1000 | Cuentas creadas en el equipo | Cada una exige justificación |

**Se espera ver:** las cuatro cuentas integradas en `Enabled: False`, y varias cuentas con RID ≥ 1000 correspondientes a esta estación.

!!! question "Punto de control"
    Anotar en el parte cada cuenta con RID ≥ 1000: ¿qué función cumpliría? ¿Cuándo inició sesión por última vez? ¿Alguna parece sobrar? La sola existencia de una cuenta no la vuelve no autorizada — hay que argumentarlo.

!!! warning "Lo que no se puede ver sin privilegios"
    Averiguar **a qué grupos pertenece** cada cuenta requiere elevación, y ahí es donde se esconde el defecto más grave de esta estación. Anotar esa limitación en el parte: se resuelve en la Parte 2 con la cuenta `tec_admin`. Que un operador sin privilegios no pueda completar la auditoría es, en sí mismo, el principio de mínimo privilegio funcionando.

---

## Hardening Básico: CIS Benchmark Level 1

Un **benchmark de seguridad** es un conjunto de recomendaciones de configuración documentadas, consensuadas por expertos, diseñadas para reducir la superficie de ataque de un sistema específico. No son opcionales ni aspiracionales — son el estándar base de la industria.

El **Center for Internet Security (CIS)** publica benchmarks para los sistemas operativos, aplicaciones y dispositivos más usados. Para Windows 11, el CIS Benchmark tiene dos niveles:

- **Level 1:** Configuraciones aplicables en cualquier entorno, sin impacto operacional significativo. Todo equipo debe cumplir Level 1.
- **Level 2:** Configuraciones más restrictivas para entornos de alta seguridad. Pueden interferir con algunas funcionalidades comunes — evaluar antes de aplicar en producción.

Para los Técnicos del Ejército, el objetivo inicial es cumplir el Level 1. Los seis controles más relevantes para Windows 11 en entorno militar son:

| Control CIS L1 | Por qué importa | Cómo verificar |
|----------------|-----------------|---------------|
| **1. Deshabilitar la cuenta Invitado** | La cuenta Invitado (*Guest*) permite acceso sin contraseña. Es el primer vector que un atacante prueba en un sistema nuevo. | Localizarla por **RID 501**, no por nombre: `Get-LocalUser \| Where-Object { ($_.SID.Value -split '-')[-1] -eq '501' }` — debe mostrar `Enabled: False` |
| **2. Contraseña mínima de 14 caracteres** | Contraseñas cortas son vulnerables a ataques de fuerza bruta. 14 caracteres con complejidad tardan años en romperse con hardware actual. | `net.exe accounts` — la línea *Longitud mínima de contraseña* debe ser ≥ 14 |
| **3. Umbral de bloqueo: 5 intentos** | Limita los intentos de adivinar contraseñas. Después de 5 intentos fallidos, la cuenta se bloquea. | `net.exe accounts` — la línea *Umbral de bloqueo* debe ser 5, no `Nunca` |
| **4. Deshabilitar SMBv1** | SMBv1 es el protocolo que explotó WannaCry en 2017 para propagarse entre equipos. No tiene uso legítimo en redes modernas. | `Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol` — debe mostrar `State: Disabled` |
| **5. Habilitar auditoría de inicio de sesión** | Registra cada intento de inicio de sesión (exitoso y fallido) en el Visor de eventos. Esencial para detectar ataques de fuerza bruta. | `auditpol.exe /get /subcategory:"{0CCE9215-69AE-11D9-BED3-505054503030}"` — debe indicar *Correcto y error* |
| **6. Windows Update al día** | Los parches de seguridad corrigen vulnerabilidades conocidas. Un sistema sin parches recientes tiene vulnerabilidades públicas que cualquier atacante puede explotar. | **Configuración → Windows Update → Buscar actualizaciones** — debe mostrar «Está todo actualizado» |

!!! tip "Por qué un GUID en el control 5"
    El nombre de la subcategoría de auditoría está traducido en un Windows en español, así que `auditpol` no la encuentra si se la pide por nombre en inglés. El GUID `{0CCE9215-…}` identifica *Inicio de sesión* en cualquier idioma. Es el mismo principio que usar el RID 501 en lugar de «Guest».

Estos seis controles son el mínimo absoluto antes de conectar cualquier equipo a la red de unidad. No son opcionales.

### ▸ Práctica 5 — Levantar el parte de conformidad CIS L1

**Tiempo:** 25 minutos.

Se recorre la tabla de los seis controles y se anota el estado **observado** de cada uno. Sin elevación no se pueden comprobar todos, y eso también se registra: un parte honesto distingue «cumple», «no cumple» y «no verificable con los privilegios disponibles».

**Paso 1 — Control 1: cuenta Invitado deshabilitada**

```powershell
Get-LocalUser |
    Where-Object { ($_.SID.Value -split '-')[-1] -eq '501' } |
    Format-List Name, Enabled, SID
```

**Se espera ver:** una sola cuenta —en un sistema en español se llama **Invitado**— con `Enabled: False`.

**Paso 2 — Control 6: estado de Windows Update**

Abrir **Configuración → Windows Update** y anotar la fecha de la última comprobación y si hay actualizaciones pendientes. No instalar nada durante la clase.

**Paso 3 — Levantar el parte de conformidad**

Generar la plantilla y completarla en el Bloc de notas:

```powershell
@'
PARTE DE CONFORMIDAD CIS BENCHMARK LEVEL 1
Estacion: VM-WIN-SI    Operador: operador_alumno    Sesion: sin elevacion

Estados admitidos: CUMPLE / NO CUMPLE / NO VERIFICABLE SIN ELEVACION

1. Cuenta Invitado (RID 501) deshabilitada ....
   Comprobado con: Get-LocalUser, filtrando por RID 501

2. Longitud minima de contrasena 14 ..........
   Comprobado con:

3. Umbral de bloqueo 5 intentos ..............
   Comprobado con:

4. SMBv1 deshabilitado .......................
   Comprobado con:

5. Auditoria de inicio de sesion activada ....
   Comprobado con:

6. Windows Update al dia .....................
   Comprobado con: Configuracion > Windows Update

OBSERVACIONES:
'@ | Set-Content C:\Lab\Evidencias\01-conformidad-CIS.txt -Encoding UTF8

notepad.exe C:\Lab\Evidencias\01-conformidad-CIS.txt
```

Rellenar los seis estados y **guardar antes de cerrar**. Los controles que no se puedan comprobar con la sesión actual se marcan `NO VERIFICABLE SIN ELEVACION` — no se dejan en blanco ni se dan por buenos.

**Paso 4 — Revisar la evidencia y cerrar la transcripción**

```powershell
Get-Content C:\Lab\Evidencias\01-CIA.txt
Get-Content C:\Lab\Evidencias\01-conformidad-CIS.txt
Get-ChildItem C:\Lab\Evidencias | Format-Table Name, Length, LastWriteTime -AutoSize
Stop-Transcript
```

**Se espera ver:** los dos partes completos, un listado con los cuatro archivos de evidencia, y un mensaje que confirme el cierre de `01-reconocimiento.txt`.

**Si no aparece:** si algún parte está vacío, volver a abrirlo, completarlo y repetir el paso. Si falta la captura `.png`, revisar el procedimiento de guardado antes de cerrar la transcripción.

!!! question "Cierre"
    1. Cuatro de los seis controles no se pudieron verificar. ¿Es eso un fallo del procedimiento o el resultado esperado? ¿Qué dice sobre el nivel de privilegio con el que debe trabajar un operador en su jornada normal?
    2. Clasificar los seis controles CIS como **preventivos**, **detectivos** o **correctivos**. ¿Cuántos de cada tipo hay? ¿Qué revela ese reparto sobre lo que el benchmark prioriza?

---

## Puesta en común de hallazgos

**Tiempo:** 15 minutos.

Cada alumno informa **un** elemento de su parte que requiera justificación, y responde:

1. ¿Es un dato observado o una conclusión propia?
2. ¿Qué función podría cumplir ese elemento en la estación?
3. ¿Qué evidencia adicional permitiría decidir si es necesario o riesgoso?

!!! warning "Lenguaje técnico preciso"
    Se informa «puerto en escucha» o «servicio activo» como hallazgo. No se informa una vulnerabilidad sin demostrar la condición que la produce. En un parte real, esa diferencia decide si se moviliza o no a un equipo de respuesta.

### Evidencia que debe quedar en la estación

Al terminar la Parte 1, en `C:\Lab\Evidencias` deben existir:

- [ ] `01-reconocimiento.txt` — transcripción completa, con marca de cierre
- [ ] `01-CIA.txt` — los cuatro incidentes clasificados y justificados
- [ ] `01-seguridad-windows.png` — captura del panel de Seguridad de Windows
- [ ] `01-conformidad-CIS.txt` — los seis controles con su estado observado

Comprobación final:

```powershell
'01-reconocimiento.txt','01-CIA.txt','01-seguridad-windows.png','01-conformidad-CIS.txt' |
    ForEach-Object {
        [pscustomobject]@{
            Archivo = $_
            Existe  = Test-Path "C:\Lab\Evidencias\$_"
        }
    } | Format-Table -AutoSize
```

Las cuatro filas deben decir `True`. Estos archivos son el punto de partida de la Parte 2: **no se borran ni se mueven**.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Un Técnico de comunicaciones recibe un laptop de campo que estuvo almacenado durante seis meses. Antes de conectarlo a la red de unidad, debe verificar su postura de seguridad. El procedimiento correcto:

    **Paso 1 — Verificar Windows Security Center:** Abre el panel y revisa los seis paneles. Si alguno está en rojo o amarillo, no conecta el equipo a la red hasta resolverlo.

    **Paso 2 — Verificar Windows Update:** **Configuración → Windows Update → Buscar actualizaciones**. Descarga e instala todos los parches pendientes. Un laptop guardado seis meses puede tener docenas de parches de seguridad atrasados, incluyendo correcciones para vulnerabilidades críticas.

    **Paso 3 — Auditar cuentas:** Ejecuta `Get-LocalUser` en PowerShell para listar todas las cuentas. Verifica que la cuenta Guest esté deshabilitada y que no existan cuentas desconocidas con privilegios de Administrador.

    **Paso 4 — Verificar SMBv1:** Confirma que el protocolo obsoleto está deshabilitado. Si está habilitado, ejecuta el comando de deshabilitación antes de conectar a la red.

    **Paso 5 — Verificar antivirus:** Actualiza las definiciones de Defender (`Update-MpSignature`) y ejecuta un escaneo rápido (`Start-MpScan -ScanType QuickScan`).

    Solo después de completar estos cinco pasos el Técnico conecta el equipo a la red de unidad. Este procedimiento toma aproximadamente 20 minutos y puede evitar que un equipo comprometido sea usado como puente de entrada para un atacante en la red.

---

## Resumen

1. Un **control de seguridad** es toda medida que reduce un riesgo, y se clasifica por el momento en que actúa: **preventivo** (impide), **detectivo** (deja constancia) y **correctivo** (repara). Los tres son necesarios.
2. La **Triada CIA** (Confidencialidad, Integridad, Disponibilidad) es el marco de referencia para evaluar toda amenaza y toda protección durante el curso.
3. La **superficie de ataque** incluye puertos abiertos, servicios en ejecución, cuentas de usuario y software instalado — reducirla es el objetivo del hardening.
4. El **Windows Security Center** proporciona una vista de estado unificada de todas las protecciones; un panel rojo requiere acción antes de conectar el equipo a la red.
5. El **principio de mínimo privilegio** dicta que nadie — ni usuarios ni procesos — debe tener más acceso del estrictamente necesario para su función.
6. El **CIS Benchmark Level 1** establece seis controles mínimos para Windows 11: deshabilitar la cuenta Invitado, contraseña de 14 caracteres, umbral de bloqueo en 5 intentos, deshabilitar SMBv1, auditoría de inicio de sesión activada, y Windows Update al día.
7. Los identificadores del sistema (**SID**, **RID**, **GUID**) no dependen del idioma; los nombres sí. En un Windows en español, `Guest` es `Invitado` y `Administrators` es `Administradores` — por eso se identifica por SID.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **CIS Benchmarks (sitio oficial):** https://www.cisecurity.org/cis-benchmarks — Descargar el benchmark gratuito para Windows 11. Requiere registro. El documento tiene cientos de controles — los Level 1 están marcados explícitamente.
- **Windows Security Center — Microsoft Learn:** https://learn.microsoft.com/es-es/windows/security/operating-system-security/system-security/windows-defender-security-center/windows-defender-security-center — Guía oficial con descripción detallada de cada panel.
- **Triada CIA en contexto de operaciones militares (NIST SP 800-53):** https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final — El estándar federal de EE.UU. que implementa la Triada CIA en controles específicos para sistemas de defensa.

---

*Siguiente: [Controles Defensivos (Parte 2)](controles-defensivos-p2.md)*
