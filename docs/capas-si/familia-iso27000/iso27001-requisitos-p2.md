---
# Horas asignadas: 3 hrs
# Tipo: Teoría
---

# ISO 27001 — Requisitos Clave (Parte 2)

> **Duración:** 3 horas | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar en lenguaje sencillo el propósito de las cláusulas 8, 9 y 10 de ISO 27001:2022.
- Describir qué es una auditoría interna del SGSI y qué hace una revisión por la dirección.
- Distinguir entre no-conformidad y mejora, y explicar cómo el SGSI las gestiona.
- Reconocer que la cláusula 9 es sobre ciclo de verificación, no sobre el proceso de certificación.

---

> **Recapitulación:** En la clase anterior (Parte 1) cubrimos las cláusulas 4 a 7 de ISO 27001 — las bases del SGSI: contexto organizacional, compromiso del liderazgo, planificación de riesgos, y los recursos necesarios para operar el sistema. Esta clase completa el ciclo con las cláusulas 8, 9 y 10: la operación del SGSI, su verificación, y su mejora continua.

---

## Cláusulas de ISO 27001 cubiertas en esta clase

| Cláusula | Título | En una frase |
|----------|--------|-------------|
| § 8 | Operación | Implementar los controles planificados — pasar del plan a la acción |
| § 9 | Evaluación del desempeño | Medir si el SGSI funciona: auditorías internas y revisión por la dirección |
| § 10 | Mejora | Corregir lo que no funciona y mejorar continuamente |

Estas tres cláusulas corresponden a las fases DO, CHECK y ACT del ciclo PDCA visto en la Parte 1 de Generalidades. La cláusula 8 es el DO (ejecutar), la 9 es el CHECK (verificar), y la 10 es el ACT (actuar y mejorar).

---

## § 8 Operación

La cláusula 8 es donde el SGSI pasa del papel a la práctica. Todo lo que se planificó en la cláusula 6 — los controles seleccionados, los objetivos definidos, los riesgos identificados — ahora debe implementarse.

### § 8.1 Planificación y control operacional

**En lenguaje sencillo:** La organización debe implementar los controles planificados y mantener registros de que lo está haciendo. No es suficiente tener el plan — la operación debe estar controlada y documentada.

**Por qué existe esta cláusula:** La brecha más común en los SGSI es entre lo que el plan dice y lo que realmente ocurre. La cláusula 8.1 cierra esa brecha al requerir evidencia de operación real.

**En una unidad militar:**
La Jefatura de Comunicaciones planificó (§ 6) que todos los equipos con material criptográfico se verificarán diariamente. La cláusula 8 requiere que esa verificación ocurra realmente, que quede registrada (quién verificó, qué fecha, resultado), y que si un equipo no pasa la verificación, haya un procedimiento para manejarlo. El registro firmado del Suboficial de turno es la evidencia de operación.

### § 8.2 Evaluación de riesgos de seguridad de la información

**En lenguaje sencillo:** La evaluación de riesgos no se hace solo una vez al crear el SGSI. Debe repetirse a intervalos planificados o cuando ocurren cambios significativos en el contexto o en los activos.

**Por qué existe esta cláusula:** Los riesgos cambian. Se incorporan nuevos sistemas, surgen nuevas amenazas, el contexto operacional varía. Un SGSI que solo evalúa riesgos al inicio y no los revisa se vuelve obsoleto.

**En una unidad militar:**
Cuando se incorpora un nuevo sistema C2 a la red de la unidad, se debe realizar una evaluación de riesgos para ese sistema: ¿qué amenazas específicas lo afectan? ¿qué vulnerabilidades tiene? ¿qué controles se necesitan antes de conectarlo a la red operacional? Esta evaluación no es opcional — es un requisito de § 8.2.

### § 8.3 Tratamiento de riesgos de seguridad de la información

**En lenguaje sencillo:** Para cada riesgo identificado, la organización debe implementar el plan de tratamiento que decidió en § 6.1 y conservar los registros de esa implementación.

**Por qué existe esta cláusula:** Asegura que la decisión tomada en la fase de planificación (reducir el riesgo X con el control Y) se traduzca en acción real (el control Y está implementado y funcionando).

!!! example "Aplicación en entorno castrense"
    El plan de tratamiento de riesgos de la Jefatura de Comunicaciones dice: "Riesgo de intercepción de comunicaciones vía radio → tratamiento: cifrado de todas las transmisiones con material criptográfico vigente". La operación de ese tratamiento significa: cargar el material criptográfico en todos los equipos antes de cada operación, verificar que está activo, y registrar esa verificación. La § 8.3 requiere esa documentación como evidencia de que el tratamiento se implementó.

---

## § 9 Evaluación del Desempeño

La cláusula 9 es la fase CHECK del ciclo PDCA. Su pregunta central es: ¿el SGSI está funcionando como se diseñó?

