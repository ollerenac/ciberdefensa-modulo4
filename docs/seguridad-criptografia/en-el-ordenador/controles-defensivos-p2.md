---
# Horas asignadas: 1 hr
# Tipo: Teoría + Laboratorio
---

# Controles Defensivos en el Ordenador (Parte 2)

> **Duración:** 1 hora | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En el Ordenador | Continuación de Parte 1

En la Parte 1 establecimos el marco conceptual: la Triada CIA como referencia, la superficie de ataque como lo que el atacante ve, el Windows Security Center como panel de estado, el principio de mínimo privilegio y los seis controles CIS L1 que todo equipo debe cumplir. En esta Parte 2 aplicamos esos conceptos con comandos concretos: auditamos las cuentas locales del equipo, revisamos la política de contraseñas y deshabilitamos los servicios que amplían la superficie de ataque sin aportar valor a la misión.

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Auditar las cuentas de usuario locales de un equipo Windows 11 usando PowerShell e identificar cuentas con privilegios excesivos o sin uso.
- Verificar y configurar la política de contraseñas local siguiendo las recomendaciones CIS Level 1.
- Deshabilitar servicios innecesarios (Print Spooler, Remote Registry, SMBv1) usando PowerShell para reducir la superficie de ataque.
- Habilitar la auditoría de inicio de sesión y dejar constancia verificable de cada cambio aplicado.

---

## Entorno de laboratorio

Esta parte **corrige** la estación que la Parte 1 se limitó a observar. Cambia todo respecto a la sesión anterior: aquí sí se modifica el sistema, y por tanto se trabaja **con privilegios elevados**.

!!! danger "Esta parte modifica el sistema"
    En la Parte 1 la consigna era observar sin tocar. Aquí se aplican cambios reales sobre la estación. Cada modificación se hace **después** de haber registrado el estado previo, nunca antes. Un cambio sin línea base es un cambio que no se puede auditar ni revertir.

| Requisito | Verificación |
|-----------|-------------|
| La Parte 1 está completa | Existen los cuatro archivos en `C:\Lab\Evidencias` |
| Se conocen las credenciales de `tec_admin` | Ver la página [Laboratorio virtual](laboratorio-vm.md) |
| La VM no se ha reiniciado desde la Parte 1 | Si se restauró el snapshot, repetir antes la Práctica 0 de la Parte 1 |

### ▸ Práctica 6 — Elevar privilegios y abrir el registro de cambios

**Tiempo:** 5 minutos.

Cerrar la ventana de PowerShell de la Parte 1 y abrir una **nueva, elevada**: menú Inicio → `Windows PowerShell` → clic derecho → **Ejecutar como administrador**. Windows pedirá credenciales porque `operador_alumno` no es administrador: introducir **`tec_admin`** y su contraseña.

Confirmar que la elevación funcionó y abrir la transcripción de esta sesión:

```powershell
[Security.Principal.WindowsPrincipal]::new(
    [Security.Principal.WindowsIdentity]::GetCurrent()
).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)

Start-Transcript -Path C:\Lab\Evidencias\02-correccion.txt -Force
```

**Se espera ver:** `True`, y la confirmación de que la transcripción comenzó.

**Si no aparece:** si devuelve `False`, la consola no está elevada — cerrarla y repetir el procedimiento. No continuar: los comandos de esta parte fallarían a medias, dejando la estación en un estado inconsistente.

!!! note "Por qué se abre una transcripción nueva"
    `01-reconocimiento.txt` registra lo que se observó; `02-correccion.txt` registrará lo que se cambió. Separar observación de intervención es lo que permite demostrar después qué estado se encontró y qué estado se produjo.

---

## Auditoría de Cuentas Locales

Toda cuenta de usuario que existe en un equipo es una identidad que puede ser comprometida. Cuentas que nadie usa actualmente, cuentas con nombres genéricos, o cuentas que tienen privilegios de Administrador sin justificación son vectores de ataque que un adversario buscará.

El primer paso de la auditoría es listar todas las cuentas locales con su estado:

```powershell
# Listar todas las cuentas locales con nombre, estado y último inicio de sesión
Get-LocalUser | Select-Object Name, Enabled, LastLogon
```

La salida típica muestra columnas: Name (nombre de la cuenta), Enabled (si está habilitada o no), LastLogon (cuándo se usó por última vez). Una cuenta con `Enabled: True` y `LastLogon` de hace más de 90 días debe investigarse — ¿por qué existe esta cuenta y quién la usa?

