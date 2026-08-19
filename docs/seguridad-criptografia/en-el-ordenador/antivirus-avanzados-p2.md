---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Antivirus Avanzados: Microsoft Defender (Parte 2)

> **Duración:** 2 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En el Ordenador | Continuación de Parte 1

En la Parte 1 vimos cómo funciona la detección por firmas, por qué las actualizaciones de definiciones son críticas, la diferencia entre detección heurística y comportamental, los cuatro comandos PowerShell principales de Defender, y el riesgo de configurar exclusiones sin documentación adecuada. En esta Parte 2 nos enfocamos en las operaciones diarias de gestión: cómo programar escaneos para que no interfieran con la misión, qué hacer cuando Defender detecta y pone en cuarentena un archivo, cómo leer el historial de detecciones (Protection History), y cómo interpretar el Windows Security Center Dashboard como herramienta de supervisión integrada.

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Configurar escaneos programados en Microsoft Defender, eligiendo el tipo de escaneo y horario apropiados para el entorno operativo.
- Gestionar la cuarentena de Defender: revisar archivos detenidos, decidir cuándo restaurar y cuándo eliminar permanentemente.
- Leer el Protection History e interpretar el nombre de una amenaza detectada por Microsoft.

---

## Escaneos Programados

Un escaneo completo del disco duro puede tomar varias horas y consumir recursos significativos de CPU y almacenamiento. Si se ejecuta en horario de operaciones, puede degradar la capacidad de trabajo. La solución es programar los escaneos para momentos de baja actividad.

**Tipos de escaneo:**

| Tipo | Qué analiza | Duración típica | Cuándo usar |
|------|------------|----------------|-------------|
| **Quick Scan** | Memoria RAM activa, archivos de inicio del sistema, carpeta System32, ubicaciones de autorrun | 5-15 minutos | Verificación rápida diaria; antes de conectar un equipo a la red |
| **Full Scan** | Todos los archivos del disco duro, incluyendo carpetas comprimidas y archivos de sistema | 1-6 horas (según capacidad del disco) | Verificación periódica profunda; después de un incidente sospechoso |

**¿Por qué los escaneos completos van los fines de semana?** En entornos militares operativos, los equipos se usan de forma intensiva durante el horario de servicio. Un Full Scan que empieza un martes a las 10:00 puede hacer que el equipo sea prácticamente inutilizable hasta la tarde. La práctica correcta es programarlos para el sábado temprano o domingo, cuando los equipos están encendidos pero sin actividad de usuario.

### Demostración — Programar un Full Scan para dentro de cinco minutos

Abrir **PowerShell como administrador**. La demostración calcula el día y la hora a partir del reloj de la estación, configura un examen completo y elimina las dos condiciones que harían impredecible una prueba en vivo: la aleatorización y la espera por inactividad.

```powershell
# Calcular el momento objetivo: cinco minutos desde ahora
$Objetivo = (Get-Date).AddMinutes(5)
$Dia = $Objetivo.DayOfWeek.ToString()
$Hora = $Objetivo.ToString('HH:mm:ss')
$InicioPrueba = Get-Date

# Programar un Full Scan semanal en el día y la hora calculados
Set-MpPreference `
    -ScanParameters FullScan `
    -ScanScheduleDay $Dia `
    -ScanScheduleTime $Hora `
    -RandomizeScheduleTaskTimes $false `
    -ScanOnlyIfIdleEnabled $false

"FULL SCAN PROGRAMADO PARA: $($Objetivo.ToString('dd/MM/yyyy HH:mm:ss'))"

# Comprobar la configuración efectiva antes de esperar
Get-MpPreference | Format-List `
    ScanParameters, `
    ScanScheduleDay, `
    ScanScheduleTime, `
    ScanScheduleOffset, `
    RandomizeScheduleTaskTimes, `
    SchedulerRandomizationTime, `
    ScanOnlyIfIdleEnabled, `
    EnableFullScanOnBatteryPower
