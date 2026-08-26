---
# Horas asignadas: 4 hrs
# Tipo: Teoría
---

# ISO 27000 — Generalidades (Parte 1)

> **Duración:** 4 horas | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar el origen y propósito de los estándares internacionales de seguridad de la información.
- Identificar las principales normas de la familia ISO/IEC 27000 y describir el rol de cada una.
- Definir qué es un Sistema de Gestión de la Seguridad de la Información (SGSI) y explicar su estructura conceptual.
- Reconocer por qué las organizaciones adoptan un SGSI como marco de referencia, sin confundirlo con el proceso de certificación.

---

## Por qué Existen los Estándares de Seguridad

Antes de hablar de normas específicas, es importante entender el problema que vinieron a resolver.

A finales del siglo XX, las organizaciones comenzaron a depender masivamente de sistemas informáticos para procesar información crítica. Sin embargo, cada organización protegía esa información de manera diferente — algunas bien, muchas mal, y casi ninguna de forma documentada o auditable. Cuando ocurría un incidente, era difícil saber qué había fallado y por qué, porque no existía un conjunto acordado de requisitos mínimos.

El primer intento serio de crear un lenguaje común fue el estándar británico **BS 7799**, publicado en 1995 por el British Standards Institution. BS 7799 fue un documento de buenas prácticas orientado a ayudar a las organizaciones a proteger su información de forma sistemática. Fue tan influyente que la Organización Internacional de Normalización (ISO) lo adoptó y lo convirtió en un estándar internacional.

!!! note "Definición"
    Un **estándar internacional** es un documento técnico consensuado por expertos de múltiples países, que establece requisitos, guías o características para un producto, proceso o servicio. Los estándares ISO son voluntarios — ninguna ley obliga a cumplirlos — pero se convierten en requisitos de facto cuando clientes, socios o reguladores los exigen.

### La evolución hacia la familia ISO/IEC 27000

La transición de BS 7799 a la familia ISO 27000 ocurrió en etapas:

| Año | Hito |
|-----|------|
| 1995 | BS 7799 Parte 1 — Código de prácticas (buenas prácticas, no requisitos) |
| 1998 | BS 7799 Parte 2 — Especificación del SGSI (primeros requisitos auditables) |
| 2000 | ISO/IEC 17799 — BS 7799 Parte 1 adopta el número ISO |
| 2005 | ISO/IEC 27001:2005 — Primera versión del estándar de gestión con ese número |
| 2013 | ISO/IEC 27001:2013 — Revisión mayor, Anexo A con 114 controles en 14 dominios |
| 2022 | ISO/IEC 27001:2022 — Versión vigente, Anexo A con 93 controles en 4 temas |
| 2026 | ISO/IEC 27000:2026 — Panorama, conceptos, principios y relaciones de la familia actualizados |

Conviene distinguir dos normas vigentes: **ISO/IEC 27000:2026** ofrece la introducción conceptual a la familia, mientras **ISO/IEC 27001:2022** contiene los requisitos certificables del SGSI. Los números de cláusulas y controles usados en este curso corresponden a ISO/IEC 27001:2022.

---

## La Familia ISO/IEC 27000

La "familia ISO 27000" no es una norma única — es un conjunto de estándares relacionados, cada uno con un propósito específico. Funcionan como una biblioteca: ISO 27001 es el libro central que establece los requisitos, y los demás son referencias que se usan según la necesidad.

!!! note "Definición"
    La **familia ISO/IEC 27000** es el conjunto de estándares internacionales publicados conjuntamente por ISO e IEC que cubren la gestión de la seguridad de la información. Los estándares de esta familia comparten vocabulario, conceptos y estructura, y están diseñados para usarse en conjunto.

