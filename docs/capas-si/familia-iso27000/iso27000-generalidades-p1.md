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

### La Evolución hacia ISO 27001

La transición de BS 7799 a la familia ISO 27000 ocurrió en etapas:

| Año | Hito |
|-----|------|
| 1995 | BS 7799 Parte 1 — Código de prácticas (buenas prácticas, no requisitos) |
| 1998 | BS 7799 Parte 2 — Especificación del SGSI (primeros requisitos auditables) |
| 2000 | ISO/IEC 17799 — BS 7799 Parte 1 adopta el número ISO |
| 2005 | ISO/IEC 27001:2005 — Primera versión del estándar de gestión con ese número |
| 2013 | ISO/IEC 27001:2013 — Revisión mayor, Annex A con 114 controles en 14 dominios |
| 2022 | ISO/IEC 27001:2022 — Versión vigente, Annex A con 93 controles en 4 temas |

La versión vigente es **ISO/IEC 27001:2022**. Cualquier referencia en este curso usa la numeración de 2022.

---

## La Familia ISO/IEC 27000

La "familia ISO 27000" no es una norma única — es un conjunto de estándares relacionados, cada uno con un propósito específico. Funcionan como una biblioteca: ISO 27001 es el libro central que establece los requisitos, y los demás son referencias que se usan según la necesidad.

!!! note "Definición"
    La **familia ISO/IEC 27000** es el conjunto de estándares internacionales publicados conjuntamente por ISO e IEC que cubren la gestión de la seguridad de la información. Los estándares de esta familia comparten vocabulario, conceptos y estructura, y están diseñados para usarse en conjunto.

| Norma | Propósito | Qué entrega |
|-------|-----------|-------------|
| **ISO/IEC 27000** | Vocabulario y conceptos generales | Definiciones comunes para toda la familia — el glosario oficial |
| **ISO/IEC 27001:2022** | Requisitos para un SGSI (auditable) | La lista de "qué debe hacer" — base para la certificación si se desea |
| **ISO/IEC 27002:2022** | Guía de implementación de controles | El "cómo hacerlo" — 93 controles con orientación práctica |
| **ISO/IEC 27005:2022** | Gestión del riesgo de seguridad de la información | El proceso completo para identificar, analizar y tratar riesgos |
| **ISO/IEC 27017** | Controles para servicios en la nube (referencia) | Extensión de 27002 para entornos cloud |
| **ISO/IEC 27035** | Gestión de incidentes de seguridad (referencia) | Proceso de respuesta a incidentes alineado con la familia ISO |

### Relación entre las normas

La forma más sencilla de entender la relación es esta:

- **ISO 27001** es el jefe: dice **qué** debe existir en una organización que gestiona la seguridad de su información.
- **ISO 27002** es el técnico: dice **cómo** implementar cada control que ISO 27001 requiere.
- **ISO 27005** es el analista de riesgo: dice **cómo** identificar y tratar los riesgos que ISO 27001 pide gestionar.
- **ISO 27000** es el intérprete: dice **qué significan** los términos que las otras normas usan.

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
Un SCIF (Sensitive Compartmented Information Facility) es una instalación diseñada para procesar información clasificada. Los controles de seguridad que se aplican en un SCIF — acceso físico controlado, equipos sin conectividad inalámbrica, procedimientos de barrido de dispositivos de almacenamiento — son la implementación práctica de conceptos que ISO 27001 formaliza: contexto organizacional (qué información se maneja), controles de acceso físico (cláusula 7 del Annex A de ISO 27002:2022), y gestión de activos de información. Un Técnico que entiende el marco ISO puede reconocer por qué cada procedimiento del SCIF existe, no solo seguirlo mecánicamente.

**Ejemplo 2 — Radios tácticas y sistemas C2:**
Una unidad de comunicaciones que opera radios tácticas y sistemas de mando y control (C2) tiene activos de información críticos: las frecuencias de operación, los planes de comunicaciones, los algoritmos de cifrado cargados en los equipos. Un SGSI para esa unidad define qué activos existen, quién puede acceder a ellos, bajo qué condiciones se renueva el material criptográfico, y qué se hace si un equipo es capturado o comprometido. Estos son los cuatro componentes del SGSI aplicados al contexto operativo del Técnico.

---

## Resumen

1. Los estándares ISO 27000 nacieron de la necesidad de un lenguaje común para gestionar la seguridad de la información — comenzando con BS 7799 (1995) y evolucionando hasta ISO/IEC 27001:2022.
2. La familia ISO/IEC 27000 es un conjunto de normas complementarias: **27000** (vocabulario), **27001** (requisitos auditables), **27002** (guía de controles), **27005** (gestión de riesgo), entre otras.
3. Un **SGSI** es el sistema documentado de políticas, controles y procesos que gestiona la seguridad de la información de forma sistemática — no es una herramienta tecnológica, es un sistema de gestión.
4. Los cuatro componentes de un SGSI son: alcance y contexto, evaluación de riesgos, controles de seguridad, y ciclo de mejora continua.
5. En este curso el SGSI se estudia como **marco de referencia conceptual** — para entender su propósito y estructura, no para certificar una organización.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **ISO/IEC 27000:2018 (overview):** https://www.iso.org/standard/73906.html — Disponible en versión gratuita en el sitio de ISO. Define todos los términos de la familia.
- **Historia de ISO 27001 (IT Governance UK):** https://www.itgovernance.co.uk/iso27001 — Resumen accesible de la evolución del estándar.
- **ISO 27001:2022 vs 2013 — Qué cambió:** https://www.iso27001security.com/html/27001.html — Comparación de versiones con foco en los cambios del Annex A.

---

*Siguiente: [ISO 27000 — Generalidades (Parte 2)](iso27000-generalidades-p2.md)*