```

**Criterio para continuar:** `ScanParameters` debe ser `2`, `ScanScheduleTime` debe coincidir con `$Hora`, `RandomizeScheduleTaskTimes` debe ser `False` y `ScanOnlyIfIdleEnabled` debe ser `False`. Si la hora aparece como `00:00:00`, no esperar: repetir el bloque sin agregar `-ScanScheduleOffset 0`.

!!! warning "No combinar la hora con un offset igual a cero"
    En la versión de Defender instalada en la VM, incluir `-ScanScheduleTime $Hora` y `-ScanScheduleOffset 0` en el mismo comando hizo que prevaleciera el offset y dejó la hora efectiva en `00:00:00`. Esta demostración configura únicamente `ScanScheduleTime`; `ScanScheduleOffset` se consulta como evidencia, pero no se modifica.

Mantener la VM encendida, sin suspensión y conectada a corriente. Al llegar la hora objetivo, comprobar los eventos generados desde el inicio de la prueba:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName   = 'Microsoft-Windows-Windows Defender/Operational'
    Id        = 1000, 1001, 1002
    StartTime = $InicioPrueba
} |
    Sort-Object TimeCreated |
    Select-Object TimeCreated, Id, Message |
    Format-List
```

El evento `1000` demuestra que el examen comenzó; su mensaje debe indicar `Full Scan` y mostrar el usuario `NT AUTHORITY\SYSTEM`. El evento `1001` demuestra que terminó y debe contener el mismo `Scan ID`. Un evento `1002` indica que comenzó, pero fue cancelado.

En **Seguridad de Windows → Protección contra virus y amenazas → Amenazas actuales** se muestra el último examen, su duración y la cantidad de archivos examinados. La aplicación no conserva una lista completa de exámenes limpios; para esa trazabilidad se utiliza el registro `Windows Defender/Operational`.

**Opciones de examen desde la interfaz:**
Seguridad de Windows → Protección contra virus y amenazas → Opciones de examen permite iniciar exámenes bajo demanda. **Microsoft Defender Antivirus (examen sin conexión)** requiere reiniciar y no configura una programación.

Para configurar el escaneo programado estándar con el Programador de tareas: `Win+R → taskschd.msc → Task Scheduler Library → Microsoft → Windows → Windows Defender → Windows Defender Scheduled Scan`.

!!! tip "Escaneos en entornos militares operativos"
    Programar los escaneos completos fuera del horario de operaciones críticas. En sistemas que operan 24/7 (como servidores de comunicaciones), programar el escaneo durante el período de menor carga — generalmente entre las 02:00 y las 04:00. Verificar siempre que la máquina permanece encendida durante la ventana de escaneo; si el equipo se apaga, el escaneo se interrumpe y no se completa.

---

## Gestión de Cuarentena

Cuando Defender detecta un archivo que coincide con una firma de malware o activa la detección heurística, no lo elimina inmediatamente. En cambio, lo mueve a cuarentena — una carpeta cifrada especial del sistema donde el archivo queda inaccesible para el usuario y para otros procesos, pero no se elimina permanentemente.

¿Por qué cuarentena en lugar de eliminación directa? Porque puede ser un falso positivo. Si Defender elimina directamente un archivo legítimo que confundió con malware, ese archivo se pierde permanentemente. La cuarentena da tiempo para revisar y restaurar si es necesario.

**Cómo revisar los archivos en cuarentena:**
- Via GUI: Windows Security → Virus & threat protection → Protection history → Filtrar por "Quarantined items"
- Via PowerShell:

```powershell
# Ver lista de amenazas activas y en cuarentena
Get-MpThreat
```

La salida de `Get-MpThreat` incluye: ThreatID, ThreatName (nombre de la amenaza según taxonomía de Microsoft), Resources (archivos afectados), IsActive (si sigue activa o está en cuarentena).

**¿Cuándo restaurar un archivo de cuarentena?**