| Norma | Propósito | Qué entrega |
|-------|-----------|-------------|
| **ISO/IEC 27000:2026** | Panorama, conceptos y principios generales | Mapa para comprender la familia y la relación entre sus normas |
| **ISO/IEC 27001:2022** | Requisitos para un SGSI (auditable) | La lista de "qué debe hacer" — base para la certificación si se desea |
| **ISO/IEC 27002:2022** | Guía de implementación de controles | El "cómo hacerlo" — 93 controles con orientación práctica |
| **ISO/IEC 27005:2022** | Gestión del riesgo de seguridad de la información | El proceso completo para identificar, analizar y tratar riesgos |
| **ISO/IEC 27017** | Controles para servicios en la nube (referencia) | Extensión de 27002 para entornos cloud |
| **ISO/IEC 27035** | Gestión de incidentes de seguridad (referencia) | Proceso de respuesta a incidentes alineado con la familia ISO |

### Relación entre las normas

La forma más sencilla de entender la relación es esta:

- **ISO 27001** es el jefe: dice **qué** debe existir en una organización que gestiona la seguridad de su información.
- **ISO 27002** es el técnico: orienta **cómo** implementar los controles que la organización selecciona según sus riesgos.
- **ISO 27005** es el analista de riesgo: dice **cómo** identificar y tratar los riesgos que ISO 27001 pide gestionar.
- **ISO 27000** es el mapa: explica los **conceptos, principios y relaciones** que permiten navegar la familia.

Una organización que quiere implementar un SGSI completo lee ISO 27001 para saber qué debe hacer, consulta ISO 27002 para saber cómo hacerlo, y usa ISO 27005 para gestionar los riesgos que su SGSI debe controlar.

!!! tip "Para el alumno"
    No es necesario leer todas estas normas. En este curso se cubre el contenido conceptual que necesitan para trabajar en una organización que ya tiene un SGSI o que está construyendo uno. Los números de norma son referencias — saber que "el control de acceso está en ISO 27002 tema 5, control 5.15" les permite buscar la guía correcta cuando la necesiten.

---

## Qué es un SGSI

El concepto central de ISO 27001 es el **Sistema de Gestión de la Seguridad de la Información** (SGSI). Entender qué es un SGSI es el primer paso para entender todo lo demás.

!!! note "Definición"
    Un **SGSI** (en inglés: *Information Security Management System*, ISMS) es el conjunto de políticas, procedimientos, controles técnicos y responsabilidades organizativas que una organización establece para gestionar de forma sistemática los riesgos para la seguridad de su información.

La palabra clave es **sistemática**. No se trata de instalar un antivirus o poner una contraseña. Se trata de tener un sistema — documentado, revisado, mejorado — que gestione la seguridad de manera continua y controlada.

Una analogía militar: el SGSI es equivalente a un sistema de gestión de la seguridad física de una instalación. No basta con poner un guardia en la puerta. El sistema completo incluye: definir qué zonas son restringidas y por qué, establecer quién puede entrar a cada zona y con qué autorización, documentar los procedimientos de patrullaje y verificación, registrar los incidentes de acceso, revisar los procedimientos periódicamente, y mejorarlos cuando se detectan fallas. Eso es gestión sistemática — no improvisación.

### Los componentes de un SGSI

Un SGSI bien construido tiene cuatro componentes fundamentales:

**1. Alcance y contexto:** Qué información protege el SGSI, qué sistemas cubre, qué unidades organizativas incluye. No todos los activos de una organización necesitan el mismo nivel de protección — el alcance define el perímetro del sistema de gestión.

**2. Evaluación de riesgos:** El proceso sistemático para identificar qué puede salir mal, qué tan probable es y qué impacto tendría. Sin evaluación de riesgos, los controles que se implementen pueden ser incorrectos — demasiados donde no se necesitan, muy pocos donde sí.

**3. Controles de seguridad:** Las medidas técnicas, físicas y organizativas que reducen los riesgos identificados. ISO 27002:2022 proporciona un catálogo de 93 controles organizados en 4 temas como menú de opciones — la organización selecciona los que corresponden a su contexto.

**4. Ciclo de mejora continua:** El SGSI no es un proyecto que se completa una vez. Es un ciclo permanente: planear, ejecutar, verificar, mejorar. El ciclo PDCA — que se estudiará en la Parte 2 — es el motor de esta mejora continua.

!!! warning "El SGSI no es una lista de compras tecnológica"
    Un error frecuente es pensar que implementar un SGSI significa comprar herramientas de seguridad. El SGSI es principalmente un sistema de gestión — documentación, responsabilidades, procesos, decisiones sobre riesgo. La tecnología es uno de sus componentes, no el único ni necesariamente el más importante.

