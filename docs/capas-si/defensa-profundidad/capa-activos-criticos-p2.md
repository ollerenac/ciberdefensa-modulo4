---
# Horas asignadas: 3 hrs
# Tipo: Teoría
---

# Capa de Activos Críticos (Parte 2): Respuesta a Incidentes y Ejercicio Tabletop (CONT-28)

> Esta clase continúa **Capa 7: Activos Críticos** del modelo de defensa en profundidad establecido en [Parte 1](capa-humana-perimetral-p1.md).

> **Duración:** 3 horas (1 hora teoría + 2 horas ejercicio) | **Asignatura:** Capas SI | **Unidad:** Defensa en Profundidad

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Identificar las cuatro fases de respuesta a incidentes del NIST SP 800-61 y describir las actividades de cada fase.
- Aplicar el marco NIST SP 800-61 a un escenario de ransomware en una red militar.
- Participar activamente en un ejercicio tabletop asumiendo un rol definido y tomando decisiones fundamentadas bajo presión de tiempo.

---

> **Recapitulación:** En la Parte 1 (CONT-27) identificamos los activos críticos militares (C2, radios, SCIF, servidor de directorio), definimos criterios de criticidad y estudiamos los planes de continuidad (BCP/DRP, RTO/RPO). Esta clase añade la dimensión de respuesta: qué hacer cuando un incidente de seguridad ya está ocurriendo.

---

## Parte Teórica (1 hora): Fases de Respuesta a Incidentes — NIST SP 800-61

### ¿Qué es el NIST SP 800-61?

El NIST SP 800-61 (Computer Security Incident Handling Guide) es la guía publicada por el Instituto Nacional de Estándares y Tecnología de Estados Unidos que define el proceso estándar de respuesta a incidentes de seguridad. Es el marco de referencia más utilizado en organizaciones gubernamentales, militares y del sector privado a nivel mundial.

!!! note "Definición"
    El **NIST SP 800-61** define un **ciclo de respuesta a incidentes** de cuatro fases que proporciona un proceso estructurado para detectar, contener y recuperarse de incidentes de seguridad, minimizando el impacto y extrayendo lecciones para mejorar la defensa.

La relevancia para entornos militares es directa: los adversarios más sofisticados planifican sus ataques para maximizar el impacto antes de ser detectados. Sin un proceso de respuesta estructurado, la reacción es caótica, tardía e incompleta. El NIST SP 800-61 es ese proceso.

### Fase 1: Preparación

La Fase 1 ocurre **antes** del incidente. Es la más importante — los incidentes no se gestionan bien durante el incidente si no se prepararon bien antes.

**Actividades de preparación:**

- **Equipo de respuesta a incidentes (CSIRT):** Definir quién forma el equipo, sus roles y cómo se les contacta fuera del horario laboral. En un batallón, esto incluye el oficial de seguridad, el técnico de sistemas y el Comandante o su delegado.
- **Plan de respuesta a incidentes:** Documento que define el proceso paso a paso para los tipos de incidentes más probables. No se improvisa durante el incidente — se sigue el plan.
- **Herramientas y recursos:** Acceso a logs de sistemas, contacto con proveedores, lista de sistemas críticos y sus custodios, procedimientos de aislamiento de red.
- **Formación y ejercicios:** El personal que responde a incidentes debe haberlo practicado antes. Los tabletop exercises (como el de esta clase) son la forma más eficiente de practicar sin causar daño real.
- **Comunicaciones seguras fuera de banda:** Si la red principal está comprometida, ¿cómo se comunica el equipo de respuesta? Teléfonos móviles separados, radio táctica, canal de comunicaciones de contingencia.

!!! example "Aplicación en entorno castrense"
    El oficial de seguridad del batallón convoca una reunión antes del siguiente período operacional y define: (1) el equipo de respuesta a incidentes de 4 personas, (2) los números de teléfono de emergencia de cada uno, (3) un canal de radio de reserva para coordinación si la red principal cae, y (4) la lista de sistemas críticos con sus custodios. Este trabajo hecho antes del incidente determina si la respuesta tardará 2 horas o 2 días.