Solo cuando se confirma que es un falso positivo. Esto requiere:
1. Investigar el nombre de la amenaza (ver siguiente sección sobre cómo interpretar los nombres).
2. Confirmar con el proveedor del software que afectó que es un falso positivo conocido.
3. Obtener autorización del oficial de seguridad por escrito.
4. Restaurar el archivo y agregar una exclusión documentada para que no vuelva a ponerse en cuarentena.

**¿Cuándo eliminar permanentemente?**

Cuando se confirma que el archivo es malware real. En ese caso: Protection history → seleccionar el elemento en cuarentena → Remove.

!!! warning "No restaurar archivos de cuarentena sin autorización"
    Restaurar un archivo de cuarentena que es malware real — y no un falso positivo — equivale a re-infectar el equipo deliberadamente. En entornos militares, ningún Técnico debe restaurar un archivo de cuarentena sin confirmación explícita del oficial de seguridad. Si hay duda, la respuesta correcta siempre es: no restaurar y escalar.

---

## Protection History: Leyendo el Log de Detecciones

Protection History es el registro de todas las acciones que Defender ha tomado: detecciones, cuarentenas, eliminaciones, restauraciones. Es la evidencia forense básica de actividad de seguridad en el equipo.

Para acceder: Windows Security → Virus & threat protection → Protection history.

Cada evento muestra: nombre de la amenaza, archivo afectado, acción tomada, fecha y hora.

**Leyendo el nombre de una amenaza:** Microsoft usa una taxonomía estándar para nombrar las amenazas:

`Tipo:Plataforma/Familia.Variante!Modificador`

Ejemplo: `Trojan:Win32/Emotet.AH!ibt`
- `Trojan` → tipo de amenaza
- `Win32` → plataforma (Windows 32/64-bit)
- `Emotet` → familia de malware
- `AH` → variante específica dentro de la familia
- `!ibt` → modificador (en este caso, "cloud-based detection" — detectado via MAPS)

| Tipo de amenaza | Qué significa | Acción recomendada |
|-----------------|--------------|-------------------|
| **Trojan** | Software malicioso disfrazado de legítimo. Puede robar credenciales, instalar backdoors, exfiltrar datos. | Cuarentena inmediata. Aislar equipo de la red. Investigar qué hizo el troyano antes de ser detectado. |
| **Worm** | Se propaga automáticamente por la red sin interacción del usuario. Puede infectar todos los equipos de la red local. | Aislar el equipo de la red INMEDIATAMENTE. El worm puede estar en otros equipos ya. |
| **Exploit** | Código que aprovecha una vulnerabilidad específica del software o sistema operativo. | Cuarentena. Verificar si el sistema tiene el parche correspondiente. Aplicar parche urgente. |
| **PUA** (Potentially Unwanted Application) | Software que no es intrínsecamente malicioso pero puede ser no deseado: adware, cryptominers, software de acceso remoto no autorizado. | Evaluar caso a caso. Si no es software autorizado, eliminar. |
| **HackTool** | Herramienta de hacking legítima (como Mimikatz o PSExec) usada potencialmente con fines maliciosos. | Investigar quién lo instaló y por qué. Probablemente no tiene justificación en un equipo de usuario estándar. |

!!! note "PUA no es lo mismo que malware"
    Un PUA (Potentially Unwanted Application) no es intrínsecamente malicioso — puede ser un adware que muestra publicidad, o una extensión de navegador que redirige búsquedas. El riesgo es que puede comprometer la privacidad o degradar el rendimiento. Sin embargo, en un entorno militar, cualquier software no autorizado — sea PUA o malware claro — debe ser evaluado y probablemente eliminado. La regla general: si no está en la lista de software autorizado de la unidad, no debe estar en el equipo.

---

## Windows Security Center Dashboard

El Windows Security Center Dashboard no es solo el punto de acceso a cada herramienta — es una herramienta de supervisión integrada que combina el estado de múltiples componentes de seguridad en una sola vista.

