---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# ISO 27002 — Controles de Seguridad (Parte 1)

> **Duración:** 1 hora | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar la relación entre ISO 27001 (requisitos) e ISO 27002 (guía de controles).
- Identificar las 4 categorías de controles de ISO 27002:2022 y describir qué tipo de control vive en cada una.
- Reconocer el Annex A de ISO 27001 como el puente que conecta ambas normas.

---

## ISO 27001 e ISO 27002: el Par que Trabaja Junto

ISO 27001 e ISO 27002 son normas complementarias — no son lo mismo ni pueden reemplazarse mutuamente.

**ISO 27001** es el estándar de requisitos: dice **qué debe tener** un SGSI para ser conforme. Entre sus requisitos está que la organización seleccione controles de seguridad apropiados para sus riesgos. Pero ISO 27001 no explica cómo implementar esos controles.

**ISO 27002** es la guía de implementación: dice **cómo hacer** los controles. Para cada uno de los 93 controles del catálogo, ISO 27002:2022 proporciona: qué es el control, cuál es su propósito, orientación de implementación, y consideraciones adicionales.

El **Annex A de ISO 27001:2022** es el puente: es una tabla de referencia que lista los 93 controles de ISO 27002, organizada en los mismos 4 temas. Cuando una organización hace su evaluación de riesgos y selecciona controles, los selecciona del Annex A. Si luego necesita orientación sobre cómo implementar un control específico, consulta ISO 27002.

!!! note "Definición"
    El **Annex A** de ISO 27001:2022 es un anexo normativo (obligatorio) que lista los 93 controles de seguridad organizados en 4 temas. La organización debe usar este catálogo como referencia al seleccionar controles y debe documentar en su **Statement of Applicability (SoA)** qué controles aplica y por qué.

---

## Las 4 Categorías de Controles de ISO 27002:2022

ISO 27002:2022 organiza sus 93 controles en 4 temas o categorías. Cada categoría agrupa controles según el tipo de recurso o actor que protegen.

| Categoría | Número de controles | Qué tipo de control contiene |
|-----------|--------------------|-----------------------------|
| **Tema 5 — Organizacional** | 37 controles | Políticas, procesos, roles, responsabilidades — controles que afectan cómo la organización gestiona la seguridad |
| **Tema 6 — Personas** | 8 controles | Controles relacionados con el personal: selección, formación, concienciación, obligaciones de confidencialidad |
| **Tema 7 — Físico** | 14 controles | Seguridad de instalaciones, áreas seguras, equipo físico, entorno físico |
| **Tema 8 — Tecnológico** | 34 controles | Controles técnicos: autenticación, cifrado, redes, endpoints, logs, gestión de vulnerabilidades |

### ¿Qué significa que un control sea "organizacional"?

Los controles organizacionales (Tema 5) son los más numerosos porque la seguridad comienza con las decisiones de gestión, no con la tecnología. Una política de contraseñas, un proceso de gestión de incidentes, un procedimiento de clasificación de información — todos estos son controles organizacionales. Existen como documentos, decisiones y procesos, no como software o hardware.

### ¿Qué son los controles de personas (Tema 6)?

Los controles de personas afectan a los individuos que trabajan en la organización. Incluyen la verificación de antecedentes antes de contratar (6.1), la formación obligatoria en seguridad (6.3), el proceso de reporte de incidentes por parte del personal (6.8), y los acuerdos de confidencialidad. Son pocos (8) pero de alto impacto: una persona mal gestionada puede comprometer toda la infraestructura técnica.

### ¿Qué son los controles físicos (Tema 7)?

Los controles físicos protegen los activos de información en el mundo físico: perímetros de seguridad (7.1), controles de acceso físico a instalaciones (7.2), monitoreo físico (7.4), protección contra desastres naturales. En muchos entornos militares, los controles físicos son los más maduros porque la cultura de seguridad física tiene décadas de desarrollo.

### ¿Qué son los controles tecnológicos (Tema 8)?

Los controles tecnológicos son los más técnicos: autenticación segura (8.5), protección contra malware (8.7), gestión de configuraciones (8.9), registros y logs de seguridad (8.15), seguridad de redes (8.20). Son los controles que los Técnicos configuran, mantienen y verifican directamente.

!!! tip "Los 4 temas trabajan juntos"
    Un control tecnológico (8.5 autenticación) no funciona sin el control organizacional que define la política de contraseñas (5.1), ni sin la formación del personal sobre por qué las contraseñas fuertes importan (6.3). La seguridad no se logra con tecnología sola — se logra con los 4 temas aplicados en conjunto.

---

## Aplicación en Contexto Castrense

La estructura de 4 temas de ISO 27002:2022 refleja cómo la seguridad funciona en cualquier organización — incluyendo las militares.

**Ejemplo — Los 4 temas en un puesto de mando avanzado:**
Un puesto de mando avanzado tiene los 4 tipos de controles presentes simultáneamente:
- **Organizacional:** El SOP de comunicaciones seguras que define qué canales usar para qué tipo de información (equivalente al Tema 5).
- **Personas:** La instrucción obligatoria de todos los operadores en procedimientos de comunicaciones seguras antes de asumir turno (equivalente al Tema 6 control 6.3).
- **Físico:** El perímetro físico del puesto de mando, los controles de acceso a la sala de comunicaciones, la protección de los equipos de radio durante el transporte (equivalente al Tema 7).
- **Tecnológico:** El cifrado activo en las radios tácticas, la autenticación para acceder a los sistemas C2, los logs de acceso a los terminales (equivalente al Tema 8).

Cuando los 4 temas están bien implementados y coordinados, el resultado es defensa en profundidad — que se estudiará en la Unidad 2 de esta asignatura.

---

## Resumen

1. **ISO 27001** dice qué debe tener un SGSI (requisitos); **ISO 27002** dice cómo implementar cada control (guía). El **Annex A** de ISO 27001 es el catálogo de los 93 controles que conecta ambas normas.
2. Los 93 controles de ISO 27002:2022 se organizan en 4 temas: **Organizacional** (37), **Personas** (8), **Físico** (14) y **Tecnológico** (34).
3. Los 4 temas no son independientes — trabajan juntos. Un control tecnológico sin el apoyo organizacional y de personas que lo sustenta es frágil.
4. La Parte 2 de este tema (CONT-18) cubrirá controles clave de cada categoría con ejemplos militares, y terminará con el concepto de Statement of Applicability (SoA).

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **ISO 27002:2022 overview (ISO.org):** https://www.iso.org/standard/75652.html — Descripción oficial de la norma de controles.
- **Los 93 controles de ISO 27002:2022 — tabla completa (Advisera):** https://advisera.com/27001academy/iso-27002/ — Lista de todos los controles con breve descripción en inglés.
- **Annex A vs ISO 27002 — diferencias (IT Governance):** https://www.itgovernance.co.uk/iso-27002-controls — Explicación de cómo Annex A e ISO 27002 se relacionan.

---

*Siguiente: [ISO 27002 — Controles de Seguridad (Parte 2)](iso27002-controles-p2.md)*
