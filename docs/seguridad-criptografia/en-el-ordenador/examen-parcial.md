---
# Horas asignadas: 1 hr
# Tipo: Examen
---

# Examen Parcial — Unidad: En el Ordenador

> **Duración:** 60 minutos | **Preguntas:** 20 | **Valor:** 20 puntos (1 punto por pregunta)

!!! warning "Instrucciones"
    - Responder de forma individual. No consultar apuntes, libros ni dispositivos electrónicos.
    - Para las preguntas de opción múltiple (Sección A), marcar **una sola respuesta** por pregunta en la hoja de respuestas.
    - Para las preguntas de identificación (Sección B), el instructor proyectará o distribuirá los pantallazos.
    - Para las preguntas de respuesta corta (Sección C), escribir en el espacio indicado en la hoja de examen.
    - Escribir nombre completo y número de identificación en la hoja de respuestas antes de comenzar.
    - Al terminar, entregar la hoja de respuestas al instructor. Conservar el enunciado.

---

## Sección A: Opción Múltiple (Preguntas 1-12)

*Cada pregunta vale 1 punto. Marcar una sola respuesta.*

---

**P1.** ¿Cuál dimensión de la Triada CIA se ve afectada cuando un virus cifra los archivos de una víctima de ransomware y pide rescate?

- (A) Confidencialidad únicamente
- (B) Integridad únicamente
- (C) Disponibilidad únicamente
- (D) Las tres dimensiones simultáneamente

---

**P2.** ¿Qué comando PowerShell lista todos los usuarios locales de un equipo Windows 11?

- (A) `Get-ADUser`
- (B) `Get-LocalUser`
- (C) `net users`
- (D) `whoami /all`

---

**P3.** Según el CIS Benchmark Level 1 para Windows 11, ¿cuál es el umbral de intentos fallidos de inicio de sesión recomendado antes de bloquear la cuenta?

- (A) 3 intentos
- (B) 5 intentos
- (C) 10 intentos
- (D) No existe recomendación de bloqueo en CIS L1

---

**P4.** ¿Por qué se recomienda deshabilitar el servicio Print Spooler en equipos que no tienen funciones de impresión?

- (A) Consume demasiada memoria RAM en equipos con poca capacidad
- (B) Es un vector de ataque documentado (PrintNightmare) que permite escalación de privilegios a SYSTEM
- (C) Impide que Windows Update descargue parches correctamente
- (D) Ralentiza el inicio del sistema al cargar controladores de impresora innecesarios

---

**P5.** ¿Cuál es la función principal del Windows Security Center en Windows 11?

- (A) Instalar actualizaciones del sistema operativo automáticamente
- (B) Proporcionar una vista unificada del estado de todas las protecciones de seguridad del equipo
- (C) Configurar la política de contraseñas del sistema
- (D) Administrar los servicios en ejecución y su consumo de recursos

---

**P6.** ¿Cómo funciona la detección de malware por firmas en Microsoft Defender?

- (A) Analiza el comportamiento del proceso en tiempo real comparando sus acciones con patrones sospechosos
- (B) Compara el código de cada archivo contra una base de datos de secuencias de bytes conocidas de malware
- (C) Envía automáticamente todos los archivos a la nube de Microsoft para análisis
- (D) Bloquea todos los archivos ejecutables descargados de internet hasta que el usuario los autorice

---

