---
# Horas asignadas: 3 hrs
# Tipo: Teoría + Laboratorio
---

# Antivirus Avanzados: Microsoft Defender (Parte 1)

> **Duración:** 3 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En el Ordenador

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar cómo funciona la detección de malware por firmas y por qué las actualizaciones de definiciones son críticas.
- Configurar Microsoft Defender Antivirus desde la interfaz gráfica (Windows Security) y desde PowerShell.
- Definir y configurar exclusiones de Defender, entendiendo el riesgo que cada exclusión representa.
- Interpretar el resultado de un escaneo y un evento de cuarentena.
- Describir la diferencia entre detección heurística/comportamental y detección por firmas como capas complementarias de protección.

---

## Entorno de laboratorio

Como en Controles Defensivos, cada tema principal cierra con un bloque **▸ Práctica** sobre la estación **VM-WIN-SI**. Credenciales y arranque de la máquina: [Laboratorio virtual](laboratorio-vm.md).

| Requisito | Verificación |
|-----------|-------------|
| VM-WIN-SI iniciada | Sesión abierta como `operador_alumno` |
| Defender operativo | `Get-MpComputerStatus` devuelve datos del motor y de las firmas |
| Credenciales de `tec_admin` disponibles | Las prácticas 13 y 14 requieren elevación |
| Conectividad a internet desde la VM | Necesaria para `Update-MpSignature` |

!!! warning "Dos niveles de privilegio en esta clase"
    Las prácticas 11 y 12 son de **observación** y funcionan sin elevar. Las prácticas 13 y 14 **modifican la configuración de Defender** y exigen una consola elevada con `tec_admin`. Cada bloque indica cuál necesita.

### ▸ Práctica 11 — Estado del motor y edad de las firmas

**Tiempo:** 15 minutos. **Privilegio:** sin elevación.

```powershell
New-Item -ItemType Directory -Path C:\Lab\Evidencias -Force | Out-Null
Start-Transcript -Path C:\Lab\Evidencias\03-defender.txt -Force

Get-MpComputerStatus |
    Format-List AMRunningMode,
                AntivirusEnabled,
                AntispywareEnabled,
                RealTimeProtectionEnabled,
                AntivirusSignatureVersion,
                AntivirusSignatureLastUpdated,
                DefenderSignaturesOutOfDate
```

**Se espera ver:** el motor en modo `Normal`, antivirus y protección en tiempo real en `True`, una versión de firmas, y `DefenderSignaturesOutOfDate` en `False`.

**Calcular la edad real de las firmas:**

```powershell
$Estado = Get-MpComputerStatus
$Edad   = (Get-Date) - $Estado.AntivirusSignatureLastUpdated

[pscustomobject]@{
    UltimaActualizacion = $Estado.AntivirusSignatureLastUpdated
    HorasDeAntiguedad   = [math]::Round($Edad.TotalHours, 1)
    MarcadasComoViejas  = $Estado.DefenderSignaturesOutOfDate
} | Format-List
```

!!! question "Interpretación"
    ¿Cuántas horas de antigüedad tienen las firmas de esta estación? Microsoft publica varias actualizaciones al día. Si el equipo hubiera estado guardado seis meses, ¿qué valor tendría `DefenderSignaturesOutOfDate` y qué habría que hacer **antes** de conectarlo a la red de unidad?

---

## Cómo Funciona la Detección por Firmas

La detección por firmas es el método más antiguo y todavía más extendido en los antivirus. Para entenderla, sigamos el ciclo de vida completo desde que se descubre un malware hasta que tu antivirus lo detecta.

**El ciclo de la firma:**

1. **Descubrimiento:** Un analista de seguridad, un honeypot, o un sistema automatizado detecta un archivo con comportamiento sospechoso.
2. **Análisis:** El equipo de investigación del fabricante (en el caso de Microsoft, el equipo de Microsoft Defender Research) analiza el archivo. Confirma que es malware y cataloga su comportamiento.
3. **Extracción de firma:** Del análisis se extrae una firma — puede ser un hash MD5/SHA-256 del archivo completo, o una secuencia de bytes característica que solo aparece en ese código malicioso específico. También puede ser una combinación de ambas.
4. **Publicación:** La firma se añade a la base de datos de definiciones (security intelligence) y se publica para todos los usuarios.
5. **Descarga:** Tu equipo descarga la actualización de definiciones. A partir de ese momento, cuando Defender escanea un archivo y encuentra esa secuencia de bytes o ese hash, lo identifica como la amenaza catalogada y actúa.