---

## Ejemplo Integrador — SGSI de una Sección de Comunicaciones

> **Duración sugerida:** 60 minutos | **Propósito:** recorrer un SGSI completo sin entrar todavía en el detalle de las cláusulas de ISO/IEC 27001

La **Sección de Comunicaciones del Regimiento de Infantería N.° 8 (SC-RI8)** tiene 15 integrantes. Opera 24 radios tácticas, seis terminales de mando y control (C2), un servidor de archivos, una sala de comunicaciones y documentación impresa con planes de frecuencias. La rotación de personal es frecuente y parte del equipamiento se despliega fuera de la base.

El Comandante no necesita solamente saber qué herramientas están instaladas. Necesita saber **qué información sostiene la misión, qué puede afectarla, quién responde por ella y si las medidas de seguridad funcionan**. Para responder esas preguntas, la SC-RI8 establece el siguiente SGSI.

### Componente 1 — Alcance y contexto

**Qué pregunta responde:** ¿qué protegerá el SGSI, para qué misión y dentro de qué límites?

- **Misión que se debe sostener:**
    - Mantener comunicaciones confiables entre el puesto de mando y las unidades subordinadas.
    - Evitar que personal no autorizado conozca órdenes, ubicaciones, frecuencias o capacidades operativas.
    - Poder continuar operando, aunque un equipo falle, se pierda o sea comprometido.
- **Alcance propuesto:**
    - Incluye al personal de la SC-RI8, la sala de comunicaciones, los radios tácticos, las terminales C2, el servidor de archivos y los soportes físicos o digitales que contienen planes de comunicaciones.
    - Incluye los procesos de asignación de equipos, administración de cuentas, respaldo, mantenimiento, despliegue, devolución y reporte de incidentes.
    - Incluye a proveedores o talleres externos mientras tengan acceso a equipos o información de la sección.
    - Excluye los sistemas administrativos del Regimiento que son gestionados por otra dependencia; la exclusión se documenta y se identifica quién es responsable de ellos.
- **Partes interesadas y necesidades:**
    - El **Comandante** necesita disponibilidad, información para decidir y riesgos expresados en términos de impacto sobre la misión.
    - Los **operadores** necesitan procedimientos breves que puedan ejecutar bajo presión.
    - El **personal técnico** necesita configuraciones autorizadas, inventarios correctos y responsables definidos.
    - Las **unidades apoyadas** necesitan canales disponibles y mecanismos alternativos de comunicación.
    - Los **proveedores** necesitan reglas contractuales sobre acceso, custodia, mantenimiento y eliminación de información.
- **Responsabilidades mínimas:**
    - El Comandante aprueba el alcance, la política y los riesgos que se decida aceptar.
    - El Jefe de Comunicaciones administra el SGSI y reporta sus resultados.
    - Cada custodio responde por los equipos que recibe.
    - El administrador de sistemas gestiona cuentas, configuraciones, registros y respaldos.
    - Todo integrante debe reportar pérdidas, accesos sospechosos y fallas de seguridad.
- **Evidencias que produce este componente:**
    - Documento de alcance aprobado.
    - Organigrama o matriz de responsabilidades.
    - Lista de procesos, ubicaciones, terceros y activos incluidos.
    - Registro y justificación de exclusiones.
- **Alternativas de implementación:**
    - **Alcance reducido:** comenzar solo con el servidor, las terminales C2 y la sala de comunicaciones. Es más rápido y manejable, pero puede dejar fuera riesgos de los radios desplegados.
    - **Alcance completo:** incluir desde el inicio todos los equipos, personas, procesos y despliegues. Ofrece mayor cobertura, pero requiere más recursos y coordinación.
    - **Implementación progresiva:** comenzar con los activos más críticos y ampliar el alcance por etapas con fechas y responsables definidos. Es la alternativa más realista si la unidad tiene recursos limitados.

!!! tip "Idea para explicar"
    El alcance es como el perímetro de responsabilidad asignado a una unidad: debe indicar qué se defiende, quién lo defiende y dónde termina la responsabilidad. Lo que queda fuera no desaparece; debe tener otro responsable conocido.

