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

<hr style="height:3px; background-color:black;">

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

<hr style="height:3px; background-color:black;">

## La Superficie de Ataque del Ordenador

Un atacante que apunta a un ordenador con Windows 11 no ve lo mismo que el usuario que trabaja frente a él. El usuario ve su escritorio, sus documentos y sus aplicaciones. El atacante ve una colección de puntos de entrada posibles — cada uno una oportunidad para establecer acceso, moverse lateralmente o extraer información.

La **superficie de ataque** es el conjunto de todos los vectores posibles mediante los cuales un atacante no autorizado puede intentar entrar, interactuar con, o extraer datos de un sistema.

Pensemos en analogía militar: cuando se establece un perímetro de seguridad alrededor de un campamento, no solo se aseguran las puertas principales. Se identifican todas las vías de entrada posibles — caminos secundarios, ríos, accesos aéreos — y se aplican controles a cada uno. La superficie de ataque de un ordenador funciona igual.

### Los cuatro componentes de la superficie de ataque

**1. Puertos abiertos:** Cada puerto que escucha conexiones de red es una puerta potencial. **Un puerto abierto indica que hay un servicio a la escucha, esperando que un cliente se conecte y le envíe datos conforme a su protocolo.** El servicio interpreta esos datos — y ahí está el riesgo: si tiene una vulnerabilidad, unos datos construidos a propósito se convierten en un punto de entrada.

Para visualizar los puertos que escucha un equipo Windows 11, ejecutar en la ventana de PowerShell ya abierta:

```powershell
netstat -aon
```

Para numerar cada línea:

```powershell
$i = 0; netstat -aon | ForEach-Object { "{0,4}: {1}" -f ++$i, $_ }
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
    - Cuanto más amplio el vínculo (*binding*), mayor la exposición.
3. **Dirección remota.** 
    - En una línea `LISTENING`, dado que el puerto está a la escucha, la dirección remota será `0.0.0.0:0`, el valor que significa «aún no hay interlocutor» (el puerto `0` ni siquiera es un puerto válido).
    - Una conexión en curso muestra la dirección remota concreta — las `ESTABLISHED` de la salida. Ojo: `ESTABLISHED` no distingue quién inició la conexión. Las dos de esta salida son conexiones **salientes** de la VM actuando como cliente, y la pista es el puerto local: `51824` pertenece al rango dinámico. Un cliente externo entrante habría llegado *a* uno de los puertos en escucha (139, 445...).
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

!!! question "Análisis guiado — ¿qué entradas investigarías primero?"
    La siguiente salida es **simulada, pero técnicamente plausible**. Corresponde a una estación de trabajo que no debería ofrecer servicios al exterior ni aceptar administración remota. El operador solo reconoce haber abierto el navegador para consultar el portal de instrucción.

    ```text
    Proto  Dirección local          Dirección remota         Estado           PID
    TCP    0.0.0.0:135             0.0.0.0:0               LISTENING       916
    TCP    0.0.0.0:445             0.0.0.0:0               LISTENING       4
    TCP    127.0.0.1:49673         0.0.0.0:0               LISTENING       6120
    TCP    0.0.0.0:3389            0.0.0.0:0               LISTENING       1180
    TCP    0.0.0.0:4444            0.0.0.0:0               LISTENING       7312
    TCP    10.0.2.15:51824         192.0.2.20:443          ESTABLISHED     6760
    TCP    10.0.2.15:51891         203.0.113.77:8081       ESTABLISHED     7312
    TCP    10.0.2.15:3389          198.51.100.46:53122     ESTABLISHED     1180
    TCP    10.0.2.15:49672         192.0.2.55:443          TIME_WAIT       0
    ```

    Las direcciones `192.0.2.0/24`, `198.51.100.0/24` y `203.0.113.0/24` están reservadas para documentación: representan equipos externos ficticios, no destinos reales identificados como maliciosos.

    Trabajar en parejas. Cada respuesta debe señalar la fila, el endpoint o el PID exacto que la sustenta:

    1. ¿Qué PID aparece simultáneamente en un socket `LISTENING` y en una conexión `ESTABLISHED`?
    2. ¿Cuáles son los dos endpoints asociados con ese PID? Escriban el endpoint local que escucha y el par local-remoto de la conexión establecida.
    3. Entre `0.0.0.0:4444` y `127.0.0.1:49673`, ¿cuál tiene mayor exposición a la red y qué diferencia exacta entre ambas direcciones lo demuestra?
    4. ¿Qué fila demuestra que RDP no solo está disponible, sino que existe una sesión activa? Copien sus endpoints local y remoto.
    5. ¿Cuál es la dirección IP y el puerto de origen del equipo que mantiene esa sesión RDP?
    6. ¿Qué fila representa una conexión que ya terminó y por qué no puede atribuirse a un proceso activo mediante su PID?
    7. ¿Qué puerto en escucha está asociado al PID `4` y qué servicio de Windows sugiere esa combinación?
    8. ¿Qué conexión `ESTABLISHED` es más compatible con la navegación web reconocida por el operador y qué PID habría que comprobar para confirmarlo?
    9. Escriban el comando exacto de `tasklist` para identificar el proceso que mantiene tanto el puerto `4444` en escucha como la conexión hacia el puerto remoto `8081`.
    10. A partir únicamente de esta salida, ¿cuál de estas dos conclusiones es técnicamente válida sobre el PID `7312`: «malware confirmado» o «actividad anómala que exige atribución»? Indiquen el dato mínimo que todavía falta obtener.

    **Regla de análisis:** un puerto o una conexión inusual inicia una investigación; no demuestra por sí solo que exista malware. La fuerza del hallazgo aumenta cuando varias pistas coinciden — por ejemplo, un puerto no autorizado, una conexión externa activa y un mismo PID.

---
**2. Servicios en ejecución:** Cada servicio de Windows que corre en segundo plano es código que procesa datos, recibe entradas y tiene acceso al sistema operativo. Si un servicio tiene un error de programación (vulnerabilidad), un atacante puede aprovecharlo para ejecutar código malicioso.

Un **servicio** es un programa que Windows arranca y gestiona en segundo plano, sin ventana propia y muchas veces sin que ningún usuario haya iniciado sesión. Para enumerar los que están corriendo, con los tres datos que un análisis de superficie necesita:

```powershell
Get-CimInstance Win32_Service -Filter "State='Running'" |
    Select-Object Name, StartMode, StartName, PathName |
    Sort-Object Name