!!! note "Definición"
    Una **firma de malware** es un patrón único — hash criptográfico, secuencia de bytes, o combinación de ambos — que identifica de forma inequívoca un código malicioso conocido. Funciona como la foto en un cartel de "Se busca": el antivirus busca esa imagen exacta en cada archivo que analiza.

La analogía más clara: imagina que la policía distribuye la foto de un sospechoso conocido a todos los puestos de control. Cualquier control que vea a esa persona la detiene. Pero si el sospechoso usa un disfraz diferente en cada ciudad, la foto ya no sirve. Eso es exactamente la limitación de la detección por firmas ante el malware polimórfico.

**Limitación crítica — código polimórfico:** El malware moderno puede modificar su propio código en cada infección para que el hash o la secuencia de bytes cambie. El malware polimórfico produce variantes que no coinciden con ninguna firma conocida — el antivirus que solo usa firmas no lo detecta. Por eso existe la detección heurística y comportamental (siguiente sección).

---

## Actualización de Definiciones

Si las firmas son el corazón de la detección basada en definiciones, las actualizaciones son el latido. Un antivirus con definiciones de hace una semana está ciego ante todos los malware nuevos descubiertos en los últimos siete días — y en el panorama actual, miles de variantes nuevas aparecen cada día.

Microsoft publica actualizaciones de inteligencia de seguridad múltiples veces al día. El ciclo de actualización tiene tres tipos:

| Tipo de actualización | Frecuencia | Impacto si se omite |
|-----------------------|-----------|---------------------|
| **Security Intelligence Updates** (definiciones de firmas) | Varias veces al día | El AV no detecta malware descubierto desde la última actualización. Crítico si la máquina estuvo offline. |
| **Platform Updates** (motor de análisis de Defender) | Mensual (vía Windows Update) | El motor de análisis puede tener vulnerabilidades conocidas si no se actualiza. |
| **Engine Updates** (mejoras de capacidades) | Mensual (vía Windows Update) | Capacidades de detección heurística y comportamental menos efectivas. |

**¿Qué pasa cuando la máquina estuvo offline?**

Un laptop de campo que estuvo desconectado durante una semana puede tener definiciones de siete días de antigüedad. En esos siete días, pueden haberse publicado parches para amenazas activas. Antes de conectar ese equipo a la red de unidad, el Técnico debe forzar una actualización manual.

Verificar la fecha de la última actualización de firmas:

```powershell
# Ver la fecha y hora de la última actualización de definiciones
(Get-MpComputerStatus).AntivirusSignatureLastUpdated
```

Forzar una actualización inmediata:

```powershell
# Forzar descarga e instalación de las últimas definiciones
Update-MpSignature
```

Si el equipo no tiene acceso a internet, se puede distribuir el paquete de definiciones fuera de línea desde el sitio oficial de Microsoft (mpam-fe.exe). Este procedimiento es relevante para operaciones en entornos sin conectividad.

### ▸ Práctica 12 — Actualizar firmas, escanear e interpretar el resultado

**Tiempo:** 25 minutos. **Privilegio:** sin elevación.

**Paso 1 — Actualizar las definiciones y medir cuánto tarda**

```powershell
$t = Measure-Command { Update-MpSignature }
Write-Host "Update-MpSignature tardo: $([math]::Round($t.TotalSeconds,1)) segundos"

(Get-MpComputerStatus).AntivirusSignatureLastUpdated
```

**Se espera ver:** la marca de tiempo actualizada al momento de la práctica.

**Si no aparece:** si el comando falla, la VM probablemente no tiene salida a internet. Registrar el fallo como hallazgo — un equipo sin acceso a las definiciones es exactamente el escenario del laptop guardado seis meses.

**Paso 2 — Ejecutar un análisis rápido**

```powershell
$t = Measure-Command { Start-MpScan -ScanType QuickScan }
Write-Host "Quick Scan tardo: $([math]::Round($t.TotalMinutes,1)) minutos"
```

**Se espera ver:** el comando **no devuelve el control hasta que el análisis termina**. En esta estación tarda alrededor de 4 a 5 minutos. Que la consola parezca congelada es lo normal: está analizando.