### Componente 2 — Evaluación de riesgos

**Qué pregunta responde:** ¿qué puede impedir la misión, por qué podría ocurrir y qué riesgo merece atención primero?

- **Activos que la SC-RI8 identifica:**
    - Planes de frecuencias, órdenes y archivos operativos.
    - Radios, terminales C2, servidor, dispositivos de respaldo y material criptográfico.
    - Servicios de red y energía.
    - Conocimiento y experiencia del personal.
    - Confianza de las unidades que reciben el servicio de comunicaciones.
- **Escenarios de riesgo iniciales:**
    - Las cuentas genéricas del servidor permiten modificar o extraer archivos sin identificar al responsable.
    - Un radio se pierde durante el despliegue y contiene parámetros de comunicación todavía válidos.
    - Una falla eléctrica daña el servidor y no existe un respaldo recuperable.
    - Un proveedor recibe un equipo para mantenimiento sin que se elimine la información sensible.
    - Un operador nuevo configura incorrectamente una terminal porque no recibió instrucción ni una configuración de referencia.
- **Método sencillo de valoración:**
    - La probabilidad se califica como 1 baja, 2 media o 3 alta.
    - El impacto sobre la misión se califica como 1 bajo, 2 medio o 3 alto.
    - El nivel se obtiene multiplicando probabilidad por impacto; la unidad atiende primero los riesgos altos.
    - Los criterios se acuerdan antes de calificar para que “alto” signifique lo mismo para todos.
- **Ejemplo de registro de riesgo:**
    - **Activo:** servidor de archivos operativos.
    - **Amenaza:** acceso no autorizado.
    - **Vulnerabilidad:** cuentas genéricas y contraseñas compartidas.
    - **Consecuencia:** pérdida de confidencialidad y modificación de órdenes sin trazabilidad.
    - **Probabilidad:** 3, porque muchas personas conocen la contraseña y no se puede revocar individualmente.
    - **Impacto:** 3, porque archivos alterados o divulgados pueden afectar una operación.
    - **Nivel:** 9, riesgo alto.
    - **Propietario del riesgo:** Jefe de Comunicaciones, porque tiene autoridad para decidir y asignar recursos sobre el proceso afectado.
- **Alternativas de tratamiento del ejemplo:**
    - **Modificar:** crear cuentas individuales, limitar privilegios y revisar los registros de acceso.
    - **Evitar:** dejar de almacenar información operativa sensible en ese servidor hasta que sea protegido.
    - **Compartir:** contratar un servicio administrado puede distribuir responsabilidades, pero no elimina la responsabilidad de la unidad sobre su información.
    - **Retener:** aceptar temporalmente el riesgo requiere justificación, autoridad competente, fecha de revisión y medidas provisionales; no equivale a ignorarlo.
- **Evidencias que produce este componente:**
    - Inventario de activos y propietarios.
    - Criterios de probabilidad, impacto y aceptación.
    - Registro de riesgos con responsables y fechas de revisión.
    - Decisiones de tratamiento o aceptación aprobadas.
- **Alternativas metodológicas:**
    - Una matriz cualitativa de 3 × 3 es rápida y adecuada para comenzar.
    - Una matriz de 5 × 5 ofrece mayor diferenciación, pero puede crear una precisión aparente si no existen buenos datos.
    - Un análisis cuantitativo estima pérdidas monetarias o tiempos de interrupción; es útil para decisiones de inversión, pero exige datos confiables y mayor madurez.

!!! tip "Idea para explicar"
    El riesgo no es solamente una vulnerabilidad. Es un escenario que conecta un activo valioso, una amenaza, una debilidad y una consecuencia para la misión.

### Componente 3 — Controles de seguridad

**Qué pregunta responde:** ¿qué medidas concretas reducirán los riesgos seleccionados?

- **Controles organizacionales:**
    - Aprobar una política de uso de los sistemas y un procedimiento de altas, cambios y bajas de usuarios.
    - Mantener un inventario con número de serie, custodio, ubicación, configuración y estado de cada equipo.
    - Establecer reglas de seguridad para proveedores y una cadena de custodia durante el mantenimiento.
    - **Alternativa simple:** formularios en papel numerados y revisados semanalmente.
    - **Alternativa de mayor capacidad:** sistema electrónico de inventario y flujo de aprobaciones con alertas por vencimiento.
