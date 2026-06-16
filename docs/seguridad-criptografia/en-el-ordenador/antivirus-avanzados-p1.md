---
# Horas asignadas: 3 hrs
# Tipo: Teoría
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

---

## Exclusiones: Necesidad y Riesgo

Una exclusión le dice a Defender: "no analices esta carpeta, archivo, proceso o tipo de archivo". Las exclusiones existen porque hay situaciones legítimas en que el AV genera falsos positivos o impacta el rendimiento de aplicaciones críticas:

- **Falsos positivos:** Una herramienta de administración legítima que usa técnicas similares a las de malware puede ser detectada erróneamente.
- **Rendimiento:** En servidores de bases de datos o aplicaciones que procesan miles de archivos por segundo, el análisis en tiempo real puede degradar el rendimiento inaceptablemente.

**Cómo configurar exclusiones via GUI:**
Windows Security → Virus & threat protection → Manage settings → Exclusions → Add or remove exclusions → Add an exclusion

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
5. Cada **exclusión** es una zona ciega que el AV no ve — toda exclusión debe estar documentada con justificación, autorización y fecha de revisión.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Microsoft Defender Antivirus en Windows — Microsoft Learn:** https://learn.microsoft.com/es-es/microsoft-365/security/defender-endpoint/microsoft-defender-antivirus-windows — Documentación oficial completa incluyendo PowerShell, exclusiones y configuración avanzada.
- **Security Intelligence Updates (definiciones de firmas):** https://www.microsoft.com/es-es/wdsi/definitions — Página oficial para descarga manual de definiciones (útil en entornos sin conectividad a internet).

---

*Siguiente: [Antivirus Avanzados (Parte 2)](antivirus-avanzados-p2.md)*