```

!!! note "Por qué `Win32_Service` y no `Get-Service`"
    `Get-Service` lista nombres y estados, pero no dice **con qué cuenta corre** el servicio ni **qué ejecutable** hay detrás — y esos dos datos son justamente los que importan. Igual que con los puertos, enumerar servicios es lectura: no requiere elevación.

Salida típica de un Windows 11, recortada (la lista completa supera los 70 servicios; los directorios de versión y el orden varían por equipo):

```text
Name          StartMode  StartName                    PathName
----          ---------  ---------                    --------
Dhcp          Auto       NT Authority\LocalService    C:\Windows\system32\svchost.exe -k LocalServiceNetworkRestricted -p
Dnscache      Auto       NT AUTHORITY\NetworkService  C:\Windows\system32\svchost.exe -k NetworkService -p
LanmanServer  Auto       LocalSystem                  C:\Windows\system32\svchost.exe -k netsvcs -p
Spooler       Auto       LocalSystem                  C:\Windows\System32\spoolsv.exe
TermService   Manual     NT Authority\NetworkService  C:\Windows\System32\svchost.exe -k NetworkService
WinDefend     Auto       LocalSystem                  "C:\ProgramData\Microsoft\Windows Defender\Platform\...\MsMpEng.exe"
wuauserv      Manual     LocalSystem                  C:\Windows\system32\svchost.exe -k netsvcs -p
```

Cuatro lecturas salen de esta tabla:

1. **StartMode.**
    - `Auto` significa que el servicio arranca con el sistema, sin intervención de nadie: es superficie de ataque **permanente**.
    - `Manual` arranca solo cuando algo lo solicita: superficie bajo demanda.
    - Un servicio en `Auto` que el equipo no necesita es exposición gratuita — el primer candidato del hardening.
2. **StartName.**
    - Es la cuenta con la que corre el servicio, y por tanto **el privilegio que hereda quien lo comprometa**.
    - `LocalSystem` es más poderoso que Administrador: un servicio así comprometido entrega el equipo completo.
    - `LocalService` y `NetworkService` son cuentas deliberadamente limitadas: el principio de mínimo privilegio aplicado a procesos, no solo a personas.
3. **PathName.**
    - Dice qué ejecutable es realmente el servicio. Muchos comparten `svchost.exe`, cada uno con su grupo `-k`; otros, como `Spooler`, tienen ejecutable propio (`spoolsv.exe`).
    - Heurística de triage: un servicio cuyo ejecutable vive **fuera** de `C:\Windows` o `C:\Program Files` merece investigación antes que ningún otro.
4. **El puente con los puertos.**
    - `netstat -aon` entrega un PID, pero un mismo `svchost.exe` aloja varios servicios — el PID solo no dice *cuál*.
    - `tasklist /svc /fi "PID eq 916"` lista los servicios que viven dentro de ese proceso. En el ejercicio simulado de la sección anterior, este comando es el que revelaría que el PID `1180` del puerto 3389 corresponde a `TermService` — Escritorio remoto.

La misma información existe en gráfico: `services.msc` (o **Administración de equipos → Servicios**). Como con Seguridad de Windows, el panel resume y el cmdlet da el dato exacto.

!!! question "Análisis guiado — ¿qué servicio merece investigación?"
    La siguiente salida es **simulada, pero técnicamente plausible**. Todos los servicios mostrados están en ejecución en una estación de trabajo. La unidad no ha autorizado herramientas de soporte remoto ni cuentas personales para ejecutar servicios; la estación tampoco tiene impresora asignada.

    ```text
    Name           StartMode  StartName       PathName
    ----           ---------  ---------       --------
    WinDefend      Auto       LocalSystem     "C:\ProgramData\Microsoft\Windows Defender\Platform\...\MsMpEng.exe"
    wuauserv       Manual     LocalSystem     C:\Windows\system32\svchost.exe -k netsvcs -p
    Spooler        Auto       LocalSystem     C:\Windows\System32\spoolsv.exe
    WinUpdateSvc   Auto       LocalSystem     C:\Users\Public\svchost.exe -service
    RemoteSupport  Auto       LocalSystem     C:\Program Files\Remote Support\agent.exe -service
    LogCollector   Auto       .\operador_temp  "C:\Program Files\Log Collector\collector.exe" -service
    ```

    Una ruta de ejecutable que contiene espacios debe encerrar entre comillas la parte correspondiente al archivo. Sin ellas, la separación entre el ejecutable y sus argumentos queda ambigua: por ejemplo, antes de llegar a `C:\Program Files\...`, Windows puede intentar interpretar `C:\Program.exe` como ejecutable.

    Responder con el nombre, campo, valor o comando exacto solicitado:

    1. ¿Qué servicio intenta parecer un componente de Windows Update, pero ejecuta un archivo fuera de `C:\Windows` y `C:\Program Files`?
    2. ¿Qué combinación exacta de `StartMode` y `StartName` hace que ese servicio obtenga persistencia desde el arranque y privilegios máximos?
    3. ¿Cuál es la ruta completa del ejecutable que contradice el nombre aparentemente legítimo de ese servicio?
    4. ¿Qué servicio contiene espacios en `PathName`, pero no encierra entre comillas la ruta de su ejecutable?
    5. ¿Qué nombre de ejecutable podría intentar interpretar Windows primero debido a esa ruta ambigua?
    6. ¿Qué servicio se ejecuta con una cuenta local creada para un usuario temporal, y cuál es esa cuenta?
    7. ¿Qué columna demuestra la identidad con la que corre ese servicio, sin demostrar que la persona haya iniciado una sesión interactiva?
    8. ¿Qué servicio puede ser legítimo, pero aumenta innecesariamente la superficie de ataque según el contexto de una estación sin impresora?
    9. Escriban el comando exacto para consultar únicamente `WinUpdateSvc`.
    10. ¿Esta salida permite afirmar «malware confirmado» para `WinUpdateSvc`? Respondan sí o no e indiquen una evidencia mínima que todavía faltaría obtener del ejecutable.

    **Regla de análisis:** un nombre parecido al de Windows no vuelve legítimo a un servicio. Se correlacionan nombre, ubicación del ejecutable, modo de inicio, cuenta de ejecución y necesidad operativa antes de emitir una conclusión.

---

**3. Cuentas de usuario:** Cada cuenta de usuario es una identidad que puede ser suplantada. Cuentas con contraseñas débiles, cuentas sin uso activo (huérfanas), o cuentas con privilegios excesivos son vectores de ataque de alto valor para los atacantes.

A diferencia de un puerto o un servicio, una cuenta no es código que pueda tener errores: es una **llave**. El atacante no la explota — la usa. Para enumerar las cuentas locales del equipo:

```powershell
Get-LocalUser |
    Select-Object Name, Enabled, LastLogon, SID
