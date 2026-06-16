---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# ISO 27001 — Requisitos Clave (Parte 1)

> **Duración:** 2 horas | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar en lenguaje sencillo el propósito de las cláusulas 4 a 7 de ISO 27001:2022.
- Identificar qué pregunta responde cada cláusula en el contexto de una organización o unidad militar.
- Asociar cada cláusula con acciones concretas que un Técnico de comunicaciones puede observar o ejecutar.

---

## Cláusulas de ISO 27001 cubiertas en esta clase

| Cláusula | Título | En una frase |
|----------|--------|-------------|
| § 4 | Contexto de la organización | Conocer quién eres, qué haces y qué riesgos te rodean |
| § 5 | Liderazgo | La dirección debe comprometerse visiblemente con la seguridad |
| § 6 | Planificación | Evaluar riesgos, establecer objetivos y seleccionar controles |
| § 7 | Apoyo | Recursos, competencia del personal, concienciación y documentación |

ISO 27001:2022 tiene 10 cláusulas de requisitos (cláusulas 4 a 10) más un Annex A con 93 controles. Esta clase cubre las cláusulas 4 a 7 — el bloque que establece las bases del SGSI. La Parte 2 cubrirá las cláusulas 8, 9 y 10.

!!! note ""
    El objetivo de estas clases no es que el alumno memorice el texto de la norma. El objetivo es que, cuando trabaje en una organización que tiene un SGSI, entienda qué significa cada elemento que encuentra y por qué existe. Las referencias § X.Y son la llave para buscar la guía correspondiente cuando la necesiten.

---

## Las Cláusulas 4-7: Las Bases del SGSI

### § 4 Contexto de la organización

Esta cláusula tiene tres subcláusulas principales. Todas responden a la misma pregunta fundamental: ¿quiénes somos y en qué entorno operamos?

### § 4.1 Comprender la organización y su contexto

**En lenguaje sencillo:** Antes de diseñar cualquier control de seguridad, la organización debe entender quién es, qué hace y qué factores externos e internos afectan su seguridad. No hay un SGSI genérico que funcione igual para todos — cada organización tiene su contexto único.

**Por qué existe esta cláusula:** Sin análisis de contexto, los controles implementados pueden ser incorrectos — una pequeña jefatura de comunicaciones no necesita los mismos controles que un Estado Mayor con cientos de sistemas. Implementar demasiado es costoso e innecesario; implementar muy poco es peligroso.

**En una unidad militar:**
La Jefatura de Comunicaciones de un batallón define su contexto identificando: misión táctica (soporte de comunicaciones en operaciones), activos principales (radios tácticas, sistemas C2, equipos de puesto de mando), partes interesadas (Comandante de batallón, personal de comunicaciones, unidades subordinadas), y amenazas prioritarias (interceptación de señales, interferencia electrónica, acceso físico no autorizado a equipos).

### § 4.2 Comprender las necesidades y expectativas de las partes interesadas

**En lenguaje sencillo:** Las "partes interesadas" son todas las personas y organizaciones que se ven afectadas por la seguridad de la información de la organización — o que tienen expectativas sobre cómo se gestiona. Incluye no solo a los usuarios internos, sino también a clientes, reguladores, socios y proveedores.

**Por qué existe esta cláusula:** Los controles de seguridad deben satisfacer no solo las necesidades internas, sino también los requisitos que imponen terceros. Un contrato puede exigir ciertos estándares de protección de datos; una regulación puede imponer controles específicos.

**En una unidad militar:**
Las partes interesadas de una unidad de comunicaciones incluyen: el Comando Superior (que impone requisitos de clasificación y manejo de información), unidades aliadas (que exigen compatibilidad de protocolos cifrados), el área de contrainteligencia (que puede imponer restricciones sobre qué sistemas conectar a qué redes), y los propios Técnicos (que necesitan procedimientos claros y realizables).

### § 4.3 Determinar el alcance del SGSI

**En lenguaje sencillo:** El alcance define exactamente qué sistemas, qué información y qué unidades organizativas cubre el SGSI. Lo que está dentro del alcance es gestionado por el SGSI; lo que está fuera puede no serlo.

**Por qué existe esta cláusula:** Sin un alcance definido, no hay forma de saber si el SGSI cubre lo que necesita cubrir — ni de evaluar si es completo. El alcance también comunica a las partes interesadas qué está bajo control y qué no.

**En una unidad militar:**
Una unidad de comunicaciones puede definir su alcance como: "todos los sistemas de comunicaciones tácticas y estratégicas operados por la Jefatura de Comunicaciones del Batallón, incluyendo radios tácticas, terminales de enlace de datos, y los equipos del puesto de mando avanzado". Los sistemas administrativos (computadoras de oficina, impresoras) pueden estar fuera del alcance si no procesan información clasificada.

---

### § 5 Liderazgo

Esta cláusula establece que la seguridad de la información no puede ser responsabilidad exclusiva del área técnica. La dirección — el mando — debe comprometerse activamente.

### § 5.1 Liderazgo y compromiso