Para identificar qué cuentas tienen privilegios de Administrador local:

```powershell
# Listar todos los miembros del grupo Administradores local
Get-LocalGroupMember -SID 'S-1-5-32-544'
```

En un equipo correctamente configurado, este comando debería mostrar solo las cuentas que tienen justificación explícita para tener privilegios administrativos. Si aparece una cuenta con nombre como "admin", "temp", "prueba" o "usuario2", debe investigarse inmediatamente.

!!! warning "Se pide por SID, no por nombre"
    El grupo se llama `Administrators` en un Windows en inglés y `Administradores` en uno en español. Pedirlo por nombre falla en la mitad de los equipos. El SID **`S-1-5-32-544`** identifica al grupo Administradores integrado en cualquier idioma y en cualquier versión de Windows. Igual que el RID 501 para la cuenta Invitado, es el identificador el que no cambia.

!!! warning "Señales de alerta en cuentas locales"
    Las siguientes situaciones requieren investigación inmediata y posiblemente reporte al oficial de seguridad:

    - Cuenta con nombre genérico ("admin", "temp", "test", "prueba") con estado habilitado.
    - Cuenta en el grupo Administrators que no corresponde a personal autorizado.
    - Cuenta con LastLogon de una fecha durante un período en que nadie debería haber usado el equipo (fines de semana, vacaciones, etc.).
    - La cuenta Guest (`Invitado` en español) aparece con estado habilitado — debe estar siempre deshabilitada.

### Qué hacer al encontrar una cuenta con privilegios injustificados

El orden importa, y la tentación es equivocarse en él. Deshabilitar primero destruye información: si la cuenta está siendo usada por un atacante, se le avisa de que ha sido detectado; si pertenece a un procedimiento legítimo que nadie documentó, se interrumpe un servicio sin saberlo.

El procedimiento correcto es:

1. **Preservar la evidencia.** Registrar nombre, SID, estado, último inicio de sesión y pertenencia a grupos, tal como se encontraron.
2. **Contrastar contra la autorización.** Comparar la identidad observada con el acta de identidades autorizadas de la estación. Sin ese documento no hay forma de decir si una cuenta sobra.
3. **Documentar la decisión.** Anotar qué se va a hacer y por qué, antes de hacerlo.
4. **Corregir.** Retirar el privilegio y, si procede, deshabilitar la cuenta.
5. **Verificar y reportar.** Comprobar que el cambio surtió efecto y elevar el parte al oficial de seguridad.

!!! danger "No borrar nunca la cuenta"
    Retirar el privilegio y deshabilitar es reversible y conserva la evidencia. **Borrar** una cuenta destruye su SID, su historial y la posibilidad de investigar qué hizo. Una cuenta deshabilitada no puede iniciar sesión: el riesgo ya está contenido.

### ▸ Práctica 7 — Auditar identidades contra el acta de autorizaciones

**Tiempo:** 15 minutos.

**Paso 1 — Registrar el estado encontrado**

```powershell
Get-LocalGroupMember -SID 'S-1-5-32-544' |
    Format-Table Name, ObjectClass, PrincipalSource, SID -AutoSize

Get-LocalUser |
    Format-Table Name, Enabled, LastLogon, SID -AutoSize
```

**Se espera ver:** el grupo Administradores con **tres** miembros, y el listado completo de cuentas locales.

**Paso 2 — Leer el acta de identidades autorizadas**

```powershell
Get-Content C:\Mision\autorizaciones.txt
```

**Se espera ver:** el acta de entrega de la estación, que autoriza únicamente a `operador_alumno` y `tec_admin`.

!!! question "El hallazgo"
    Comparar las dos listas. ¿Qué cuenta aparece en el grupo Administradores sin figurar como autorizada en el acta? ¿Qué dice su descripción sobre cómo llegó ahí? Anotarlo antes de tocar nada.

**Paso 3 — Corregir, con la evidencia ya registrada**

Solo después de haber documentado el hallazgo:

```powershell
$CuentaTemporal = Get-LocalUser -Name 'operador_temp'

Remove-LocalGroupMember -SID 'S-1-5-32-544' -Member $CuentaTemporal -Confirm:$false
Disable-LocalUser -InputObject $CuentaTemporal
```

**Paso 4 — Verificar que el cambio surtió efecto**