Los componentes que integra el dashboard:
- **Defender Antivirus:** Estado de protección en tiempo real, fecha de últimas definiciones, resultado del último escaneo.
- **Windows Firewall:** Estado de los tres perfiles (Domain, Private, Public) — si alguno está deshabilitado, aparece en rojo.
- **SmartScreen:** Protección contra aplicaciones y sitios web no reconocidos.
- **Device Health:** Estado del TPM, Secure Boot, y actualizaciones de Windows.

**Interpretando el dashboard:**

- **Panel verde:** Todo funciona correctamente. Sin acción requerida.
- **Panel amarillo (advertencia):** Una recomendación de seguridad está pendiente. Ejemplo: "Some settings are managed by your organization" — la política de la organización puede haber configurado algo que el dashboard identifica como no óptimo pero que es intencional. Revisar y decidir.
- **Panel rojo (crítico):** Acción inmediata requerida. Ejemplos: protección en tiempo real deshabilitada, firewall apagado, definiciones con más de 7 días de antigüedad.

**¿Por qué el dashboard no reemplaza revisar los logs individuales?**

El dashboard muestra el estado actual. Pero si hubo una detección ayer, una cuarentena hace tres días, o un intento de deshabilitación del AV la semana pasada, el dashboard de hoy no lo refleja — muestra que todo está bien ahora. Para entender el historial de lo que ha ocurrido, se necesita revisar Protection History, el Visor de Eventos (Event Viewer → Windows Logs → Security), y los logs de WFAS. El dashboard es la vista de "¿estoy protegido ahora?". Los logs son la respuesta a "¿qué ha pasado?".

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Durante una operación nocturna, un Técnico de turno recibe una alerta en pantalla: Defender ha detectado y puesto en cuarentena un archivo en la estación de trabajo del oficial de comunicaciones. El nombre de la amenaza es `Worm:Win32/Conficker.C`.

    El protocolo correcto:

    **Paso 1 — Verificar la amenaza en Protection History.** Abrir Windows Security → Protection history → identificar el evento. La amenaza es un Worm — el tipo más peligroso desde el punto de vista de propagación lateral.

    **Paso 2 — Aislar el equipo de la red inmediatamente.** Los worms se propagan automáticamente. Si el equipo sigue conectado a la red de unidad, el Conficker puede ya estar intentando infectar otros equipos. Desconectar el cable de red y deshabilitar el WiFi antes de hacer cualquier otra cosa.

    **Paso 3 — NO restaurar el archivo de cuarentena.** Es un worm confirmado, no un falso positivo.

    **Paso 4 — Reportar al oficial de seguridad.** Comunicar: nombre del equipo infectado, nombre de la amenaza detectada, hora del evento, acción tomada (aislamiento). El oficial decide los siguientes pasos: escanear otros equipos de la red, revisar logs de red, iniciar procedimiento de respuesta a incidentes.

    **Paso 5 — Documentar todo.** Qué se detectó, cuándo, qué acciones se tomaron y en qué orden. Esta documentación es la base del informe de incidente.

    La respuesta correcta en los primeros dos minutos — verificar y aislar — puede determinar si el incidente afecta a un equipo o a toda la red de unidad.

---

## Resumen

1. Los **escaneos completos** deben programarse fuera del horario de operaciones críticas. La demostración calcula una ejecución para dentro de cinco minutos, configura explícitamente `FullScan` y verifica el resultado mediante los eventos `1000`, `1001` y `1002`; los escaneos rápidos pueden ejecutarse antes de conectar equipos a la red.
2. La **cuarentena** es temporal: `Get-MpThreat` muestra los elementos retenidos; nunca restaurar sin confirmación del oficial de seguridad y sin verificar que es un falso positivo.
3. El **Protection History** registra toda la actividad de Defender — los nombres de amenaza siguen la taxonomía `Tipo:Plataforma/Familia.Variante`; un Worm requiere aislamiento inmediato de la red.
4. El **Windows Security Center Dashboard** muestra el estado actual pero no reemplaza revisar los logs de Protection History y el Visor de Eventos para entender el historial de incidentes.

---

*Siguiente: [Firewall (Parte 1)](cortafuegos-p1.md)*