!!! note "Un análisis que no devuelve nada es un análisis limpio"
    `Start-MpScan` no imprime un informe al terminar. El silencio significa que no hubo detecciones. Para consultar el resultado hay que preguntarlo explícitamente, que es el paso siguiente — y es el error más común: dar por hecho que «no salió nada» equivale a «no se analizó».

**Paso 3 — Consultar el historial de detecciones**

```powershell
Get-MpThreatDetection |
    Sort-Object InitialDetectionTime -Descending |
    Format-Table InitialDetectionTime, ThreatID, Resources -AutoSize

Get-MpThreat |
    Format-Table ThreatName, SeverityID, IsActive, Resources -AutoSize
```

**Se espera ver:** en una estación limpia, **ninguna fila**. Una salida vacía aquí es el resultado correcto, no un error del comando.

!!! question "Interpretación"
    ¿Qué diferencia hay entre `Get-MpThreatDetection` y `Get-MpThreat`? El primero lista **eventos de detección** (cuándo se detectó algo y sobre qué archivo); el segundo lista **amenazas conocidas por el equipo** con su severidad y si siguen activas. Un archivo ya puesto en cuarentena aparece como detección, pero `IsActive` en `False`.

---

## Detección Heurística y Comportamental

La detección heurística y comportamental es la segunda capa de defensa — la que cubre lo que las firmas no pueden detectar.

**Detección heurística:** El antivirus analiza las características del código (no solo comparando contra una firma) para determinar si se comporta como malware. Ejemplo: un archivo ejecutable que intenta modificar entradas del registro de inicio del sistema, cifrar otros archivos, y deshabilitar servicios de seguridad al mismo tiempo tiene un perfil heurístico de ransomware — incluso si la firma específica no existe aún en la base de datos.

**Detección comportamental (dinámica):** Va más allá — analiza lo que el proceso realmente hace mientras se ejecuta. Si un proceso inicia y comienza a cifrar cientos de archivos en 30 segundos, eso es comportamiento de ransomware. No importa si la firma existe o no — el comportamiento es la evidencia.

Ejemplo concreto: el ransomware Petya/NotPetya de 2017 usó técnicas que modificaban el código entre infecciones. Muchos AV basados solo en firmas no lo detectaron inicialmente. Los AV con detección comportamental sí lo detectaron porque el comportamiento de cifrado masivo de archivos era inconfundible.

**Microsoft MAPS — Microsoft Active Protection Service:** Microsoft Defender usa análisis en la nube mediante MAPS. Cuando encuentra un archivo sospechoso del que no tiene firma local, puede enviarlo (o su hash) a la nube de Microsoft para análisis en tiempo real. La respuesta llega en segundos y mejora la detección de amenazas zero-day.

**Análisis en reposo vs. análisis dinámico:**

- **En reposo:** Defender analiza el archivo en disco cuando se copia, descarga o abre. Compara firma y aplica heurística estática.
- **Dinámico:** Defender monitorea el proceso mientras se ejecuta. Si el proceso muestra comportamiento sospechoso, interviene aunque el escaneo inicial lo haya considerado limpio.

**¿Más falsos positivos?** Sí. La detección heurística y comportamental genera más falsos positivos que la detección por firmas, porque características que parecen maliciosas pueden tener usos legítimos. Un software de copia de seguridad que escribe miles de archivos rápidamente puede activar la detección comportamental de ransomware. Por eso existen las exclusiones — pero cada exclusión tiene un costo (ver siguiente sección).

**Nota conceptual:** Microsoft Defender Antivirus (incluido gratis en Windows 11) y Microsoft Defender for Endpoint son herramientas diferentes. Defender Antivirus es la solución local que estudiamos aquí. Defender for Endpoint es una solución EDR (Endpoint Detection and Response) basada en la nube que requiere licencia empresarial. No las confundamos — en operación estándar de unidad, tendremos Defender Antivirus.

---

## Configuración via PowerShell

La interfaz gráfica de Windows Security es suficiente para uso básico, pero los Técnicos deben poder gestionar Defender desde PowerShell para automatizar verificaciones y aplicar configuraciones en múltiples equipos.

Los cuatro comandos PowerShell más importantes:

```powershell
# 1. Ver el estado actual completo de Defender
Get-MpComputerStatus
```

Salida típica de `Get-MpComputerStatus` (fragmento relevante):