```powershell
Get-LocalUser -Name 'operador_temp' | Format-Table Name, Enabled, SID -AutoSize
Get-LocalGroupMember -SID 'S-1-5-32-544' | Format-Table Name, SID -AutoSize
```

**Se espera ver:** `operador_temp` con `Enabled: False`, y el grupo Administradores reducido a **dos** miembros: la cuenta Administrador integrada y `tec_admin`.

**Si no aparece:** si `Remove-LocalGroupMember` da error, comprobar que la consola está elevada. No repetir el comando a ciegas: volver a listar el grupo y ver el estado real.

!!! note "Por qué se retiró el privilegio y además se deshabilitó"
    Son dos controles distintos. Retirar del grupo elimina la **capacidad** de hacer daño administrativo; deshabilitar elimina la **posibilidad de iniciar sesión**. El acta no autoriza esa identidad en absoluto, así que corresponden ambos. Si el acta la hubiera autorizado como cuenta estándar, se habría retirado solo el privilegio.

---

## Política de Contraseñas

La política de contraseñas define las reglas que Windows aplica a todas las contraseñas de cuentas locales. Se puede consultar y modificar de dos formas: por interfaz gráfica (`Win+R → secpol.msc → Directivas de cuenta → Directiva de contraseñas`) o por línea de comandos con `net.exe accounts`.

En este laboratorio se usa **`net.exe accounts`**, por dos razones: su salida sirve como evidencia registrable en la transcripción, y no depende de navegar menús cuyos nombres cambian con el idioma del sistema.

Las configuraciones recomendadas por el CIS Benchmark Level 1 son:

| Parámetro | Línea en `net accounts` | Valor CIS L1 | Por qué importa |
|-----------|------------------------|-------------|-----------------|
| Longitud mínima de contraseña | *Longitud mínima de contraseña* | 14 caracteres | Contraseñas cortas son vulnerables a ataques de diccionario y fuerza bruta |
| Vigencia máxima de contraseña | *Duración máx. de contraseña (días)* | 60 días | Limita el tiempo de exposición si una contraseña es comprometida |
| Requisitos de complejidad | *(no aparece — ver nota)* | Habilitado | Exige mayúsculas, minúsculas, números y símbolos |
| Umbral de bloqueo de cuenta | *Umbral de bloqueo* | 5 intentos | Bloquea la cuenta tras N intentos fallidos — previene fuerza bruta |
| Duración del bloqueo | *Duración de bloqueo (minutos)* | 15 minutos | Tiempo que la cuenta permanece bloqueada antes de desbloquearse |

!!! note "La complejidad no se ve con `net accounts`"
    El requisito de complejidad no aparece en la salida de `net accounts`: es una directiva de seguridad, no un parámetro de cuentas. Se consulta exportando la política con `secedit.exe`, que es justo lo que hace el último paso de la práctica siguiente.

!!! example "Aplicación en entorno castrense"
    Los sistemas de comunicaciones militares frecuentemente requieren contraseñas más robustas que el estándar corporativo. En un puesto de mando, los equipos que acceden a sistemas de comunicaciones cifradas deben tener contraseñas de al menos 16 caracteres, rotadas cada 30 días, con historial de contraseñas configurado para que no se pueda reusar ninguna de las últimas 24. El oficial de seguridad establece estas políticas mediante directiva; el Técnico verifica que estén implementadas antes de conectar el equipo a la red clasificada.

### ▸ Práctica 8 — Aplicar la política de contraseñas y dejarla probada

**Tiempo:** 8 minutos.

**Paso 1 — Registrar la política encontrada**

```powershell
net.exe accounts
```

**Se espera ver:** una estación sin política. En concreto, *Longitud mínima de contraseña* en `0` y *Umbral de bloqueo* en `Nunca`. Es decir: se admite cualquier contraseña, incluso vacía, y se puede intentar adivinarla infinitas veces.

!!! question "Interpretación"
    ¿Cuál de los dos defectos es más grave para una estación conectada a una red de unidad? Justificar en términos de qué le costaría a un atacante aprovechar cada uno.

**Paso 2 — Aplicar los valores CIS L1**

```powershell
net.exe accounts /minpwlen:14 /maxpwage:60
net.exe accounts /lockoutthreshold:5
net.exe accounts /lockoutduration:15 /lockoutwindow:15
```

**Se espera ver:** *Se ha completado el comando correctamente* tres veces.