```

Salida típica de un Windows 11 (los SID cambian en cada equipo; aquí se recortan por legibilidad):

```text
Name               Enabled LastLogon           SID
----               ------- ---------           ---
Administrador      False                       S-1-5-21-3814504823-909563556-1708538438-500
DefaultAccount     False                       S-1-5-21-…-503
Invitado           False                       S-1-5-21-…-501
operador_alumno    True    15/08/2026 8:12:04  S-1-5-21-…-1001
WDAGUtilityAccount False                       S-1-5-21-…-504
```

Cuatro lecturas salen de esta tabla:

1. **Enabled.**
    - Una cuenta deshabilitada no está borrada: existe, conserva su perfil y puede rehabilitarse. Lo que impide es **iniciar sesión** — un control preventivo.
    - Las cuentas integradas de Windows (las cuatro de la salida sin fecha de sesión) deben estar en `False`; ninguna es para uso diario.
2. **LastLogon.**
    - Una cuenta habilitada con `LastLogon` vacío o muy antiguo es candidata a **huérfana**: nadie la usa, nadie la vigila, y sigue siendo una llave válida.
    - El caso inverso también informa: una fecha de sesión reciente en una cuenta que nadie reconoce es un hallazgo prioritario.
3. **SID.**
    - La estructura es fija: `S-1-5-21-<identificador de la máquina>-<RID>`. El tramo final —el **RID**— identifica el rol de la cuenta: `500` Administrador integrado, `501` Invitado, `≥1000` cuentas creadas en este equipo.
    - El nombre depende del idioma (`Guest` aquí se llama `Invitado`); el SID no. Por eso toda verificación seria filtra por RID, nunca por nombre — la Práctica 4 audita la estación exactamente así.
4. **Lo que esta tabla no muestra.**
    - Ni la **fortaleza de la contraseña** (la gobierna la política del equipo — controles 2 y 3 del CIS L1, más adelante) ni los **grupos** a los que pertenece cada cuenta.
    - Es decir: los tres vectores del párrafo inicial no se ven igual. La huérfana se detecta aquí (`LastLogon`); la contraseña débil y el privilegio excesivo exigen otras consultas. Una lista de cuentas «limpia» no autoriza a concluir que las cuentas están bien.

La misma información existe en gráfico: `lusrmgr.msc` (**Usuarios y grupos locales**, ediciones Pro). Como siempre: el panel resume, el cmdlet da el dato exacto.

**4. Software instalado:** Cada aplicación instalada es código adicional que puede contener vulnerabilidades. El software desactualizado es especialmente peligroso porque sus vulnerabilidades ya son públicas y los atacantes las conocen.

Un servicio corre; una aplicación instalada puede pasar meses sin abrirse. Aun así cuenta: es código residente en el disco, con sus bibliotecas y sus asociaciones de archivo. Si su versión tiene una vulnerabilidad publicada, el atacante no necesita encontrarla abierta — le basta con lograr que se abra: un documento adjunto, un enlace, un archivo en una memoria USB. Por eso el software instalado es superficie de ataque **se use o no**.

Windows anota cada programa instalado en el Registro, en dos ramas (aplicaciones de 64 y de 32 bits). Para leerlas, en la ventana de PowerShell ya abierta:

```powershell
$RutasSoftware = @(
    'HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*',
    'HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*'
)