**P7.** Un Técnico configura una exclusión de Defender para la carpeta `C:\SoftwareMilitar\` porque el software genera falsos positivos. ¿Cuál es el principal riesgo de esta acción?

- (A) El equipo se vuelve notablemente más lento al no analizar esa carpeta
- (B) Windows Update deja de funcionar para los archivos en esa carpeta
- (C) Si un atacante deposita malware en esa carpeta, Defender no lo detectará
- (D) La exclusión expira automáticamente en 30 días y debe renovarse

---

**P8.** ¿Cuál es la diferencia entre un escaneo rápido (Quick Scan) y un escaneo completo (Full Scan) en Defender?

- (A) El escaneo rápido solo revisa los archivos modificados en las últimas 24 horas
- (B) El escaneo rápido se enfoca en áreas de alta probabilidad de infección (memoria, startup, System32); el completo revisa todos los archivos del disco
- (C) El escaneo rápido solo funciona en modo sin conexión a internet
- (D) No hay diferencia funcional; el rápido solo usa menos CPU durante el análisis

---

**P9.** En Windows Defender Firewall, ¿cuál es el comportamiento predeterminado para el tráfico de **entrada** (inbound) que no tiene una regla de permiso explícita?

- (A) Se permite el tráfico y se registra en el log para revisión posterior
- (B) Se bloquea el tráfico y se registra en el log automáticamente
- (C) Se bloquea el tráfico silenciosamente (sin registro por defecto)
- (D) Se pregunta al usuario si permite o bloquea antes de actuar

---

**P10.** Un soldado conecta su laptop a la red WiFi de un hotel durante una misión. ¿Qué perfil de red de Windows Firewall debe estar activo?

- (A) Dominio, porque es una red institucional que requiere autenticación
- (B) Privado, porque el hotel es un entorno controlado con acceso restringido
- (C) Público, porque es una red no verificada y potencialmente hostil
- (D) El perfil no importa; el firewall aplica las mismas reglas en todos los perfiles

---

**P11.** ¿Por qué es importante controlar el tráfico **saliente** (outbound) en el cortafuegos, además del tráfico entrante?

- (A) Para mejorar la velocidad de descarga de actualizaciones de Windows
- (B) Para impedir que malware instalado en el equipo se comunique con servidores de comando y control externos
- (C) Porque Windows Firewall bloquea todo el tráfico saliente por defecto
- (D) Para cumplir con la norma ISO 27001 que exige logging bidireccional

---

**P12.** ¿Qué limitación tiene Windows Defender Firewall que un UTM corporativo no tiene?

- (A) No puede bloquear tráfico UDP, solo tráfico TCP
- (B) No puede crear reglas por aplicación, solo por puerto y protocolo
- (C) No puede inspeccionar el contenido del tráfico permitido (Deep Packet Inspection)
- (D) Solo funciona correctamente cuando el equipo está unido a un dominio

---

## Sección B: Identificación de Pantallazos (Preguntas 13-16)

*El instructor proyecta o distribuye cada pantallazo. Describir lo que se observa y responder según se indica. Cada pregunta vale 1 punto.*

---

**P13.** *[Pantallazo: Windows Security Center mostrando el panel "Virus & threat protection" con un círculo rojo con X.]*

¿Qué indica esta señal y cuál es la acción inmediata requerida por parte del Técnico?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P14.** *[Pantallazo: WFAS mostrando una regla con Action=Block, Direction=Outbound, Protocol=TCP, Remote Port=443.]*

¿Qué efecto tiene esta regla sobre el equipo? ¿Es esto una configuración típica o inusual? Justifique.

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P15.** *[Pantallazo: Ventana de Protection History de Defender mostrando el archivo "eicar_test.com" detectado como "Virus:DOS/EICAR_Test_File".]*

¿Qué es el archivo EICAR y por qué aparece en cuarentena? ¿Se debe restaurar? Justifique.

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P16.** *[Pantallazo: Log de firewall pfirewall.log con la línea: `2026-06-15 14:32:11 DROP TCP 10.0.0.55 192.168.1.100 54321 22`]*

Interpretar esta línea del log. ¿Qué ocurrió, cuándo, desde dónde y hacia dónde?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

## Sección C: Respuesta Corta (Preguntas 17-20)

*Responder en el espacio indicado. Cada pregunta vale 1 punto. Se evalúa precisión del concepto, no extensión de la respuesta.*

---

**P17.** Nombre dos servicios de Windows que deben deshabilitarse en una estación de trabajo militar que **no** es servidor de impresión ni permite acceso remoto al registro. Justifique brevemente por qué debe deshabilitarse cada uno.

*Respuesta:*

Servicio 1: ________________________________________________________________

Justificación: ________________________________________________________________

Servicio 2: ________________________________________________________________

Justificación: ________________________________________________________________

---

**P18.** Explique con sus propias palabras la diferencia entre filtrado de paquetes **stateful** y **stateless**. ¿Cuál de los dos usa Windows Defender Firewall?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P19.** Un usuario reporta que Microsoft Defender **no detectó** un virus conocido que infectó su máquina. Mencione **dos razones posibles** por las que esto podría haber ocurrido.

*Respuesta:*

Razón 1: ________________________________________________________________

________________________________________________________________

Razón 2: ________________________________________________________________

________________________________________________________________

---

**P20.** ¿Qué comando PowerShell utilizaría para verificar la **fecha de la última actualización de firmas** de Defender y para **forzar una actualización inmediata**? Escriba los dos comandos.

*Respuesta:*

Verificar fecha de última actualización:

```
________________________________________________________________
```

Forzar actualización inmediata:

```
________________________________________________________________
```

---

!!! danger "Solo instructor — Clave de Respuestas"

    ## Clave de Respuestas

    ### Sección A: Opción Múltiple (P1-P12)

    | Pregunta | Respuesta | Justificación |
    |----------|-----------|---------------|
    | P1 | **D** | El ransomware afecta las tres dimensiones: la exfiltración previa vulnera la Confidencialidad, la modificación/cifrado de archivos vulnera la Integridad, y el equipo inutilizable vulnera la Disponibilidad. |
    | P2 | **B** | `Get-LocalUser` es el cmdlet de PowerShell para listar cuentas locales. (A) es para Active Directory, (C) es el comando CMD equivalente pero no PowerShell puro, (D) muestra información del usuario actual. |
    | P3 | **B** | CIS Benchmark Level 1 para Windows 11 especifica Account Lockout Threshold = 5 intentos. |
    | P4 | **B** | PrintNightmare (CVE-2021-34527) es una vulnerabilidad crítica en el servicio Print Spooler que permite ejecución remota de código y escalación de privilegios a SYSTEM, documentada y explotada activamente. |
    | P5 | **B** | El Windows Security Center es la consola unificada que integra el estado de Defender AV, Firewall, SmartScreen, Device Health y Account Protection en una sola vista. |
    | P6 | **B** | La detección por firmas compara el código (hash o secuencia de bytes) de cada archivo contra la base de datos de definiciones. (A) describe detección comportamental, (C) describe MAPS pero no es la función principal, (D) describe SmartScreen. |
    | P7 | **C** | Una exclusión crea una zona ciega donde el AV no analiza. Si un atacante deposita malware en esa carpeta, pasa invisible. (A) es incorrecto — excluir una carpeta no afecta el rendimiento general. (D) es incorrecto — las exclusiones no expiran automáticamente. |
    | P8 | **B** | Quick Scan analiza memoria, startup, System32 y ubicaciones de autorrun — áreas de alta probabilidad de infección. Full Scan analiza todos los archivos. (A) es incorrecto — Quick Scan no filtra por fecha de modificación. |
    | P9 | **C** | El comportamiento predeterminado de WFAS para inbound es bloqueo silencioso (sin log por defecto). El log debe habilitarse explícitamente. (D) describe el comportamiento del antiguo firewall con notificaciones. |
    | P10 | **C** | Una red WiFi de hotel es una red no verificada y potencialmente hostil. El perfil Public aplica automáticamente para redes nuevas no marcadas como privadas o dominio. En entornos militares, la regla es: si hay duda, Public. |
    | P11 | **B** | El tráfico outbound permisivo permite que malware instalado se comunique con sus servidores C2 (Comando y Control). Un troyano puede exfiltrar datos y recibir instrucciones usando puertos de salida normales (como 443 HTTPS). |
    | P12 | **C** | WFAS filtra por IP, puerto y protocolo pero no puede inspeccionar el contenido de los paquetes. Deep Packet Inspection (DPI) requiere hardware/software especializado que analiza la carga de datos dentro de cada paquete. (A) es incorrecto — WFAS puede bloquear UDP. (B) es incorrecto — WFAS sí puede crear reglas por aplicación. (D) es incorrecto — WFAS funciona en todos los perfiles. |

    ---

    ### Sección B: Respuestas Esperadas para Pantallazos (P13-P16)

    **P13 — Panel rojo en Virus & threat protection:**
    Respuesta completa: el círculo rojo con X indica que la protección en tiempo real de Microsoft Defender está deshabilitada O que las definiciones de firmas están gravemente desactualizadas (más de 7 días). Acción inmediata: (1) abrir Windows Security → Virus & threat protection → Turn on, o (2) ejecutar `Update-MpSignature` en PowerShell para actualizar definiciones, o (3) ejecutar `Set-MpPreference -DisableRealtimeMonitoring $false` para rehabilitar la protección en tiempo real.

    *Criterio mínimo para obtener el punto:* El alumno debe identificar que indica un problema con Defender (protección deshabilitada o desactualizada) Y mencionar al menos una acción correctiva concreta.

    **P14 — Regla Block Outbound TCP 443:**
    Respuesta completa: esta regla bloquea todo el tráfico HTTPS saliente del equipo. Efecto: el equipo no puede acceder a ningún sitio web con HTTPS (la mayoría de sitios web modernos), no puede descargar actualizaciones de Windows, no puede hacer actualizaciones de definiciones de Defender. Es una configuración **inusual y probablemente incorrecta** — puede ser un error de configuración o, si fue creada intencionalmente por un atacante, un sabotaje para aislar el equipo de sus mecanismos de actualización.

    *Criterio mínimo:* Identificar que bloquea tráfico HTTPS saliente Y que es inusual/problemático.

    **P15 — EICAR en cuarentena:**
    Respuesta completa: EICAR (European Institute for Computer Antivirus Research) es un archivo de prueba estándar de la industria — 68 bytes de texto ASCII diseñados para que todos los AV lo detecten como si fuera malware. No es malware real, no puede causar daño. Está en cuarentena porque Defender funciona correctamente al detectarlo — es la verificación de que el AV opera. **No se debe restaurar** aunque sea un falso positivo inofensivo — en un entorno de producción, no se restaura ningún archivo de cuarentena sin autorización del oficial de seguridad. El archivo EICAR se usa exclusivamente para verificar que el AV funciona, no como archivo de trabajo.

    *Criterio mínimo:* Identificar que EICAR es un archivo de prueba (no malware real) Y que no se debe restaurar sin autorización.

    **P16 — Línea de log pfirewall.log:**
    Respuesta completa: el firewall bloqueó (DROP) un intento de conexión TCP desde el equipo con IP 10.0.0.55 al puerto 22 (SSH) del equipo local con IP 192.168.1.100, el día 15 de junio de 2026 a las 14:32:11. El puerto de origen en el equipo atacante era 54321. Interpretación: alguien desde 10.0.0.55 intentó conectarse al servicio SSH del equipo local y fue rechazado.

    *Criterio mínimo:* Identificar la acción (DROP), el protocolo (TCP), las IPs y el puerto destino (22/SSH), y la fecha/hora.

    ---

    ### Sección C: Respuestas Modelo para Respuesta Corta (P17-P20)

    **P17 — Servicios a deshabilitar:**

    Respuesta modelo: **Print Spooler** — debe deshabilitarse en equipos que no imprimen porque es el vector del exploit PrintNightmare (CVE-2021-34527) que permite escalación de privilegios a SYSTEM y ejecución remota de código. **Remote Registry** — debe deshabilitarse porque permite modificar el registro de Windows de forma remota; nadie debe poder alterar el registro desde la red en una estación de trabajo de usuario.

    Otras respuestas aceptables: SMBv1 (aunque técnicamente es un protocolo/feature de Windows, no un servicio en sí — aceptar si el alumno lo menciona como feature a deshabilitar).

    *Criterio mínimo:* Nombrar dos servicios correctos (Print Spooler y Remote Registry son la respuesta canónica) Y dar una justificación relacionada con seguridad para cada uno.

    **P18 — Stateful vs. Stateless:**

    Respuesta modelo: **Stateless**: el firewall evalúa cada paquete de forma independiente, solo considerando dirección IP, puerto y protocolo — sin recordar el contexto de paquetes anteriores. **Stateful**: el firewall mantiene una tabla de conexiones activas y recuerda el estado de cada conversación — si el equipo inició la conexión, permite automáticamente las respuestas sin necesidad de regla explícita de entrada. Windows Defender Firewall es **stateful**.

    *Criterio mínimo:* Explicar correctamente la diferencia (stateless=sin contexto, stateful=con contexto de conexión) Y responder correctamente que WFAS es stateful.

    **P19 — Razones por las que Defender no detectó un virus:**

    Respuesta modelo: (1) Las definiciones de Defender estaban desactualizadas — si el malware era nuevo y la firma no había sido descargada aún, Defender no podía detectarlo. (2) El malware es polimórfico — modifica su código entre infecciones, produciendo una variante cuya firma exacta no existía en la base de datos. Otras razones aceptables: (3) El archivo estaba en una carpeta excluida de los escaneos. (4) La protección en tiempo real estaba deshabilitada. (5) El malware usó técnicas de evasión que bypasearon la detección heurística.

    *Criterio mínimo:* Mencionar dos razones técnicamente correctas y coherentes con los temas de la unidad.

    **P20 — Comandos PowerShell para firmas:**

    Respuesta modelo:

    Verificar fecha: `(Get-MpComputerStatus).AntivirusSignatureLastUpdated`

    Forzar actualización: `Update-MpSignature`

    *Criterio mínimo:* Ambos comandos correctos. `Get-MpComputerStatus` sin filtrar la propiedad es parcialmente correcto (el alumno muestra que conoce el comando pero no cómo extraer el campo específico) — otorgar 0.5 puntos si el instructor considera apropiado, o 0 si se exige el comando exacto.

    ---

    ## Notas de Aplicación

    **Distribución del tiempo recomendada:**

    | Sección | Preguntas | Tiempo sugerido |
    |---------|-----------|----------------|
    | Sección A (Opción Múltiple) | P1-P12 | 20 minutos |
    | Sección B (Identificación de Pantallazos) | P13-P16 | 15 minutos |
    | Sección C (Respuesta Corta) | P17-P20 | 20 minutos |
    | Revisión final | — | 5 minutos |
    | **Total** | **20 preguntas** | **60 minutos** |

    **Proyección de pantallazos para Sección B:**

    Los pantallazos de P13 a P16 pueden obtenerse de la siguiente forma:
    - P13: captura de pantalla del Windows Security Center con protección en tiempo real deshabilitada (ir a Windows Security → Virus & threat protection → Manage settings → Real-time protection: Off).
    - P14: captura de la consola WFAS con la regla visible. Crear la regla previamente en un equipo de prueba.
    - P15: captura de la ventana de Protection History tras descargar y ejecutar el archivo EICAR de https://www.eicar.org/download-anti-malware-testfile/.
    - P16: captura del archivo pfirewall.log con la línea de ejemplo, o mostrar el texto en proyector.

    **Errores comunes esperados:**

    - P1: muchos alumnos eligen (C) Disponibilidad únicamente — reforzar que el ransomware moderno exfiltra datos antes de cifrar, afectando las tres dimensiones.
    - P9: confusión entre "bloqueado silenciosamente" y "bloqueado con registro" — el log debe habilitarse explícitamente; por defecto no hay log.
    - P20: algunos alumnos escriben `Get-MpComputerStatus` sin filtrar la propiedad — considerar si aceptar como parcialmente correcto según criterio del instructor.
    - P16: confusión entre IP origen y destino — recordar que src-ip es quien intentó conectarse, dst-ip es nuestro equipo.