- **Controles sobre las personas:**
    - Dar una inducción antes de conceder acceso y realizar ejercicios periódicos de reporte de incidentes.
    - Separar funciones críticas: quien solicita una cuenta no debe aprobarla y crearla sin una segunda revisión.
    - Retirar accesos y recuperar equipos inmediatamente cuando una persona cambia de función o deja la unidad.
    - **Alternativa de bajo costo:** charla de ingreso, tarjeta de bolsillo y comprobación oral del procedimiento.
    - **Alternativa más robusta:** capacitación por funciones, simulaciones, evaluación práctica y registro de competencia.
- **Controles físicos:**
    - Restringir la sala de comunicaciones, registrar ingresos y acompañar a visitantes.
    - Guardar respaldos y material sensible en un lugar distinto del equipo principal.
    - Verificar la devolución y el estado de radios al finalizar cada operación.
    - **Alternativa manual:** cerradura, lista autorizada y libro de ingresos revisado por el responsable.
    - **Alternativa automatizada:** credencial individual, registro electrónico, alarma y revisión periódica de accesos.
- **Controles tecnológicos para las cuentas compartidas:**
    - Crear una cuenta nominativa por usuario y asignar solamente los privilegios necesarios.
    - Exigir un segundo factor para administradores cuando la plataforma lo permita.
    - Registrar accesos y alertar por intentos repetidos, horarios inusuales o descarga masiva de archivos.
    - **Si el sistema antiguo no admite cuentas individuales:** usar cuentas nominativas en un equipo intermediario, custodiar las credenciales privilegiadas, registrar cada autorización y planificar el reemplazo del sistema.
- **Controles tecnológicos para la disponibilidad:**
    - Realizar respaldos según la criticidad de la información y probar restauraciones, no solo comprobar que el archivo de respaldo existe.
    - Mantener configuraciones de referencia y repuestos para los equipos esenciales.
    - **Alternativa básica:** respaldo cifrado desconectado y restauración de prueba mensual.
    - **Alternativa de recuperación rápida:** réplica en un segundo servidor y conmutación ensayada; cuesta más y también debe protegerse.
- **Controles para la pérdida de un radio:**
    - Registrar qué persona recibe cada equipo y qué parámetros contiene.
    - Exigir reporte inmediato y ejecutar el cambio de claves o frecuencias definido para el incidente.
    - **Si el equipo permite borrado remoto:** activarlo y probarlo bajo condiciones controladas.
    - **Si no lo permite:** reducir el periodo de validez del material, disponer de claves de contingencia y entrenar el procedimiento de cambio.
- **Cómo se decide entre alternativas:**
    - El control elegido debe reducir el riesgo hasta un nivel aceptable sin impedir la misión.
    - Deben considerarse costo, tiempo, personal disponible, facilidad de operación, dependencia de conectividad y capacidad de trabajar en modo degradado.
    - Una medida temporal debe tener responsable y fecha de sustitución; de lo contrario, tiende a convertirse en una debilidad permanente.
- **Evidencias que produce este componente:**
    - Políticas y procedimientos aprobados.
    - Inventarios, autorizaciones, registros de acceso y listas de capacitación.
    - Configuraciones técnicas, alertas, reportes de respaldo y resultados de restauración.
    - Contratos, actas de entrega, cadenas de custodia y reportes de incidentes.

!!! tip "Idea para explicar"
    Un control no es únicamente una herramienta. “Proteger las cuentas” exige una regla, un responsable, una configuración, evidencia de ejecución y una forma de comprobar que la medida funciona.

### Componente 4 — Ciclo de mejora continua

**Qué pregunta responde:** ¿cómo sabe el mando si el SGSI funciona y cómo se corrige cuando cambia la realidad?

- **Planear:**
    - La SC-RI8 decide eliminar las cuentas compartidas en 30 días y establece como objetivo que el 100 % de los accesos normales sean nominativos.
    - Define responsables, recursos, excepciones permitidas y la evidencia que se conservará.