**Paso 3 — Verificar el cambio**

```powershell
net.exe accounts
```

**Se espera ver:** *Longitud mínima de contraseña* en `14`, *Duración máx. de contraseña* en `60`, *Umbral de bloqueo* en `5`, y ambas duraciones de bloqueo en `15`.

**Paso 4 — Exportar la política como evidencia**

La salida de un comando en pantalla se pierde; un archivo de política exportado es evidencia que se puede adjuntar a un parte.

```powershell
secedit.exe /export /cfg C:\Lab\Evidencias\02-politica.inf /areas SECURITYPOLICY

Select-String -Path C:\Lab\Evidencias\02-politica.inf `
    -Pattern 'MinimumPasswordLength|MaximumPasswordAge|LockoutBadCount|LockoutDuration|ResetLockoutCount|PasswordComplexity'
```

**Se espera ver:** *La tarea se ha completado correctamente*, seguido de las líneas del archivo con los valores aplicados: `MinimumPasswordLength = 14`, `MaximumPasswordAge = 60`, `LockoutBadCount = 5`, `LockoutDuration = 15`, `ResetLockoutCount = 15`.

**Si no aparece:** si `secedit` falla, comprobar que la consola sigue elevada y que `C:\Lab\Evidencias` existe.

---

## Deshabilitar Servicios Innecesarios

Cada servicio de Windows que está en ejecución es código activo con acceso al sistema operativo. Si ese servicio tiene una vulnerabilidad, un atacante puede explotarla — incluso si la vulnerabilidad no tiene nada que ver con el trabajo que realiza el usuario en ese equipo.

El principio es simple: si un servicio no cumple ninguna función útil en este equipo específico, debe estar deshabilitado. Cada servicio deshabilitado reduce la superficie de ataque.

Los tres servicios que se deben deshabilitar en la mayoría de estaciones de trabajo militares que no cumplen funciones de servidor de impresión ni de administración remota:

| Servicio | Por qué deshabilitar | Comando PowerShell |
|----------|---------------------|-------------------|
| **Print Spooler** | Vector del exploit PrintNightmare (CVE-2021-34527) — permite escalación de privilegios a SYSTEM y ejecución remota de código. Si el equipo no imprime, este servicio no tiene propósito. | `Stop-Service Spooler; Set-Service Spooler -StartupType Disabled` |
| **Remote Registry** | Permite modificar el registro de Windows de forma remota. Nadie debe poder alterar el registro de un equipo desde la red — si se necesita hacer cambios, el Técnico debe estar físicamente frente al equipo. | `Stop-Service RemoteRegistry; Set-Service RemoteRegistry -StartupType Disabled` |
| **SMBv1 (protocolo, no servicio)** | El protocolo SMB versión 1 es el vector que explotó WannaCry en 2017 para propagarse entre equipos en segundos. La versión moderna SMBv2/3 lo reemplaza completamente. | `Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol` |

Verificar el estado actual de estos servicios antes de deshabilitar:

```powershell
# Verificar estado de los servicios y del protocolo SMBv1
Get-Service -Name Spooler, RemoteRegistry | Format-Table Name, Status, StartType -AutoSize
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol | Format-List FeatureName, State
```

!!! warning "Un `Format-List` por cada tipo de objeto"
    Los dos comandos anteriores devuelven objetos de **tipo distinto**. Si se encadenan con `Select-Object` en una sola tubería, PowerShell formatea la tabla según el primer objeto que recibe y **descarta el resto en silencio**, sin dar error. El resultado es una verificación que parece haberse hecho y no se hizo. Por eso cada bloque de verificación lleva su propio `Format-Table` o `Format-List`.

---

## Auditoría de Inicio de Sesión

Deshabilitar servicios es un control **preventivo**: reduce lo que un atacante puede alcanzar. Pero si alguien intenta entrar de todos modos, hace falta un control **detectivo** que deje constancia. Ese es el papel de la auditoría de inicio de sesión.

Con la auditoría activada, Windows escribe en el Visor de eventos cada intento de inicio de sesión, exitoso o fallido. Sin ella, un ataque de fuerza bruta contra la estación no deja el menor rastro: el atacante puede probar contraseñas durante semanas y nadie podrá demostrarlo después.

La auditoría se consulta y se configura con `auditpol.exe`, identificando la subcategoría por su **GUID**:

```text
{0CCE9215-69AE-11D9-BED3-505054503030}   →   Inicio de sesión
```

!!! note "Por qué un GUID y no el nombre"
    El nombre de la subcategoría está traducido en un Windows en español. Pedirla como *"Logon"* falla; pedirla como *"Inicio de sesión"* funciona solo en español. El GUID es el mismo en todas las instalaciones del mundo. Es el tercer caso del mismo patrón que ya vimos con el RID 501 y el SID `S-1-5-32-544`.

### ▸ Práctica 9 — Reducir la superficie y activar el registro

**Tiempo:** 8 minutos.

**Paso 1 — Registrar la línea base antes de tocar nada**

```powershell
Get-Service -Name Spooler, RemoteRegistry | Format-Table Name, Status, StartType -AutoSize
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol | Format-List FeatureName, State
auditpol.exe /get /subcategory:"{0CCE9215-69AE-11D9-BED3-505054503030}"
```

**Se espera ver:** `Spooler` en `Running` / `Automatic`, `RemoteRegistry` detenido con inicio `Manual`, SMBv1 en un estado distinto de `Enabled`, y la auditoría de inicio de sesión **sin configurar**.

!!! question "Interpretación"
    De estos cuatro elementos, tres son hallazgos y uno ya está correcto. ¿Cuál está bien? ¿Y por qué la auditoría desactivada es el hallazgo más difícil de detectar de todos los que se han visto en las dos partes?

**Paso 2 — Deshabilitar los servicios innecesarios**

```powershell
'Spooler', 'RemoteRegistry' | ForEach-Object {
    Stop-Service -Name $_ -Force -ErrorAction SilentlyContinue
    Set-Service -Name $_ -StartupType Disabled
}
```

**Paso 3 — Activar la auditoría de inicio de sesión**

```powershell
auditpol.exe /set /subcategory:"{0CCE9215-69AE-11D9-BED3-505054503030}" /success:enable /failure:enable
auditpol.exe /get /subcategory:"{0CCE9215-69AE-11D9-BED3-505054503030}"
```

**Se espera ver:** la subcategoría *Inicio de sesión* configurada como **Correcto y error**.

**Si no aparece:** `auditpol` exige elevación. Si devuelve un error de acceso, la consola no está elevada.

!!! danger "Deshabilitar Spooler deja el equipo sin imprimir"
    Es la decisión correcta en una estación que no imprime, y es exactamente lo que este laboratorio pide. Pero en un equipo real esa consecuencia se consulta antes de aplicarla: un control de seguridad que impide cumplir la misión no es un control, es una avería. Documentar el impacto forma parte del cambio.

### ▸ Práctica 10 — Validación final y entrega

**Tiempo:** 9 minutos.

**Paso 1 — Validar los ocho criterios de una sola vez**

```powershell
$EstadoDefender     = Get-MpComputerStatus
$CuentaInvitado     = Get-LocalUser | Where-Object { $_.SID.Value -match '-501$' }
$CuentaTemporal     = Get-LocalUser -Name 'operador_temp'
$SidsAdministradores = (Get-LocalGroupMember -SID 'S-1-5-32-544').SID.Value