**En lenguaje sencillo:** El mando debe demostrar que la seguridad de la información es una prioridad real, no una prioridad declarada. Esto significa asignar recursos, participar en las revisiones del SGSI y tomar decisiones sobre riesgo.

**Por qué existe esta cláusula:** Los Técnicos pueden configurar sistemas perfectamente seguros, pero si el mando no apoya las políticas de seguridad — o las viola abiertamente — el SGSI falla. La seguridad necesita autoridad para ser efectiva.

**En una unidad militar:**
El Comandante demuestra compromiso con la seguridad cuando: aprueba formalmente la política de seguridad de comunicaciones, asigna tiempo de instrucción para la formación del personal en procedimientos de seguridad, y no omite los procedimientos de verificación de equipos por presión de tiempo operacional.

### § 5.2 Política de seguridad de la información

**En lenguaje sencillo:** La organización debe tener una política de seguridad de la información — un documento oficial, aprobado por la dirección, que establece el compromiso de la organización con la seguridad y los principios que guían todas las decisiones en esta materia.

**Por qué existe esta cláusula:** Sin una política formal, cada persona interpreta la seguridad según su criterio. La política es el documento de referencia que alinea a toda la organización.

**En una unidad militar:**
La política puede ser tan simple como un párrafo en las órdenes de operaciones permanentes: "Todo el personal de comunicaciones es responsable de mantener la seguridad de los equipos y la información que procesan. Los incidentes de seguridad deben reportarse al Oficial de Seguridad en no más de 2 horas." Lo que importa es que exista, esté aprobada por el mando y sea conocida por todo el personal.

### § 5.3 Roles, responsabilidades y autoridades

**En lenguaje sencillo:** Alguien debe ser responsable del SGSI. Esta cláusula requiere que la organización asigne responsabilidades específicas de seguridad a personas concretas — no a "el área de TI" en abstracto.

**Por qué existe esta cláusula:** La responsabilidad difusa es responsabilidad inexistente. Si todos son responsables de la seguridad, nadie lo es en la práctica.

**En una unidad militar:**
La asignación puede ser: "El Suboficial de Comunicaciones de Turno es responsable de verificar el estado de seguridad de los equipos al inicio de cada turno. El Oficial de Seguridad es responsable de la política y de las revisiones mensuales."

---

### § 6 Planificación

Si la cláusula 4 respondió "¿quiénes somos?", la cláusula 6 responde "¿qué vamos a hacer con los riesgos que identificamos?".

### § 6.1 Acciones para abordar riesgos y oportunidades

**En lenguaje sencillo:** La organización debe evaluar sus riesgos de seguridad de forma sistemática — identificar qué activos tiene, qué amenazas los afectan, qué tan vulnerables son, y qué impacto tendría un incidente — y luego decidir qué hacer con cada riesgo.

**Por qué existe esta cláusula:** Los controles de seguridad son una respuesta a los riesgos identificados, no decisiones arbitrarias. Sin evaluación de riesgos, es imposible saber si los controles implementados son los correctos.

**En una unidad militar:**
Un riesgo identificado podría ser: "Las radios tácticas del pelotón de reconocimiento se transportan en vehículos que pueden ser capturados. Si una radio es capturada con el material criptográfico cargado, el adversario tiene acceso a las comunicaciones cifradas." La respuesta al riesgo es: procedimiento de emergencia para borrar el material criptográfico si el equipo va a ser capturado, más renovación inmediata del material en el resto de la red de comunicaciones.

### § 6.2 Objetivos de seguridad de la información

**En lenguaje sencillo:** El SGSI debe tener objetivos medibles — no solo "mejorar la seguridad", sino "reducir el tiempo de respuesta a incidentes de seguridad a menos de 4 horas" o "garantizar que el 100% del personal completa la instrucción de seguridad anual".

**Por qué existe esta cláusula:** Lo que no se mide no se gestiona. Los objetivos medibles permiten saber si el SGSI está funcionando o no.

**En una unidad militar:**
Objetivos concretos para una jefatura de comunicaciones: "Todos los equipos con material criptográfico deben verificarse cada 24 horas durante operaciones", "Ningún equipo debe conectarse a la red de unidad sin pasar la lista de verificación de seguridad estándar."

---

### § 7 Apoyo

Esta cláusula responde: "¿con qué recursos cuenta el SGSI para funcionar?"

### § 7.1 Recursos

**En lenguaje sencillo:** La organización debe proporcionar los recursos necesarios para que el SGSI funcione — tiempo, personal, herramientas, presupuesto. Un SGSI sin recursos es un documento sin implementación.

**Por qué existe esta cláusula:** Es frecuente que las organizaciones aprueben un SGSI en papel pero no asignen recursos para operarlo. Esta cláusula hace que la asignación de recursos sea un requisito explícito.

### § 7.2 Competencia

**En lenguaje sencillo:** Las personas que tienen responsabilidades de seguridad deben estar calificadas para ejercerlas. La organización debe verificar que su personal tiene la formación y experiencia necesarias para su rol en el SGSI.

