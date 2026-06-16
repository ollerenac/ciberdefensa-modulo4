---
# Horas asignadas: 4 hrs
# Tipo: Teoría
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

---

## La Superficie de Ataque del Ordenador

Un atacante que apunta a un ordenador con Windows 11 no ve lo mismo que el usuario que trabaja frente a él. El usuario ve su escritorio, sus documentos y sus aplicaciones. El atacante ve una colección de puntos de entrada posibles — cada uno una oportunidad para establecer acceso, moverse lateralmente o extraer información.

La **superficie de ataque** es el conjunto de todos los vectores posibles mediante los cuales un atacante no autorizado puede intentar entrar, interactuar con, o extraer datos de un sistema.

Pensemos en analogía militar: cuando se establece un perímetro de seguridad alrededor de un campamento, no solo se aseguran las puertas principales. Se identifican todas las vías de entrada posibles — caminos secundarios, ríos, accesos aéreos — y se aplican controles a cada uno. La superficie de ataque de un ordenador funciona igual.

### Los cuatro componentes de la superficie de ataque

**1. Puertos abiertos:** Cada puerto que escucha conexiones de red es una puerta potencial. Un puerto abierto indica que hay un servicio esperando instrucciones de la red. Si ese servicio tiene una vulnerabilidad, es un punto de entrada.

Para visualizar los puertos que escucha un equipo Windows 11, abrir CMD como administrador y ejecutar:

```cmd
netstat -an
```

La salida muestra columnas: Proto, Dirección local, Dirección extranjera, Estado. Las líneas con estado `LISTENING` son puertos abiertos esperando conexiones entrantes. El Técnico debe poder responder: ¿por qué está abierto este puerto? ¿Qué servicio lo usa? ¿Esa funcionalidad es necesaria en este equipo?

**2. Servicios en ejecución:** Cada servicio de Windows que corre en segundo plano es código que procesa datos, recibe entradas y tiene acceso al sistema operativo. Si un servicio tiene un error de programación (vulnerabilidad), un atacante puede aprovecharlo para ejecutar código malicioso.

**3. Cuentas de usuario:** Cada cuenta de usuario es una identidad que puede ser suplantada. Cuentas con contraseñas débiles, cuentas sin uso activo (huérfanas), o cuentas con privilegios excesivos son vectores de ataque de alto valor para los atacantes.

**4. Software instalado:** Cada aplicación instalada es código adicional que puede contener vulnerabilidades. El software desactualizado es especialmente peligroso porque sus vulnerabilidades ya son públicas y los atacantes las conocen.

!!! example "Aplicación en entorno castrense"
    Un equipo de reconocimiento despliega un puesto de mando avanzado. Antes de conectar el ordenador a la red de unidad, el Técnico de comunicaciones realiza una evaluación rápida de superficie de ataque: ejecuta `netstat -an` en CMD y verifica qué puertos están escuchando. Identifica que el puerto 445 (SMB) está abierto — necesario para compartir archivos en red, pero también el vector del ransomware WannaCry que en 2017 afectó a hospitales y empresas en todo el mundo. El Técnico confirma que la versión SMBv1 está deshabilitada antes de conectar el equipo. Esta verificación de cinco minutos puede evitar que un incidente de seguridad interrumpa la misión.

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

---

## Hardening Básico: CIS Benchmark Level 1

Un **benchmark de seguridad** es un conjunto de recomendaciones de configuración documentadas, consensuadas por expertos, diseñadas para reducir la superficie de ataque de un sistema específico. No son opcionales ni aspiracionales — son el estándar base de la industria.

El **Center for Internet Security (CIS)** publica benchmarks para los sistemas operativos, aplicaciones y dispositivos más usados. Para Windows 11, el CIS Benchmark tiene dos niveles:

- **Level 1:** Configuraciones aplicables en cualquier entorno, sin impacto operacional significativo. Todo equipo debe cumplir Level 1.
- **Level 2:** Configuraciones más restrictivas para entornos de alta seguridad. Pueden interferir con algunas funcionalidades comunes — evaluar antes de aplicar en producción.

Para los Técnicos del Ejército, el objetivo inicial es cumplir el Level 1. Los seis controles más relevantes para Windows 11 en entorno militar son:

| Control CIS L1 | Por qué importa | Cómo verificar |
|----------------|-----------------|---------------|
| **1. Deshabilitar cuenta Guest** | La cuenta Guest permite acceso sin contraseña. Es el primer vector que un atacante prueba en un sistema nuevo. | Ejecutar: `Get-LocalUser -Name "Guest" \| Select-Object Name, Enabled` — debe mostrar `Enabled: False` |
| **2. Contraseña mínima de 14 caracteres** | Contraseñas cortas son vulnerables a ataques de fuerza bruta. 14 caracteres con complejidad tardan años en romperse con hardware actual. | `secpol.msc → Account Policies → Password Policy → Minimum password length` |
| **3. Umbral de bloqueo: 5 intentos** | Limita los intentos de adivinar contraseñas. Después de 5 intentos fallidos, la cuenta se bloquea. | `secpol.msc → Account Lockout Policy → Account lockout threshold: 5` |
| **4. Deshabilitar SMBv1** | SMBv1 es el protocolo que explotó WannaCry en 2017 para propagarse entre equipos. No tiene uso legítimo en redes modernas. | `Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol` — debe mostrar `State: Disabled` |
| **5. Habilitar auditoría de inicio de sesión** | Registra cada intento de inicio de sesión (exitoso y fallido) en el Visor de eventos. Esencial para detectar ataques de fuerza bruta. | `secpol.msc → Local Policies → Audit Policy → Audit logon events: Success, Failure` |
| **6. Windows Update al día** | Los parches de seguridad corrigen vulnerabilidades conocidas. Un sistema sin parches recientes tiene vulnerabilidades públicas que cualquier atacante puede explotar. | `Settings → Windows Update → Check for updates` — debe mostrar "You're up to date" |

Estos seis controles son el mínimo absoluto antes de conectar cualquier equipo a la red de unidad. No son opcionales.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Un Técnico de comunicaciones recibe un laptop de campo que estuvo almacenado durante seis meses. Antes de conectarlo a la red de unidad, debe verificar su postura de seguridad. El procedimiento correcto:

    **Paso 1 — Verificar Windows Security Center:** Abre el panel y revisa los seis paneles. Si alguno está en rojo o amarillo, no conecta el equipo a la red hasta resolverlo.

    **Paso 2 — Verificar Windows Update:** `Settings → Windows Update → Check for updates`. Descarga e instala todos los parches pendientes. Un laptop guardado seis meses puede tener docenas de parches de seguridad atrasados, incluyendo correcciones para vulnerabilidades críticas.

    **Paso 3 — Auditar cuentas:** Ejecuta `Get-LocalUser` en PowerShell para listar todas las cuentas. Verifica que la cuenta Guest esté deshabilitada y que no existan cuentas desconocidas con privilegios de Administrador.

    **Paso 4 — Verificar SMBv1:** Confirma que el protocolo obsoleto está deshabilitado. Si está habilitado, ejecuta el comando de deshabilitación antes de conectar a la red.

    **Paso 5 — Verificar antivirus:** Actualiza las definiciones de Defender (`Update-MpSignature`) y ejecuta un escaneo rápido (`Start-MpScan -ScanType QuickScan`).

    Solo después de completar estos cinco pasos el Técnico conecta el equipo a la red de unidad. Este procedimiento toma aproximadamente 20 minutos y puede evitar que un equipo comprometido sea usado como puente de entrada para un atacante en la red.

---

## Resumen

1. La **Triada CIA** (Confidencialidad, Integridad, Disponibilidad) es el marco de referencia para evaluar toda amenaza y toda protección durante el curso.
2. La **superficie de ataque** incluye puertos abiertos, servicios en ejecución, cuentas de usuario y software instalado — reducirla es el objetivo del hardening.
3. El **Windows Security Center** proporciona una vista de estado unificada de todas las protecciones; un panel rojo requiere acción antes de conectar el equipo a la red.
4. El **principio de mínimo privilegio** dicta que nadie — ni usuarios ni procesos — debe tener más acceso del estrictamente necesario para su función.
5. El **CIS Benchmark Level 1** establece seis controles mínimos para Windows 11: deshabilitar Guest, contraseña de 14 chars, umbral de bloqueo en 5 intentos, deshabilitar SMBv1, auditoría de inicio de sesión activada, y Windows Update al día.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **CIS Benchmarks (sitio oficial):** https://www.cisecurity.org/cis-benchmarks — Descargar el benchmark gratuito para Windows 11. Requiere registro. El documento tiene cientos de controles — los Level 1 están marcados explícitamente.
- **Windows Security Center — Microsoft Learn:** https://learn.microsoft.com/es-es/windows/security/operating-system-security/system-security/windows-defender-security-center/windows-defender-security-center — Guía oficial con descripción detallada de cada panel.
- **Triada CIA en contexto de operaciones militares (NIST SP 800-53):** https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final — El estándar federal de EE.UU. que implementa la Triada CIA en controles específicos para sistemas de defensa.

---

*Siguiente: [Controles Defensivos (Parte 2)](controles-defensivos-p2.md)*