### Fase 2: Detección y Análisis

La Fase 2 comienza cuando hay indicios de que ha ocurrido o está ocurriendo un incidente. La detección es difícil — los atacantes trabajan para pasar desapercibidos.

**Fuentes de detección:**

| Fuente | Qué detecta | Limitación |
|--------|-------------|-----------|
| **Alertas del EDR** | Comportamiento anómalo en endpoints | Solo ve lo que pasa en los equipos donde está instalado |
| **Alertas del firewall** | Tráfico bloqueado, conexiones sospechosas | Solo ve tráfico en el perímetro de red |
| **Logs del sistema** | Accesos fallidos, cambios de configuración | Requiere monitoreo activo; sin monitoreo, el log existe pero nadie lo ve |
| **Reporte de usuarios** | "Mi ordenador se comporta raro"; "Apareció un mensaje extraño" | Depende de que el usuario lo reporte y sepa a quién |
| **Herramientas de SIEM** | Correlación de eventos de múltiples fuentes | Requiere configuración; las alertas sin clasificar son ruido |

**Análisis — preguntas que guían la respuesta:**

1. ¿Cuándo comenzó? (línea de tiempo del incidente)
2. ¿Qué sistemas están afectados? (alcance)
3. ¿Cómo entró el atacante? (vector de acceso inicial)
4. ¿Qué ha hecho el atacante desde que entró? (acciones realizadas)
5. ¿Sigue activo o el vector fue cerrado? (estado actual)

La respuesta a estas preguntas determina las acciones de la Fase 3.

### Fase 3: Contención, Erradicación y Recuperación

La Fase 3 es la fase de acción — detener el incidente, eliminar la amenaza y restaurar la operación normal.

**Contención (detener la propagación):**

El objetivo inmediato es evitar que el incidente se expanda. Las acciones dependen del tipo de incidente:

- **Aislamiento de red:** Desconectar el segmento de red afectado del resto (sin apagar los equipos, para preservar evidencia en memoria).
- **Desactivación de cuentas comprometidas:** Si se detecta que las credenciales de un usuario fueron robadas, deshabilitar la cuenta inmediatamente.
- **Bloqueo de comunicaciones C2:** Bloquear en el firewall las IPs de los servidores de comando y control del atacante, si han sido identificados.

**Erradicación (eliminar la amenaza):**

Una vez contenido, eliminar la amenaza del entorno:

- Limpiar o reimaginar los sistemas infectados.
- Eliminar el malware, las puertas traseras y las cuentas creadas por el atacante.
- Corregir la vulnerabilidad que permitió el acceso inicial (aplicar el parche, cambiar las credenciales comprometidas, corregir la configuración errónea).

**Recuperación (restaurar operaciones):**

- Restaurar sistemas desde backups verificados como limpios (un backup creado mientras el malware estaba activo puede estar infectado).
- Verificar que los sistemas restaurados funcionan correctamente antes de reconectarlos a la red de producción.
- Monitoreo intensivo post-restauración durante al menos 72 horas para detectar actividad residual del atacante.

!!! warning "No restaurar desde backup sin verificar el vector de entrada"
    Un error frecuente es restaurar el sistema desde el último backup disponible sin haber identificado y corregido cómo entró el atacante. Si el vector de entrada sigue abierto, el atacante volverá a comprometer el sistema restaurado. La erradicación (corregir el vector) debe completarse antes de la recuperación.

### Fase 4: Actividad Post-Incidente

La Fase 4 ocurre **después** de que el incidente está resuelto y las operaciones se han normalizado. Es la más omitida y, a largo plazo, la más valiosa.

**Informe de lecciones aprendidas (Post-Incident Review):**

Dentro de los 10-15 días posteriores al incidente, el equipo de respuesta se reúne para responder:

- ¿Qué funcionó bien en la respuesta?
- ¿Qué falló o tardó más de lo esperado?
- ¿Qué cambios en controles, procedimientos o formación podrían prevenir o detectar más rápido un incidente similar?
- ¿Se cumplieron los RTO definidos para los sistemas críticos afectados?