- **Ejecutar:**
    - Se crean las cuentas, se asignan privilegios, se capacita a los usuarios y se deshabilita la cuenta genérica.
    - Los casos técnicamente incompatibles quedan registrados con medidas compensatorias.
- **Verificar:**
    - Cada mes se compara la lista de personal con las cuentas activas.
    - Se muestrean registros para confirmar que las acciones pueden atribuirse a una persona.
    - Se prueba la restauración de un respaldo y se mide el tiempo real de recuperación.
    - Se revisan incidentes, incumplimientos y observaciones del personal.
- **Actuar:**
    - Si todavía existen cuentas de personal trasladado, se corrige la baja y se modifica el procedimiento que permitió la demora.
    - Si la restauración tarda seis horas cuando la misión tolera solamente dos, se mejora la solución de respaldo o se redefine la capacidad de continuidad.
    - Si una medida dificulta la operación, se busca un control alternativo que reduzca el mismo riesgo sin incentivar que el personal evada el procedimiento.
- **Indicadores útiles para el mando:**
    - Porcentaje de activos con custodio y configuración registrados.
    - Porcentaje de cuentas revisadas y número de cuentas sin responsable.
    - Vulnerabilidades críticas fuera del plazo establecido.
    - Porcentaje de restauraciones exitosas y tiempo real de recuperación.
    - Tiempo desde la detección de un incidente hasta su reporte y contención.
    - Porcentaje de acciones correctivas cerradas dentro del plazo.
- **Alternativas para verificar:**
    - **Revisión manual:** listas de comprobación y muestreo; requiere poco equipamiento, pero consume tiempo y puede omitir eventos.
    - **Supervisión automatizada:** paneles y alertas; aumenta la cobertura, pero exige configuración, mantenimiento y personal capaz de responder.
    - **Ejercicio de mesa:** los participantes explican cómo actuarían ante un incidente; es económico y descubre vacíos de decisión.
    - **Simulación técnica:** se ejecuta una pérdida, caída o restauración controlada; ofrece mayor evidencia, pero debe planificarse para no afectar la misión.
- **Evidencias que produce este componente:**
    - Indicadores y reportes periódicos al mando.
    - Resultados de auditorías, ejercicios y pruebas técnicas.
    - Registro de no conformidades, causas y acciones correctivas.
    - Decisiones de revisión del SGSI y cambios aprobados.

### Recorrido completo del ejemplo

- La **misión** exige que los archivos operativos estén disponibles y no sean divulgados ni alterados.
- El **alcance** incluye al servidor, sus usuarios, los respaldos, la sala y los procedimientos asociados.
- La **evaluación** identifica como riesgo alto el acceso mediante credenciales compartidas.
- Los **controles** combinan cuentas individuales, privilegio mínimo, capacitación, registros y revisión de accesos.
- La **verificación** descubre cuentas de personal trasladado y una recuperación más lenta de lo requerido.
- La **mejora** corrige las bajas, ajusta el procedimiento y mejora la capacidad de recuperación.

> El SGSI no es cada medida por separado. Es el sistema que conecta la misión, los riesgos, las decisiones, los responsables, la evidencia y la mejora.

---

## El SGSI como Marco de Referencia Conceptual

En este curso no se cubre el proceso de certificación ISO 27001 (auditoría externa, registro en un organismo de acreditación, mantenimiento de la certificación). Ese proceso es relevante para organizaciones que necesitan demostrar su conformidad ante clientes o reguladores, pero no es el objetivo de este módulo.

El objetivo es que los alumnos comprendan **qué es y para qué sirve un SGSI**, de modo que cuando trabajen en una organización que ya tiene uno — o que está construyendo uno — puedan entender su rol y contribuir a él.

### Por qué las organizaciones adoptan un SGSI

Las razones más comunes son:

- **Gestión coherente del riesgo:** Sin un marco, cada área gestiona su seguridad de forma diferente o no la gestiona. El SGSI crea un lenguaje y un proceso común.
- **Visibilidad para la dirección:** El SGSI obliga a medir, documentar y reportar el estado de la seguridad. La dirección puede tomar decisiones informadas sobre inversión en seguridad.
- **Base para responder a incidentes:** Una organización con un SGSI sabe qué activos tiene, qué riesgos los amenazan y qué controles los protegen. Cuando ocurre un incidente, esa información está disponible.
- **Confianza de terceros:** Clientes, socios y reguladores pueden verificar que la organización gestiona la seguridad de forma documentada y auditada.

!!! example "Aplicación en entorno castrense"
    Una Jefatura de Comunicaciones que opera un SGSI — aunque sea informal e incompleto — tiene documentado: qué sistemas de comunicaciones existen y cuál es su criticidad para la misión, qué amenazas son más probables (interferencia, acceso no autorizado, falla de equipos), qué controles están en lugar (autenticación, cifrado, redundancia) y quién es responsable de cada sistema. Si un Técnico nuevo llega a la unidad, puede leer esa documentación y entender el estado de seguridad en días, no en semanas.

---

## Aplicación en Contexto Castrense

El SGSI y los estándares ISO 27000 no son exclusivos del mundo corporativo. Las fuerzas armadas de múltiples países — incluyendo miembros de la OTAN — han adoptado marcos basados en ISO 27001 para gestionar la seguridad de sus sistemas de comunicaciones e información.

**Ejemplo 1 — SCIF y clasificación de información:**
Un SCIF (Sensitive Compartmented Information Facility) es una instalación diseñada para procesar información clasificada. Los controles de seguridad que se aplican en un SCIF — acceso físico controlado, equipos sin conectividad inalámbrica, procedimientos de barrido de dispositivos de almacenamiento — son la implementación práctica de conceptos que ISO 27001 formaliza: contexto organizacional (qué información se maneja), controles físicos A.7.* del Anexo A de ISO/IEC 27001:2022, desarrollados en el tema 7 de ISO/IEC 27002:2022, y gestión de activos de información. Un Técnico que entiende el marco ISO puede reconocer por qué cada procedimiento del SCIF existe, no solo seguirlo mecánicamente.

**Ejemplo 2 — Radios tácticas y sistemas C2:**
Una unidad de comunicaciones que opera radios tácticas y sistemas de mando y control (C2) tiene activos de información críticos: las frecuencias de operación, los planes de comunicaciones, los algoritmos de cifrado cargados en los equipos. Un SGSI para esa unidad define qué activos existen, quién puede acceder a ellos, bajo qué condiciones se renueva el material criptográfico, y qué se hace si un equipo es capturado o comprometido. Estos son los cuatro componentes del SGSI aplicados al contexto operativo del Técnico.

---

## Resumen

1. Los estándares ISO 27000 nacieron de la necesidad de un lenguaje común para gestionar la seguridad de la información — comenzando con BS 7799 (1995) y evolucionando hasta ISO/IEC 27001:2022.
2. La familia ISO/IEC 27000 es un conjunto de normas complementarias: **27000:2026** (panorama y conceptos), **27001:2022** (requisitos auditables), **27002:2022** (guía de controles), **27005:2022** (gestión de riesgo), entre otras.
3. Un **SGSI** es el sistema documentado de políticas, controles y procesos que gestiona la seguridad de la información de forma sistemática — no es una herramienta tecnológica, es un sistema de gestión.
4. Los cuatro componentes de un SGSI son: alcance y contexto, evaluación de riesgos, controles de seguridad, y ciclo de mejora continua.
5. En este curso el SGSI se estudia como **marco de referencia conceptual** — para entender su propósito y estructura, no para certificar una organización.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **ISO/IEC 27000:2026 (overview):** https://www.iso.org/standard/27000 — Panorama vigente de conceptos, principios y relaciones de la familia.
- **Historia de ISO 27001 (IT Governance UK):** https://www.itgovernance.co.uk/iso27001 — Resumen accesible de la evolución del estándar.
- **ISO 27001:2022 vs 2013 — Qué cambió:** https://www.iso27001security.com/html/27001.html — Comparación de versiones con foco en los cambios del Anexo A.

---

*Siguiente: [ISO 27000 — Generalidades (Parte 2)](iso27000-generalidades-p2.md)*