**Por qué existe esta cláusula:** Un SGSI es tan fuerte como las personas que lo operan. Un Técnico sin la formación adecuada puede implementar controles incorrectamente, crear vulnerabilidades sin saberlo, o no reconocer señales de un incidente.

**En una unidad militar:**
Un Técnico recién asignado a una jefatura de comunicaciones debe recibir la instrucción necesaria antes de operar equipos con material criptográfico: qué verificaciones realizar, qué hacer en caso de anomalía, cómo reportar un incidente. Esta instrucción es un requisito de § 7.2, no una cortesía.

### § 7.3 Concienciación

**En lenguaje sencillo:** Todo el personal que puede afectar la seguridad de la información — no solo los Técnicos, sino todos los usuarios de los sistemas — debe ser consciente de la política de seguridad, de su contribución al SGSI, y de las consecuencias de no cumplir con los requisitos.

**Por qué existe esta cláusula:** La mayoría de los incidentes de seguridad tienen un componente humano — alguien que hizo clic en el archivo equivocado, que usó una contraseña débil, que dejó un equipo desbloqueado. La concienciación no elimina estos errores, pero los reduce significativamente.

### § 7.4 Comunicación

**En lenguaje sencillo:** La organización debe definir qué información de seguridad se comunica, a quién, cuándo y cómo.

### § 7.5 Información documentada

**En lenguaje sencillo:** El SGSI debe estar documentado — las políticas, los procedimientos, los registros de auditorías, los resultados de la evaluación de riesgos. La documentación es la evidencia de que el SGSI existe y funciona.

**Por qué existe esta cláusula:** Sin documentación, no hay forma de verificar que el SGSI existe, ni de transmitirlo a personal nuevo, ni de mejorar sobre lo que se hizo antes.

**En una unidad militar:**
La documentación del SGSI de una jefatura de comunicaciones puede ser tan simple como: un registro de los equipos con material criptográfico y su estado, un checklist firmado de verificaciones diarias, y un registro de incidentes de seguridad con fecha y resolución.

---

## Aplicación en Contexto Castrense

Las cláusulas 4-7 de ISO 27001 no son burocracia abstracta — son las preguntas que cualquier organización bien gestionada debería poder responder, y que los Técnicos de comunicaciones militares ya responden en la práctica, aunque no siempre con esta terminología.

**Ejemplo 1 — Contexto y alcance en un puesto de mando avanzado:**
Antes de desplegar un puesto de mando avanzado (§ 4.3 alcance), el Oficial de Comunicaciones define qué sistemas se van a operar — radios de largo alcance, terminal de enlace de datos, computadores de mando. Identifica las partes interesadas (§ 4.2): el Comandante de la fuerza de tarea necesita comunicaciones confiables; el Estado Mayor necesita que los datos de situación lleguen sin corrupción; el área de contrainteligencia necesita que ninguna transmisión sea interceptable. Cada uno de esos requisitos se convierte en un control de seguridad concreto.

**Ejemplo 2 — Liderazgo y competencia en la operación de sistemas C2:**
El Comandante que firma el SOP de comunicaciones seguras está ejerciendo § 5.1 (liderazgo y compromiso). El Suboficial que verifica diariamente el estado de los equipos SCIF antes de permitir el ingreso de personal está ejerciendo § 5.3 (responsabilidades). La instrucción obligatoria de operación de sistemas C2 antes de asignar a un Técnico a ese puesto es § 7.2 (competencia). Estas acciones existen en muchas unidades militares — ISO 27001 les pone nombre y las hace verificables.

---

## Resumen

1. Las cláusulas 4-7 de ISO 27001 establecen las bases del SGSI: **contexto** (§4), **liderazgo** (§5), **planificación** (§6) y **apoyo** (§7).
2. El formato D-02 resume cada cláusula: qué significa, por qué existe, y cómo aplica en un contexto militar concreto.
3. La § 4 responde "¿quiénes somos y en qué entorno operamos?"; § 5 dice que el mando debe comprometerse activamente; § 6 convierte los riesgos en decisiones documentadas; § 7 asegura que el SGSI tiene los recursos y la documentación para funcionar.
4. La documentación (§ 7.5) no es un fin en sí mismo — es la evidencia de que el SGSI existe y funciona, y el mecanismo para transmitirlo y mejorarlo.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Resumen de ISO 27001:2022 (IT Governance):** https://www.itgovernance.co.uk/iso27001-2022-changes — Cambios entre la versión 2013 y 2022, con énfasis en las cláusulas 4-10.
- **ISO 27001 Clause by Clause (Advisera):** https://advisera.com/27001academy/knowledgebase/iso-27001-clause-by-clause-explanation/ — Explicación en inglés de cada cláusula en lenguaje accesible.
- **Plantilla de alcance SGSI:** https://www.iso27001security.com/html/27001.html — Referencia para entender cómo se documenta el alcance en organizaciones reales.

---

*Siguiente: [ISO 27001 — Requisitos Clave (Parte 2)](iso27001-requisitos-p2.md)*