[pscustomobject]@{
    InvitadoDeshabilitado       = -not $CuentaInvitado.Enabled
    TemporalDeshabilitada       = -not $CuentaTemporal.Enabled
    TemporalYaNoEsAdministrador = -not ($SidsAdministradores -contains $CuentaTemporal.SID.Value)
    SpoolerDeshabilitado        = (Get-Service Spooler).StartType -eq 'Disabled'
    RemoteRegistryDeshabilitado = (Get-Service RemoteRegistry).StartType -eq 'Disabled'
    SMB1Deshabilitado           = (Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol).State -ne 'Enabled'
    DefenderTiempoRealActivo    = $EstadoDefender.RealTimeProtectionEnabled
    FirmasDesactualizadas       = $EstadoDefender.DefenderSignaturesOutOfDate
} | Format-List
```

**Se espera ver:** los siete primeros criterios en `True` y el último, `FirmasDesactualizadas`, en `False`. Cualquier otra combinación significa que un paso quedó a medias: revisarlo antes de entregar.

**Paso 2 — Empaquetar la evidencia y sellarla con su hash**

```powershell
Stop-Transcript

$Paquete = "C:\Evidencias-$env:USERNAME.zip"
Compress-Archive -Path C:\Lab\Evidencias\* -DestinationPath $Paquete -Force