!!! note ""
    Esta cláusula trata sobre verificación interna — auditorías que la propia organización realiza para medir si su SGSI funciona. No se refiere al proceso de certificación ISO 27001 (auditoría externa por un organismo independiente). En este curso no cubrimos el proceso de certificación, que es voluntario y aplica a organizaciones que quieren demostrar conformidad ante terceros.

### § 9.1 Seguimiento, medición, análisis y evaluación

**En lenguaje sencillo:** La organización debe definir qué medir, cómo medirlo, cuándo y quién lo hace, y qué hace con los resultados. Sin métricas, no hay forma de saber si la seguridad está mejorando o empeorando.

**Por qué existe esta cláusula:** "Mejorar la seguridad" sin métricas es una declaración de intención, no una gestión. Las métricas convierten la seguridad en algo gestionable.

**En una unidad militar:**
Métricas concretas para una jefatura de comunicaciones:
- Porcentaje de equipos con verificación de seguridad completada esta semana (objetivo: 100%)
- Número de incidentes de seguridad reportados este mes (objetivo: reducción trimestral)
- Número de Técnicos con instrucción de seguridad actualizada (objetivo: 100% antes de cada ciclo operacional)

### § 9.2 Auditoría interna

**En lenguaje sencillo:** A intervalos planificados, la organización se audita a sí misma: verifica si los controles del SGSI están implementados, si funcionan correctamente, y si el SGSI cumple con los requisitos de ISO 27001 y con sus propios procedimientos.

**Por qué existe esta cláusula:** La auditoría interna es el mecanismo de autocontrol. Detecta problemas antes de que se conviertan en incidentes, y antes de que una auditoría externa (si la hay) los encuentre.

**Cómo funciona una auditoría interna:**
1. **Planificación:** Definir qué se va a auditar, cuándo, con qué criterios y quién lo hace.
2. **Ejecución:** El auditor interno revisa documentación, entrevista al personal, observa procesos y verifica controles en operación.
3. **Hallazgos:** Se documentan las no-conformidades (algo no cumple) y las observaciones (algo podría mejorar).
4. **Informe:** Se comunica a la dirección con las no-conformidades encontradas.
5. **Seguimiento:** Se verifica que las no-conformidades fueron corregidas.

!!! warning "El auditor interno no debe auditar su propio trabajo"
    Un principio fundamental de la auditoría interna es la independencia: la persona que diseñó un control no puede auditarlo. En unidades pequeñas donde el mismo Técnico diseña y opera los controles, esto puede ser difícil — la solución es rotar la responsabilidad de auditoría o pedir apoyo a personal de otra unidad.

### § 9.3 Revisión por la dirección

**En lenguaje sencillo:** A intervalos planificados, la dirección — el mando — revisa el SGSI para asegurarse de que sigue siendo adecuado, suficiente y efectivo para el contexto actual de la organización.

**Por qué existe esta cláusula:** Los sistemas de gestión se desconectan de la realidad si la dirección no los revisa. La revisión por la dirección asegura que el mando está informado del estado real del SGSI y toma decisiones activas sobre él.

**Qué revisa la dirección:**
- Resultados de auditorías internas y métricas de desempeño
- Estado de no-conformidades y acciones correctivas en curso
- Cambios en el contexto que podrían afectar el SGSI
- Retroalimentación de partes interesadas
- Oportunidades de mejora

**En una unidad militar:**
Una revisión por la dirección en una jefatura de comunicaciones podría ser una reunión mensual de 30 minutos entre el Oficial de Comunicaciones y el Suboficial de Seguridad: ¿qué incidentes ocurrieron? ¿qué controles fallaron? ¿hay cambios en la misión o en los equipos que afectan el plan de seguridad? ¿qué mejoras se implementaron? Esta reunión, documentada, cumple § 9.3.

---

## § 10 Mejora

La cláusula 10 es la fase ACT del ciclo PDCA. Es donde el SGSI aprende de sus errores y se hace más fuerte.

### § 10.1 No-conformidad y acción correctiva

**En lenguaje sencillo:** Cuando se detecta que algo no cumple con los requisitos del SGSI — sea por una auditoría interna, un incidente, o una revisión por la dirección — la organización debe: reaccionar ante el problema inmediato, investigar su causa raíz, implementar una corrección que evite que vuelva a ocurrir, y verificar que la corrección fue efectiva.

**Por qué existe esta cláusula:** Corregir el síntoma sin atacar la causa raíz no mejora el SGSI. Si un incidente ocurre porque un procedimiento no es claro, reparar el equipo afectado (síntoma) sin actualizar el procedimiento (causa raíz) garantiza que el mismo incidente ocurra de nuevo.

**El proceso de acción correctiva:**

| Paso | Acción | Ejemplo |
|------|--------|---------|
| 1 | Detectar la no-conformidad | Auditoría detecta que tres equipos no tienen el checklist de verificación diaria al día |
| 2 | Contener el impacto inmediato | Los tres equipos se verifican inmediatamente antes de continuar operando |
| 3 | Investigar la causa raíz | El Suboficial responsable no sabía que el checklist era obligatorio — no recibió instrucción |
| 4 | Implementar corrección | Se actualiza el proceso de inducción para incluir instrucción sobre el checklist desde el primer día |
| 5 | Verificar la efectividad | En la próxima auditoría se verifica que todo el personal nuevo conoce y cumple el requisito |