El informe de lecciones aprendidas es el mecanismo por el que la organización mejora continuamente su capacidad de respuesta. Un incidente sin lecciones aprendidas documentadas es una oportunidad de mejora desperdiciada.

---

!!! danger "Ejercicio Tabletop: Ransomware en la Red de la Unidad (2 horas)"

    ## Tabletop Exercise: Ransomware en la Red de la Unidad (2 horas)

    ### Preparación (instructor)

    **División del aula:**

    1. Dividir el aula en grupos de 3-4 personas. Cada grupo representa el equipo de respuesta a incidentes de la "Jefatura de Comunicaciones del Batallón de Comunicaciones N°3".
    2. Dentro de cada grupo, asignar los tres roles (tarjetas a continuación). Si hay 4 personas, dos comparten el rol de Técnico de Sistemas.
    3. El instructor proyecta cada Inject Card en pantalla y lee el escenario en voz alta.
    4. Los grupos tienen el tiempo indicado en cada inject para discutir y registrar sus decisiones en papel.
    5. Al finalizar cada inject, el instructor pide a 1-2 grupos que compartan sus decisiones y guía la discusión.
    6. Materiales que necesita el instructor: esta página proyectada. No se necesita nada más.

    ---

    ### Tarjetas de Rol

    !!! info "Tarjeta de Rol: Comandante"
        **Tu rol:** Eres el Comandante de la Jefatura de Comunicaciones del Batallón N°3 (JC-BC3). Tienes autoridad para tomar decisiones sobre operaciones, comunicar hacia arriba (Cuartel General) y hacia abajo (personal de la unidad), y autorizar interrupciones operacionales si son necesarias para contener el incidente.

        **Lo que sabes al inicio del ejercicio:**
        - La JC-BC3 opera la red de comunicaciones del batallón: 45 equipos, servidor C2, servidores de directorio, radios tácticas IP.
        - El batallón tiene un ejercicio conjunto programado para mañana a las 08:00 hrs. Cancelarlo requeriría coordinación con el Cuartel General con 12 horas de anticipación.
        - No tienes conocimiento técnico profundo — para eso están el Oficial de Comunicaciones y el Técnico.

        **Decisiones que debes tomar:**
        - ¿Cuándo y cómo informa al Cuartel General sobre el incidente?
        - ¿Autoriza la interrupción de servicios para contener el incidente aunque eso afecte el ejercicio de mañana?
        - ¿Qué comunica al personal de la unidad y cuándo?
        - ¿Cuándo declara que la situación está bajo control?

    !!! info "Tarjeta de Rol: Oficial de Comunicaciones"
        **Tu rol:** Eres el Oficial de Comunicaciones de la JC-BC3. Tu función es coordinar la respuesta técnica, servir de enlace entre el Técnico de Sistemas y el Comandante, y tomar decisiones operacionales de seguridad dentro de tu nivel de autoridad.

        **Lo que sabes al inicio del ejercicio:**
        - La red interna de la JC-BC3 usa VLANs (VLAN 40 para sistemas tácticos, VLAN 30 para usuarios, VLAN 20 para servidores).
        - Hay un servidor de backup diario — el último backup completo fue ayer a las 23:00 hrs.
        - El sistema C2 está en VLAN 40, separado de la VLAN de usuarios.
        - Tienes acceso a la consola de administración de la red y puedes aislar VLANs.

        **Decisiones que debes tomar:**
        - ¿Qué segmentos de red aislar primero y en qué orden?
        - ¿Cuándo involucrar al Cuartel General técnicamente?
        - ¿Cómo coordinar la comunicación entre el Técnico y el Comandante?
        - ¿Qué sistemas son los primeros en recuperar y en qué secuencia?

    !!! info "Tarjeta de Rol: Técnico de Sistemas"
        **Tu rol:** Eres el Técnico de Sistemas de la JC-BC3. Tienes el conocimiento técnico más profundo del grupo — eres quien diagnostica el incidente, ejecuta las acciones técnicas de contención y recuperación, y asesora al Oficial de Comunicaciones sobre opciones y consecuencias.

        **Lo que sabes al inicio del ejercicio:**
        - Tienes acceso de administrador a todos los servidores y equipos de la red.
        - El EDR está instalado en 40 de los 45 equipos (5 equipos más antiguos no lo soportan).
        - El servidor de directorio activo es el más crítico: si cae, nadie puede autenticarse en ningún sistema.
        - Los backups están en un servidor separado, en la VLAN 20, pero no has verificado si están cifrados también.

        **Decisiones que debes tomar:**
        - ¿Cómo determinas el alcance inicial de la infección?
        - ¿Apagas los equipos infectados o los mantienes encendidos para preservar evidencia?
        - ¿Cómo verificas si los backups están limpios antes de usarlos para recuperar?
        - ¿En qué orden reconstruyes los sistemas afectados?

    ---

    ### Inject Cards

    !!! example "Inject 1 — Detección (~20 min)"
        **Escenario:**

        Son las 14:37 del martes. El Técnico de Sistemas recibe una alerta del EDR en su consola: en los últimos 4 minutos, 12 equipos de la VLAN 30 (usuarios) han mostrado actividad inusual — el proceso `svchost.exe` está cifrando archivos en las carpetas compartidas de red. Simultáneamente, varios técnicos reportan que sus pantallas muestran un mensaje en inglés con fondo rojo: "YOUR FILES HAVE BEEN ENCRYPTED. Pay 5 BTC to recover them."

        Los primeros análisis del EDR indican que el vector de entrada fue un archivo Excel malicioso recibido por correo electrónico hace 3 horas. El malware se propagó por la red aprovechando recursos compartidos con contraseñas débiles. El malware se propagó por la red aprovechando el protocolo SMBv1 — el mismo vector del ransomware WannaCry estudiado en la Unidad "En el Ordenador" (2017). Los equipos de la VLAN 40 (sistemas tácticos y C2) no muestran alertas todavía.

        **Estado actual:** 12 equipos VLAN 30 cifrados. VLAN 40 aparentemente limpia. Servidor de directorio (VLAN 20) sin alertas pero accesible desde VLAN 30.

        ---

        **Pregunta para los equipos** (20 min de discusión):

        1. ¿Qué hace cada rol en los próximos 30 minutos? (Comandante: ¿notifica ya al CG? Oficial: ¿qué segmentos aisla? Técnico: ¿cómo confirma el alcance?)
        2. ¿Se aisla la VLAN 30 aunque eso desconecte a los usuarios que todavía pueden trabajar?
        3. ¿Se apagan los equipos cifrados o se mantienen encendidos? ¿Por qué?

    !!! example "Inject 2 — Contención (~20 min)"
        **Escenario:**

        Son las 15:15 hrs. El Oficial de Comunicaciones ha aislado la VLAN 30. El Técnico confirma que el servidor de directorio (VLAN 20) también muestra 3 archivos cifrados en su disco de datos — el ransomware llegó antes de que se aislara la VLAN. El directorio activo sigue funcionando (los archivos de datos del servicio no fueron afectados todavía) pero si continúa la cifración podría caer en los próximos 30-60 minutos.

        El Técnico también descubre que el servidor de backup está en la VLAN 20 y tiene acceso montado a carpetas de la VLAN 30 — no está cifrado todavía pero tiene conectividad con el área infectada.

        El Comandante llama al Cuartel General para reportar el incidente. El oficial de guardia del CG pregunta: "¿Pueden garantizar que los sistemas C2 están limpios para el ejercicio de mañana?"

        **Estado actual:** VLAN 30 aislada (12 equipos cifrados). VLAN 20 parcialmente afectada. Servidor de backup en riesgo. VLAN 40 limpia. Ejercicio de mañana en 17 horas.

        ---

        **Pregunta para los equipos** (20 min de discusión):

        1. ¿Se aisla también la VLAN 20 aunque eso derribe el directorio activo y deje sin autenticación a toda la red?
        2. ¿Se desconecta el servidor de backup de la red aunque eso interrumpa los backups automáticos?
        3. ¿Qué le dice el Comandante al CG sobre el ejercicio de mañana?

    !!! example "Inject 3 — Erradicación (~20 min)"
        **Escenario:**

        Son las 17:00 hrs. La contención está completada: todas las VLANs afectadas están aisladas, el servidor de backup fue desconectado manualmente antes de ser cifrado. El directorio activo sobrevivió con daño mínimo.

        El Técnico ha identificado el vector: un fichero adjunto malicioso en un correo electrónico recibido por 3 técnicos. El adjunto explotó una vulnerabilidad en la macro de Excel. La propagación usó SMBv1 y contraseñas débiles en recursos compartidos. Los 5 equipos sin EDR fueron los más afectados y no generaron alertas.

        El Técnico propone: (a) reimaginar todos los equipos afectados desde cero, o (b) intentar limpiar con herramientas antimalware. La opción (a) tarda 8 horas; la opción (b) tarda 2 horas pero no garantiza eliminación completa.

        También debe decidir si restaurar desde el backup del lunes a las 23:00 hrs (pierden 18 horas de trabajo) o intentar recuperar archivos individuales de los equipos cifrados.

        **Estado actual:** Todos los sistemas aislados. Vector identificado. Sin actividad de cifrado nueva en las últimas 2 horas.

        ---

        **Pregunta para los equipos** (20 min de discusión):

        1. ¿Reimaginar (8 horas, certeza) o limpiar (2 horas, incertidumbre)? ¿Cambia la respuesta si el ejercicio de mañana está en juego?
        2. ¿Se deshabilita SMBv1 en todos los equipos antes de reconectarlos? ¿Hay riesgo de que algo deje de funcionar?
        3. ¿Se restaura desde el backup de ayer o se intenta recuperar archivos cifrados? ¿Cómo verifica el Técnico que el backup no está infectado?

    !!! example "Inject 4 — Recuperación (~20 min)"
        **Escenario:**

        Son las 23:30 hrs del martes. El Técnico ha completado la reimaginación de los 12 equipos cifrados, restaurado desde el backup del lunes, deshabilitado SMBv1 en toda la red, y forzado un cambio de contraseñas a todos los usuarios. El directorio activo está funcionando normalmente. Los 5 equipos sin EDR tienen el agente instalado ahora.

        El Oficial de Comunicaciones propone reconectar la VLAN 30 a la red completa. El Técnico pide 2 horas de monitoreo intensivo antes de hacerlo. El Comandante recibe una llamada del CG: el ejercicio de mañana se mantiene pero con alcance reducido — solo los sistemas C2 (VLAN 40) deben estar operativos al 100%.

        La VLAN 40 nunca fue comprometida. Los sistemas C2 funcionan correctamente.

        **Estado actual:** 12 equipos reimaginados y limpios. Directorio activo operativo. VLAN 30 lista para reconectar pendiente de monitoreo. VLAN 40 limpia y operativa. Ejercicio en 8.5 horas.

        ---

        **Pregunta para los equipos** (20 min de discusión):

        1. ¿Se reconecta la VLAN 30 esta noche (más riesgo, más funcionalidad para mañana) o se espera hasta después del ejercicio?
        2. ¿Qué monitoreo intensivo se pone en marcha durante las próximas horas y quién lo hace?
        3. ¿Cuándo y cómo se comunica al personal de la unidad que el incidente está resuelto?
        4. ¿Qué tres lecciones aprendidas se llevan al informe post-incidente?

    ---

    ### Debrief (10 min)

    **Preguntas de cierre para discusión con toda la clase:**

    1. ¿En qué momento del incidente fue más difícil tomar una decisión? ¿Por qué?
    2. ¿Qué habría pasado si no hubiera habido un servidor de backup o si los backups también hubieran sido cifrados?
    3. El incidente usó SMBv1 — el mismo vector de WannaCry (2017). ¿Qué dice eso sobre la importancia de la gestión de parches que estudiamos en CONT-24?
    4. ¿Qué fases del NIST SP 800-61 estuvieron presentes en los cuatro injects? ¿Cuál fase fue la más corta? ¿Por qué?
    5. Si la VLAN 40 (sistemas C2) hubiera sido comprometida, ¿cómo habría cambiado el orden de recuperación?

---

*Continúa en: [Examen Final — Capas SI](examen-final.md)*