Get-FileHash $Paquete -Algorithm SHA256 |
    Format-List Algorithm, Hash, Path

Get-FileHash $Paquete -Algorithm SHA256 |
    Select-Object -ExpandProperty Hash |
    Set-Content "$Paquete.sha256"
```

**Se espera ver:** el ZIP creado en `C:\`, su hash SHA-256 en pantalla, y un archivo `.sha256` junto al paquete.

!!! note "Para qué sirve el hash"
    El hash es la huella digital del paquete. Si un solo byte cambia entre la entrega y la revisión, el hash deja de coincidir. Es el mismo principio de **integridad** del Incidente 2 de la Parte 1, aplicado ahora al propio trabajo del alumno.

**Paso 3 — Entregar**

Comunicar al instructor el nombre del paquete y su hash, por el medio que él indique. El instructor recogerá `C:\Evidencias-<usuario>.zip` y su archivo `.sha256`.

!!! danger "El snapshot se restaura al terminar"
    Todo lo que quede dentro de la máquina virtual desaparece cuando el instructor restaura `01-MISION-CONTROLES`. **La entrega no está hecha hasta que el instructor la confirma.**

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Antes de conectar una estación de trabajo a la red de unidad, el Técnico ejecuta la siguiente secuencia de auditoría:

    ```powershell
    # 1. Auditar cuentas locales
    Get-LocalUser | Format-Table Name, Enabled, LastLogon, SID -AutoSize

    # 2. Verificar miembros del grupo Administradores (por SID, no por nombre)
    Get-LocalGroupMember -SID 'S-1-5-32-544' | Format-Table Name, SID -AutoSize

    # 3. Verificar estado de servicios críticos
    Get-Service -Name Spooler, RemoteRegistry | Format-Table Name, Status, StartType -AutoSize

    # 4. Verificar SMBv1 (debe estar deshabilitado)
    Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol | Format-List FeatureName, State

    # 5. Verificar que la auditoría de inicio de sesión está activa
    auditpol.exe /get /subcategory:"{0CCE9215-69AE-11D9-BED3-505054503030}"
    ```

    Los resultados se documentan en el registro de auditoría del equipo: fecha, nombre del Técnico, estado encontrado y acciones correctivas tomadas. Este registro se reporta al oficial de seguridad de la unidad antes de la integración a la red. El control no es burocrático — es la evidencia de que el equipo fue verificado antes de conectarse y que alguien es responsable de su estado.

---

## Resumen

1. La auditoría de cuentas locales (`Get-LocalUser` y `Get-LocalGroupMember -SID 'S-1-5-32-544'`) revela identidades con acceso al sistema — cualquier cuenta con privilegios de Administrador que no figure en el acta de autorizaciones es una señal de alarma.
2. **Una identidad no se juzga sola, se contrasta contra su autorización.** Sin el acta de la estación no hay forma de decidir si una cuenta sobra: lo que convierte a `operador_temp` en hallazgo no es su nombre, es su ausencia del documento.
3. **El orden es evidencia → contraste → decisión documentada → corrección → verificación.** Deshabilitar antes de registrar destruye información y avisa a un posible atacante. Y nunca se borra una cuenta: se retira el privilegio y se deshabilita, que es reversible.
4. La política de contraseñas se aplica con `net.exe accounts` y se conserva como evidencia con `secedit.exe /export` — el mínimo CIS L1 es 14 caracteres, vigencia de 60 días y bloqueo a los 5 intentos fallidos.
5. Print Spooler, Remote Registry y SMBv1 deben estar deshabilitados en equipos que no los necesitan — cada uno tiene vulnerabilidades críticas documentadas (PrintNightmare, administración remota del registro, WannaCry).
6. La **auditoría de inicio de sesión** es el control detectivo que acompaña a los preventivos: sin ella, un ataque de fuerza bruta contra la estación no deja rastro alguno.
7. Los identificadores que no dependen del idioma son la única forma fiable de escribir comandos que funcionen en las veinte estaciones: **RID 501** (Invitado), **SID `S-1-5-32-544`** (Administradores), **GUID `{0CCE9215-…}`** (Inicio de sesión).
8. Todo hallazgo de auditoría se documenta, se sella con su hash y se reporta al oficial de seguridad antes de conectar el equipo a la red.

---

*Siguiente: [Antivirus Avanzados (Parte 1)](antivirus-avanzados-p1.md)*