```
AMRunningMode          : Normal
AntivirusEnabled       : True
AntispywareEnabled     : True
RealTimeProtectionEnabled : True
AntivirusSignatureLastUpdated : 2026-06-16 06:15:00 AM
AntivirusSignatureVersion : 1.413.1234.0
NISEnabled             : True
```

```powershell
# 2. Forzar actualización inmediata de definiciones
Update-MpSignature
```

```powershell
# 3. Ejecutar un escaneo rápido manualmente
Start-MpScan -ScanType QuickScan
```

QuickScan analiza las áreas más frecuentemente infectadas: memoria RAM activa, archivos de inicio del sistema, carpeta System32 y ubicaciones de inicio automático. Generalmente toma entre 5 y 15 minutos.

```powershell
# 4. Verificar que la protección en tiempo real está habilitada (y habilitarla si no lo está)
Set-MpPreference -DisableRealtimeMonitoring $false
```

Este comando asegura que la protección en tiempo real esté activa. Si alguien la había deshabilitado, este comando la vuelve a habilitar. El valor `$false` para "deshabilitar" puede resultar confuso — léase como "establece DisableRealtime a falso", es decir, la protección NO está deshabilitada, está activa.

### ▸ Práctica 13 — Provocar una detección controlada y leer la cuarentena

**Tiempo:** 20 minutos. **Privilegio:** sin elevación para provocar la detección; elevación para limpiar la cuarentena.

Hasta aquí el antivirus no ha detectado nada, así que no se ha visto cómo se comporta cuando sí detecta. Para observarlo hace falta un archivo que dispare la detección **sin ser malware**.

!!! note "Qué es el archivo de prueba EICAR"
    **EICAR** (*European Institute for Computer Antivirus Research*) publicó una cadena de texto de 68 caracteres que todos los antivirus del mercado reconocen por acuerdo como si fuera una amenaza. **No es malware**: no se ejecuta, no hace nada, no tiene código. Es el equivalente a la munición de fogueo — sirve para comprobar que el arma dispara sin que haya proyectil. Es el método estándar y seguro para verificar que un antivirus está realmente operativo.

**Paso 1 — Crear el archivo de prueba**

La cadena se escribe en dos mitades que se unen al ejecutar. Si estuviera entera en un documento, el antivirus pondría en cuarentena el propio documento.

```powershell
$Parte1 = 'X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR'
$Parte2 = '-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*'

Set-Content -Path C:\Lab\Evidencias\prueba-eicar.txt `
            -Value ($Parte1 + $Parte2) `
            -Encoding ASCII -NoNewline
```

**Se espera ver:** con la protección en tiempo real activa, Defender reacciona **en segundos**: aparece una notificación de Windows y el archivo desaparece del disco. Es posible que el propio comando devuelva un error de acceso — eso significa que el antivirus interceptó la escritura antes de completarla, que es el mejor resultado posible.

**Paso 2 — Comprobar que el archivo ya no está**

```powershell
Test-Path C:\Lab\Evidencias\prueba-eicar.txt
```

**Se espera ver:** `False`. El antivirus lo retiró.

**Paso 3 — Leer el evento de detección**

```powershell
Get-MpThreatDetection |
    Sort-Object InitialDetectionTime -Descending |
    Select-Object -First 3 |
    Format-List InitialDetectionTime, ThreatID, Resources, ActionSuccess

Get-MpThreat |
    Format-Table ThreatName, SeverityID, IsActive, Resources -AutoSize
```

**Se espera ver:** una detección reciente cuyo `ThreatName` identifica el archivo de prueba EICAR, con la ruta del archivo en `Resources`.

!!! question "Interpretación del evento"
    1. ¿Qué **acción** tomó Defender: eliminó, puso en cuarentena o solo alertó?
    2. La detección aparece con `IsActive` en `False`. ¿Qué significa eso respecto al riesgo actual del equipo?
    3. Este archivo se detectó **por firma**, no por comportamiento. ¿Por qué era imposible detectarlo por comportamiento?

**Paso 4 — Revisar la cuarentena en la interfaz gráfica**

Abrir **Seguridad de Windows → Protección antivirus y contra amenazas → Historial de protección**. Localizar la entrada correspondiente y guardar una captura como `C:\Lab\Evidencias\03-cuarentena.png` (procedimiento en la página [Laboratorio virtual](laboratorio-vm.md)).