**En una unidad militar:**
Una no-conformidad típica en comunicaciones: se descubre que un radio con material criptográfico fue transportado en un vehículo no autorizado sin seguir el procedimiento de custodia. La acción correctiva correcta no es solo amonestar al responsable — es entender por qué ocurrió (¿el procedimiento no estaba claro? ¿no había vehículo autorizado disponible?) y corregir la causa raíz.

### § 10.2 Mejora continua

**En lenguaje sencillo:** La organización debe mejorar continuamente la idoneidad, adecuación y eficacia de su SGSI — no solo corregir errores, sino también buscar activamente oportunidades para hacerlo mejor.

**Por qué existe esta cláusula:** Un SGSI que solo reacciona a problemas está siempre detrás de las amenazas. La mejora continua es la capacidad de anticiparse y fortalecerse proactivamente.

**En la práctica:**
La mejora continua incluye: incorporar lecciones aprendidas de incidentes propios y de otras organizaciones, actualizar controles cuando surgen nuevas amenazas, simplificar procedimientos que funcionan pero generan fricción innecesaria, y elevar el nivel de concienciación del personal.

!!! tip "El SGSI como organismo vivo"
    Un buen SGSI después de varios ciclos PDCA se parece poco al que se diseñó inicialmente — porque aprendió de la realidad. Eso es correcto. Un SGSI que no cambia con el tiempo no está siendo revisado; un SGSI que cambia constantemente en respuesta a sus propias mediciones está funcionando como debe.

---

## Aplicación en Contexto Castrense

Las cláusulas 8-10 representan el motor de operación del SGSI en el día a día. Para un Técnico de comunicaciones, estas son las cláusulas que más afectan su trabajo concreto.

**Ejemplo 1 — Operación y documentación en sistemas SCIF:**
Un SCIF que opera bajo ISO 27001 tiene controles documentados para cada tipo de acceso (§ 8.1): quién puede entrar, con qué autorización, qué registro se lleva. Cada vez que se detecta una anomalía — alguien intenta ingresar sin autorización, se encuentra un dispositivo de grabación no declarado, se produce un corte de energía inesperado — se activa el procedimiento de gestión de incidentes. La evidencia de que los controles operan correctamente es el registro diario de acceso, el log de intentos fallidos, y los informes de anomalías. Esa documentación es la operación real del SGSI bajo § 8.

**Ejemplo 2 — Evaluación y mejora en la red de comunicaciones del batallón:**
Al final de un ejercicio de campo, el Oficial de Comunicaciones revisa los incidentes de seguridad que ocurrieron durante el ejercicio (§ 9.1 — evaluación del desempeño). Identifica que en dos ocasiones las comunicaciones no cifradas se usaron por error cuando el sistema cifrado estaba disponible. Esto es una no-conformidad (§ 10.1). La causa raíz: en situaciones de alta presión, los Técnicos recurren al canal no cifrado porque es más rápido. La corrección: modificar la configuración de los equipos para que el canal cifrado sea el predeterminado, reduciendo la fricción operacional. Este ciclo detect → analizar → corregir → verificar es el § 10 en acción.

---

## Resumen

1. La **cláusula 8** (Operación) corresponde al DO del ciclo PDCA: implementar los controles planificados, documentar la operación y mantener evidencia de que los controles funcionan.
2. La **cláusula 9** (Evaluación del desempeño) es el CHECK: medir métricas, realizar auditorías internas (autocontrol de la organización), y revisar el SGSI con la dirección — no confundir con el proceso de certificación externa, que es voluntario y no se cubre en este curso.
3. La **cláusula 10** (Mejora) es el ACT: cuando la auditoría detecta no-conformidades, se investiga la causa raíz, se corrige, y se verifica la efectividad. La mejora continua busca proactivamente hacer el SGSI más efectivo.
4. El ciclo PDCA completo — cláusulas 4-7 (PLAN), 8 (DO), 9 (CHECK), 10 (ACT) — es el motor que hace que el SGSI mejore con el tiempo en lugar de degradarse.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Auditoría interna ISO 27001 — Guía práctica (ISO27001Security):** https://www.iso27001security.com/html/internal_audit.html — Descripción del proceso de auditoría interna con ejemplos.
- **No-conformidades y acciones correctivas (Advisera):** https://advisera.com/27001academy/knowledgebase/how-to-handle-corrective-actions-in-iso-27001/ — Proceso detallado de gestión de no-conformidades en lenguaje accesible.
- **ISO 27001 Checklist (IT Governance):** https://www.itgovernance.co.uk/iso-27001-implementation-checklist — Lista de verificación de requisitos para las 10 cláusulas.

---

*Siguiente: [ISO 27002 — Controles de Seguridad (Parte 1)](iso27002-controles-p1.md)*