Get-ItemProperty $RutasSoftware -ErrorAction SilentlyContinue |
    Where-Object DisplayName |
    Select-Object DisplayName, DisplayVersion, Publisher |
    Sort-Object DisplayName -Unique
```

Salida real de la VM del curso — una imagen recién instalada:

```text
DisplayName                              DisplayVersion Publisher
-----------                              -------------- ---------
Microsoft Edge                           151.0.4129.78  Microsoft Corporation
Oracle VirtualBox Guest Additions 7.2.14 7.2.14.174565  Oracle and/or its affi…
WebView2 Runtime de Microsoft Edge       151.0.4129.78  Microsoft Corporation
```

Cuatro lecturas salen de esta lista:

1. **DisplayVersion — la columna que convierte el inventario en evaluación.**
    - El nombre solo dice *qué* hay; la versión dice *qué vulnerabilidades públicas le aplican*. Las bases de datos de vulnerabilidades (CVE) se consultan exactamente con ese par: producto + versión.
    - De aquí sale la definición operativa de «desactualizado»: existe una versión posterior que corrige vulnerabilidades conocidas. Sin la versión anotada, esa pregunta ni siquiera puede formularse.
2. **Publisher — atribución, otra vez.**
    - Igual que un puerto se atribuye a un proceso y un servicio a una cuenta, cada programa se atribuye a un editor. Una entrada sin editor, o de un editor que nadie reconoce, es un hallazgo que requiere investigación.
3. **Lo que esta lista no muestra.**
    - Esas dos ramas cubren lo instalado **para todo el equipo**. Quedan fuera: lo instalado solo para el usuario actual (rama `HKCU:` equivalente), las aplicaciones de la Tienda de Windows (se enumeran con `Get-AppxPackage`), y el software **portable** — un ejecutable copiado a una carpeta no pasa por instalador y no deja entrada en el Registro.
    - Es decir: este inventario es necesario pero no completo. Un atacante que quiera pasar desapercibido preferirá justamente la vía que no deja registro.
4. **Tres entradas no significan «poca superficie».**
    - Esta imagen está recién instalada; una estación de trabajo real acumula decenas de entradas. La disciplina es la misma a cualquier escala: cada entrada exige poder responder *para qué está y quién responde por ella*.
    - Y el inventario es prerrequisito de todo lo demás: **no se puede parchear lo que no se sabe que existe.** El control 6 del CIS L1 (Windows Update, más adelante) mantiene al día el sistema; las aplicaciones de terceros son responsabilidad de quien administra el inventario.

!!! warning "El comando que no hay que usar: `Win32_Product`"
    En internet abunda la receta `Get-CimInstance Win32_Product`. No usarla: enumerar esa clase dispara una verificación de consistencia del instalador MSI sobre **cada** paquete — es lenta y puede reconfigurar software solo por haber consultado. La vía del Registro es lectura pura y no toca nada.

La misma información existe en gráfico: **Configuración → Aplicaciones → Aplicaciones instaladas**. Como siempre: el panel resume, el Registro da el dato exacto — con la versión completa y exportable al parte.

!!! example "Aplicación en entorno castrense"
    Un equipo de reconocimiento despliega un puesto de mando avanzado. Antes de conectar el ordenador a la red de unidad, el Técnico de comunicaciones realiza una evaluación rápida de superficie de ataque: ejecuta `netstat -aon` y verifica qué puertos están escuchando. Identifica que el puerto 445 (SMB) está abierto — necesario para compartir archivos en red, pero también el vector del ransomware WannaCry que en 2017 afectó a hospitales y empresas en todo el mundo. El Técnico confirma que la versión SMBv1 está deshabilitada antes de conectar el equipo. Esta verificación de cinco minutos puede evitar que un incidente de seguridad interrumpa la misión.

### ▸ Práctica 2 — Enumerar la superficie de ataque de la estación

**Tiempo:** 45 minutos.

Los cuatro componentes que se acaban de explicar se van a enumerar ahora sobre la VM, uno por uno. Se usa PowerShell en lugar de `netstat -aon` porque permite relacionar cada puerto con el proceso que lo tiene abierto — el dato que de verdad permite justificarlo.

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
    Elegir **dos** entradas de la tabla: una ligada a `0.0.0.0` (o `[::]`) y otra ligada a `127.0.0.1`. Para cada una responder, en este orden:

    1. **Atribución.** ¿Qué proceso la abre? Si es `svchost`, el nombre no basta: ejecutar `tasklist /svc /fi "PID eq <pid>"` y anotar el servicio concreto.
    2. **Exposición.** Según su dirección local, ¿quién puede alcanzar este puerto — cualquier equipo de la red, o solo procesos de esta misma máquina? ¿Cambia eso la urgencia del hallazgo?
    3. **Necesidad.** ¿El rol de esta estación requiere esa función? Si no se sabe, ¿a quién se le pregunta o qué documento lo establece?
    4. **Veredicto de parte.** Redactar una línea: «hallazgo justificado» o «requiere investigación» — con su porqué. La palabra *vulnerabilidad* solo se usa si se demostró necesidad ausente, configuración incorrecta o exposición indebida.

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
| **1. Deshabilitar la cuenta Invitado** | La cuenta Invitado (*Guest*) permite acceso sin contraseña. Es el primer vector que un atacante prueba en un sistema nuevo. | Localizarla por **RID 501**, no por nombre. El listado de la Práctica 4 ya la muestra: la fila con RID `501` debe tener `Enabled: False` |
| **2. Contraseña mínima de 14 caracteres** | Contraseñas cortas son vulnerables a ataques de fuerza bruta. 14 caracteres con complejidad tardan años en romperse con hardware actual. | `net.exe accounts` — la línea *Longitud mínima de contraseña* debe ser ≥ 14 |
| **3. Umbral de bloqueo: 5 intentos** | Limita los intentos de adivinar contraseñas. Después de 5 intentos fallidos, la cuenta se bloquea. | `net.exe accounts` — la línea *Umbral de bloqueo* debe ser 5, no `Nunca` |
| **4. Deshabilitar SMBv1** | SMBv1 es el protocolo que explotó WannaCry en 2017 para propagarse entre equipos. No tiene uso legítimo en redes modernas. | `Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol` — debe mostrar `State: Disabled` |
| **5. Habilitar auditoría de inicio de sesión** | Registra cada intento de inicio de sesión (exitoso y fallido) en el Visor de eventos. Esencial para detectar ataques de fuerza bruta. | `auditpol.exe /get /subcategory:"{0CCE9215-69AE-11D9-BED3-505054503030}"` — debe indicar *Correcto y error* |
| **6. Windows Update al día** | Los parches de seguridad corrigen vulnerabilidades conocidas. Un sistema sin parches recientes tiene vulnerabilidades públicas que cualquier atacante puede explotar. | **Configuración → Windows Update → Buscar actualizaciones** — debe mostrar «Está todo actualizado» |

!!! tip "Por qué un GUID en el control 5"
    El nombre de la subcategoría de auditoría está traducido en un Windows en español, así que `auditpol` no la encuentra si se la pide por nombre en inglés. El GUID `{0CCE9215-…}` identifica *Inicio de sesión* en cualquier idioma. Es el mismo principio que usar el RID 501 en lugar de «Guest».

    ¿De dónde sale ese GUID? No es de esta máquina: es una **constante definida por Microsoft**, idéntica en toda instalación de Windows desde Vista. La lista completa se puede consultar en el propio equipo:

    ```powershell
    auditpol.exe /list /subcategory:* /v
    ```

    Ese comando imprime cada subcategoría de auditoría con su nombre (en el idioma del sistema) y su GUID. Ahí aparece `{0CCE9215-69AE-11D9-BED3-505054503030}` junto a *Inicio de sesión*.

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