!!! danger "No restaurar el archivo desde la cuarentena"
    El historial de protección ofrece un botón para **permitir** el elemento detectado. En este laboratorio **no se usa**. Restaurar algo desde la cuarentena es una decisión que en un equipo real requiere autorización del oficial de seguridad, y aquí sirve para practicar precisamente esa contención.

!!! warning "Nota para el instructor"
    Esta práctica no formó parte del ensayo en seco de la imagen. Conviene ejecutarla una vez sobre `01-MISION-CONTROLES` antes de dictarla, para confirmar el texto exacto de la notificación y del `ThreatName` en la VM en español.

---

## Exclusiones: Necesidad y Riesgo

Una exclusión le dice a Defender: "no analices esta carpeta, archivo, proceso o tipo de archivo". Las exclusiones existen porque hay situaciones legítimas en que el AV genera falsos positivos o impacta el rendimiento de aplicaciones críticas:

- **Falsos positivos:** Una herramienta de administración legítima que usa técnicas similares a las de malware puede ser detectada erróneamente.
- **Rendimiento:** En servidores de bases de datos o aplicaciones que procesan miles de archivos por segundo, el análisis en tiempo real puede degradar el rendimiento inaceptablemente.

**Cómo configurar exclusiones desde la interfaz gráfica** (rutas en un Windows en español):

Seguridad de Windows → Protección antivirus y contra amenazas → Administrar la configuración → Exclusiones → Agregar o quitar exclusiones → Agregar una exclusión

**Cómo configurar exclusiones via PowerShell:**

```powershell
# Agregar una carpeta a las exclusiones de Defender
Add-MpPreference -ExclusionPath "C:\SoftwareMilitar\CommsApp"

# Ver todas las exclusiones configuradas actualmente
Get-MpPreference | Select-Object Exclusion*
```

**El riesgo de cada exclusión:**

Cada exclusión es una zona ciega que el AV no verá. Si un atacante logra depositar malware dentro de una carpeta excluida — ya sea porque comprometió esa aplicación, porque encontró una vulnerabilidad en el software que vive en esa carpeta, o porque engañó a un usuario para que lo copie allí — Defender no lo detectará. La exclusión lo hace invisible.

!!! danger "Exclusiones sin documentar: una bomba de tiempo"
    Una exclusión sin justificación escrita, sin fecha de revisión, y sin nombre del Técnico que la creó es una vulnerabilidad abierta. En entornos de seguridad maduros, cada exclusión debe registrarse en un documento con:

    - **¿Qué se excluye?** Ruta exacta, proceso o tipo de archivo.
    - **¿Por qué?** Justificación técnica (falso positivo con ticket de soporte, impacto de rendimiento medido).
    - **¿Quién autorizó?** Firma del oficial de seguridad.
    - **¿Cuándo se revisa?** Fecha de revisión (máximo 90 días). Si la razón ya no existe (la aplicación fue desinstalada, el problema fue resuelto), la exclusión se elimina.

    Una carpeta excluida que nadie recuerda por qué fue excluida, que lleva dos años en producción, y que contiene un software legado que ya nadie mantiene es exactamente el tipo de punto ciego que los atacantes buscan.

### ▸ Práctica 14 — Crear una exclusión, comprobar el punto ciego y revertirla

**Tiempo:** 15 minutos. **Privilegio:** **consola elevada con `tec_admin`**.

Se acaba de leer que cada exclusión es una zona ciega. Aquí se comprueba que eso es literal, y se deshace al terminar.

!!! danger "Esta práctica termina obligatoriamente en el Paso 5"
    Se va a crear deliberadamente un punto ciego en el antivirus. **La estación no puede quedar así.** Si suena el timbre a mitad de la práctica, se ejecuta el Paso 5 antes de levantarse.

**Paso 1 — Confirmar la elevación y registrar las exclusiones existentes**

Antes de modificar Defender se comprueban dos precondiciones. Ambas deben cumplirse para que el resultado de la práctica pueda atribuirse únicamente a la exclusión que se va a crear.

**Comprobación 1 — Confirmar que PowerShell está elevado**

```powershell
([Security.Principal.WindowsPrincipal](
    [Security.Principal.WindowsIdentity]::GetCurrent()
)).IsInRole(
    [Security.Principal.WindowsBuiltInRole]::Administrator
)
```

**Qué hace:** obtiene la identidad de Windows de la consola actual y pregunta si su token pertenece al rol integrado `Administrator`.

**Por qué se ejecuta:** `Add-MpPreference` y `Remove-MpPreference` modifican la configuración de seguridad. Sin elevación, la práctica fallará o Defender ocultará información sensible.

**Se espera ver:** `True`. Si devuelve `False`, cerrar la consola y abrir **Windows PowerShell → Ejecutar como administrador** con `tec_admin` antes de continuar.

**Comprobación 2 — Registrar las exclusiones iniciales**

```powershell
Get-MpPreference | Format-List ExclusionPath, ExclusionExtension, ExclusionProcess
```

**Qué hace:** consulta las exclusiones de rutas, extensiones y procesos que Defender tiene configuradas antes del ejercicio.

**Por qué se ejecuta:** establece la línea base. Así se demuestra que el cambio de comportamiento de EICAR se debe a `C:\Lab\ZonaExcluida` y no a una exclusión anterior.

**Se espera ver:** las tres listas vacías. Es lo correcto en una estación recién entregada: **ninguna exclusión sin justificar**.

Si aparece `N/A: Must be an administrator to view exclusions`, la consola no está elevada. Si aparece una exclusión real, no continuar ni eliminarla sin identificarla; restaurar el estado limpio del laboratorio.

**Paso 2 — Crear la carpeta y excluirla**

```powershell
New-Item -ItemType Directory -Path C:\Lab\ZonaExcluida -Force | Out-Null

Add-MpPreference -ExclusionPath 'C:\Lab\ZonaExcluida'

Get-MpPreference | Format-List ExclusionPath
```

**Se espera ver:** `C:\Lab\ZonaExcluida` en la lista de exclusiones.

**Si no aparece:** `Add-MpPreference` exige elevación. Si da error de acceso, la consola no está elevada.

**Paso 3 — Depositar el archivo de prueba dentro de la zona excluida**

```powershell
$Parte1 = 'X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR'
$Parte2 = '-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*'

Set-Content -Path C:\Lab\ZonaExcluida\prueba-eicar.txt `
            -Value ($Parte1 + $Parte2) `
            -Encoding ASCII -NoNewline

Test-Path C:\Lab\ZonaExcluida\prueba-eicar.txt
```

**Se espera ver:** `True`. **El archivo sobrevive.** En la Práctica 13, el mismo archivo desapareció en segundos; aquí Defender ni se entera de que existe.

!!! question "El punto que hay que entender"
    El antivirus está activo, con protección en tiempo real y firmas al día. La amenaza es exactamente la misma que detectó hace diez minutos. Lo único que cambió es una línea de configuración. ¿Qué le costaría a un atacante que ya tuviera acceso al equipo aprovechar esto? ¿Y por qué una exclusión sin fecha de revisión es una vulnerabilidad permanente?

**Paso 4 — Comprobar que un análisis dirigido tampoco la ve**

```powershell
Start-MpScan -ScanType CustomScan -ScanPath C:\Lab\ZonaExcluida
Test-Path C:\Lab\ZonaExcluida\prueba-eicar.txt
```

**Se espera ver:** `True` otra vez. Ni siquiera un análisis apuntado directamente a esa carpeta detecta nada: la exclusión también se aplica a los análisis bajo demanda.

**Paso 5 — Revertir: quitar la exclusión y comprobar que la protección vuelve**

```powershell
Remove-MpPreference -ExclusionPath 'C:\Lab\ZonaExcluida'

Get-MpPreference | Format-List ExclusionPath

Start-MpScan -ScanType CustomScan -ScanPath C:\Lab\ZonaExcluida
Test-Path C:\Lab\ZonaExcluida\prueba-eicar.txt
```

**Se espera ver:** la lista de exclusiones vacía de nuevo, y `Test-Path` devolviendo **`False`** — retirada la exclusión, Defender detecta y elimina el archivo que llevaba minutos ahí tranquilamente.

**Paso 6 — Dejar la estación limpia y cerrar el registro**

```powershell
Remove-Item C:\Lab\ZonaExcluida -Recurse -Force -ErrorAction SilentlyContinue

[pscustomobject]@{
    ExclusionesRestantes     = (Get-MpPreference).ExclusionPath.Count
    CarpetaEliminada         = -not (Test-Path C:\Lab\ZonaExcluida)
    TiempoRealActivo         = (Get-MpComputerStatus).RealTimeProtectionEnabled
} | Format-List

Stop-Transcript
```

**Se espera ver:** `ExclusionesRestantes` en `0`, `CarpetaEliminada` en `True` y `TiempoRealActivo` en `True`. Los tres deben cumplirse antes de dar la práctica por terminada.

!!! warning "Nota para el instructor"
    Como la Práctica 13, este bloque no formó parte del ensayo en seco. Conviene ejecutarlo una vez sobre `01-MISION-CONTROLES` antes de dictarlo. Comprobar en particular que la protección contra manipulaciones (*Tamper Protection*) no bloquee `Add-MpPreference` en esta imagen.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    En un puesto de mando, el software de comunicaciones de la unidad (sistema propietario para gestión de mensajes cifrados) genera falsos positivos en Microsoft Defender. El antivirus detecta la herramienta de cifrado integrada en el software como "HackTool:Win32/NetHacker" — un falso positivo documentado por el proveedor del software.

    El Técnico debe:

    1. **Documentar el falso positivo:** Abrir un ticket con el proveedor del software, obtener la referencia al falso positivo conocido.
    2. **Solicitar autorización:** Presentar la justificación al oficial de seguridad de la unidad y obtener aprobación escrita antes de crear la exclusión.
    3. **Crear la exclusión de forma mínima:** Excluir solo la carpeta específica de instalación del software (`C:\CommsSystem\`), no todo el disco ni carpetas genéricas como `C:\Program Files\`.
    4. **Registrar la exclusión:** Fecha, ruta exacta, justificación, número de ticket con proveedor, nombre del Técnico, firma del oficial que autorizó.
    5. **Programar revisión trimestral:** En 90 días, revisar si la exclusión sigue siendo necesaria (¿el proveedor publicó una versión que no genera el falso positivo?).

    Esta disciplina protege la misión: la herramienta de comunicaciones funciona sin interferencia del AV, y la exclusión está documentada de forma que si ocurre un incidente futuro, se puede revisar si la exclusión fue el vector de entrada.

---

## Resumen

1. La **detección por firmas** compara cada archivo contra una base de datos de patrones de malware conocidos — es efectiva pero no detecta malware nuevo ni polimórfico.
2. Las **actualizaciones de definiciones** son críticas: `Update-MpSignature` debe ejecutarse antes de conectar cualquier equipo que estuvo offline, y verificarse con `(Get-MpComputerStatus).AntivirusSignatureLastUpdated`.
3. La **detección heurística y comportamental** complementa las firmas analizando el comportamiento del código — detecta malware nuevo pero genera más falsos positivos.
4. Los comandos PowerShell clave son: `Get-MpComputerStatus` (estado), `Update-MpSignature` (actualizar definiciones), `Start-MpScan -ScanType QuickScan` (escaneo rápido), `Set-MpPreference -DisableRealtimeMonitoring $false` (activar protección en tiempo real).
5. Un análisis que termina **en silencio** no informa de nada: el resultado se consulta explícitamente con `Get-MpThreatDetection` (eventos de detección) y `Get-MpThreat` (amenazas conocidas y si siguen activas).
6. El archivo de prueba **EICAR** permite comprobar que el antivirus está realmente operativo sin usar malware — es el único modo seguro de ver una detección y una cuarentena de verdad.
7. Cada **exclusión** es una zona ciega que el AV no ve — comprobado en la práctica: el mismo archivo que Defender elimina en segundos sobrevive indefinidamente dentro de una ruta excluida, incluso ante un análisis dirigido.
8. Toda exclusión debe estar documentada con justificación, autorización y fecha de revisión — y toda exclusión creada para practicar debe **revertirse con `Remove-MpPreference`** antes de terminar.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Microsoft Defender Antivirus en Windows — Microsoft Learn:** https://learn.microsoft.com/es-es/microsoft-365/security/defender-endpoint/microsoft-defender-antivirus-windows — Documentación oficial completa incluyendo PowerShell, exclusiones y configuración avanzada.
- **Security Intelligence Updates (definiciones de firmas):** https://www.microsoft.com/es-es/wdsi/definitions — Página oficial para descarga manual de definiciones (útil en entornos sin conectividad a internet).

---

*Siguiente: [Antivirus Avanzados (Parte 2)](antivirus-avanzados-p2.md)*
