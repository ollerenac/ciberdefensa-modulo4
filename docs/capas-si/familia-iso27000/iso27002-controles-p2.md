---
# Horas asignadas: 4 hrs
# Tipo: Teoría
---

# ISO 27002 — Controles de Seguridad (Parte 2)

> **Duración:** 4 horas | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Identificar los controles clave de cada una de las 4 categorías de ISO 27002:2022 y explicar su propósito.
- Relacionar controles específicos con activos y amenazas en un contexto de comunicaciones militares.
- Definir qué es el Statement of Applicability (SoA) y explicar por qué es el documento central del SGSI.

---

> **Recapitulación:** En la clase anterior (Parte 1) presentamos la estructura de ISO 27002:2022: 93 controles organizados en 4 temas (Organizacional, Personas, Físico, Tecnológico), y cómo el Annex A de ISO 27001 funciona como puente hacia ese catálogo. Esta clase entra en el detalle de los controles más relevantes de cada categoría, con ejemplos directamente aplicables al trabajo de los Técnicos de comunicaciones.

---

## Tema 5 — Controles Organizacionales

Los controles organizacionales son los más numerosos (37 en total) porque la seguridad comienza con decisiones de gestión. Sin políticas, sin procesos documentados, sin responsabilidades asignadas, los controles técnicos funcionan en el vacío.

Los controles organizacionales más relevantes para el contexto militar se presentan en la siguiente tabla:

| Control | Qué significa | Ejemplo militar |
|---------|--------------|-----------------|
| **5.1 — Políticas de seguridad** | La organización tiene políticas de seguridad formales, aprobadas por la dirección, comunicadas a todo el personal, y revisadas periódicamente. | La Jefatura de Comunicaciones tiene un SOP de comunicaciones seguras aprobado por el Comandante, distribuido a cada Técnico, y revisado antes de cada ciclo operacional. |
| **5.9 — Inventario de activos** | La organización mantiene un inventario actualizado de todos sus activos de información — quién es el responsable de cada uno y cómo está clasificado. | Registro de todos los equipos con material criptográfico: número de serie, ubicación, responsable, fecha de última verificación. Sin este inventario, es imposible saber si algún equipo está desaparecido. |
| **5.12 — Clasificación de la información** | La información se clasifica según su sensibilidad (por ejemplo: pública, interna, confidencial, secreta) y se maneja según esa clasificación. | Los planes de comunicaciones para una operación se clasifican como Reservado. Solo el personal con habilitación correspondiente puede acceder a ellos; se transmiten solo por canales cifrados; se destruyen según procedimiento al caducar. |
| **5.15 — Control de acceso** | Se define quién puede acceder a qué información y bajo qué condiciones. El acceso se otorga según la necesidad de conocer, no según el rango. | El operador de radio tiene acceso al sistema de comunicaciones tácticas pero no a los terminales del sistema de inteligencia, aunque físicamente estén en la misma sala. El acceso se rige por la función, no por la jerarquía. |
| **5.24 / 5.25 — Gestión de incidentes** | La organización tiene un proceso documentado para detectar, reportar, analizar y responder a incidentes de seguridad. El 5.24 establece el proceso; el 5.25 define cómo evaluar y responder. | Cuando un radio táctico produce resultados inesperados de autenticación, el Técnico sabe exactamente a quién reportarlo, en qué plazo (máximo 2 horas), y qué información incluir. Eso es 5.24/5.25 en operación. |

!!! example "Aplicación en entorno castrense"
    El control 5.12 (clasificación de información) es particularmente crítico en contextos militares porque la clasificación incorrecta tiene consecuencias directas para la misión. Un plan de operaciones enviado por canal no cifrado porque alguien no lo marcó como Reservado puede comprometer toda la operación. La clasificación no es burocracia — es la primera línea de defensa para determinar qué protecciones aplican a cada pieza de información.

---

## Tema 6 — Controles de Personas

El Tema 6 tiene solo 8 controles, pero su impacto es desproporcionado. Según estudios de la industria, la mayoría de los incidentes de seguridad tienen un componente humano. Los controles de personas no eliminan el factor humano — lo gestionan.

| Control | Qué significa | Ejemplo militar |
|---------|--------------|-----------------|
| **6.1 — Verificación de antecedentes** | Antes de asignar a una persona a un puesto con acceso a información sensible, se verifica su historial y se confirma que es confiable para ese nivel de acceso. | El proceso de habilitación de seguridad antes de asignar a un Técnico a trabajar con material criptográfico es exactamente el control 6.1. No es un procedimiento burocrático — es el control que protege el activo más sensible de las comunicaciones militares. |
| **6.3 — Concienciación, educación y formación en seguridad** | Todo el personal que trabaja con información o sistemas relevantes para el SGSI recibe formación regular sobre sus obligaciones de seguridad, las políticas vigentes y las amenazas actuales. | La instrucción de seguridad de comunicaciones al inicio de cada ciclo operacional, la difusión de nuevas amenazas (por ejemplo, nuevas técnicas de interceptación detectadas), y el entrenamiento en el uso correcto de los sistemas cifrados son todos parte del control 6.3. |
| **6.8 — Reporte de eventos de seguridad de la información** | El personal sabe que tiene la obligación de reportar cualquier evento que pueda ser un incidente de seguridad — incluso si no está seguro de que lo sea. | Un Técnico que nota que su radio táctico produjo un error de autenticación inesperado debe reportarlo, aunque piense que fue un fallo técnico menor. La cultura de reporte es la diferencia entre detectar un incidente temprano y descubrirlo demasiado tarde. |

!!! warning "La cadena de reporte debe funcionar en ambas direcciones"
    El control 6.8 solo funciona si el personal confía en que reportar incidentes no tiene consecuencias negativas para ellos. Si el mensajero es castigado, los incidentes dejan de reportarse — y la organización pierde visibilidad sobre lo que está ocurriendo en sus sistemas.

---

## Tema 7 — Controles Físicos

Los controles físicos protegen los activos de información en el espacio físico. Para las fuerzas armadas, estos son frecuentemente los controles más maduros, porque la tradición de seguridad física militar tiene décadas de desarrollo. Sin embargo, los controles físicos modernos van más allá de "poner un guardia" — incluyen monitoreo electrónico, gestión de medios y protección del entorno.

| Control | Qué significa | Ejemplo militar |
|---------|--------------|-----------------|
| **7.1 — Perímetros de seguridad física** | Las áreas que contienen información sensible o activos críticos tienen perímetros definidos y controlados — no basta con cerrar con llave, el perímetro debe ser verificable y auditado. | La sala de comunicaciones del puesto de mando tiene un perímetro definido: puerta con cerradura de combinación, registro de acceso, ventanas protegidas, y separación física de áreas de tránsito. El perímetro no es opcional — sin él, no hay forma de controlar quién tuvo acceso. |
| **7.2 — Controles de acceso físico** | El acceso a áreas seguras está controlado, registrado y limitado al personal con necesidad real de estar allí. | El registro de acceso a un SCIF incluye: quién entró, a qué hora, con qué propósito, quién autorizó. El registro existe independientemente de si hubo algún incidente — la trazabilidad preventiva es parte del control. |
| **7.4 — Monitoreo de seguridad física** | Las áreas seguras son monitoreadas activamente para detectar accesos no autorizados, actividades anómalas o condiciones ambientales peligrosas. | Cámaras en la entrada de la sala de comunicaciones, alarmas de movimiento fuera del horario de operación, y sensores ambientales (temperatura, humedad) en los armarios de equipos son todos parte del control 7.4. |

---

## Tema 8 — Controles Tecnológicos

Los controles tecnológicos son los más técnicos del catálogo — los que los Técnicos configuran y mantienen directamente. Con 34 controles, es el segundo tema más numeroso, y el más relevante para el trabajo diario del personal de comunicaciones.

| Control | Qué significa | Ejemplo militar |
|---------|--------------|-----------------|
| **8.5 — Autenticación segura** | Los sistemas requieren que los usuarios demuestren su identidad antes de acceder, y esa autenticación es resistente a ataques. Contraseñas fuertes, autenticación de dos factores donde sea posible, sin credenciales compartidas. | Los terminales de los sistemas C2 requieren contraseña individual — no contraseña de sala compartida. Cada operador tiene sus propias credenciales para que el acceso sea trazable a una persona. |
| **8.7 — Protección contra malware** | Los sistemas tienen protección activa contra software malicioso: antivirus actualizado, análisis de archivos antes de ejecutar, prevención de ejecución de código no autorizado. | Antes de conectar cualquier dispositivo USB a un equipo del puesto de mando, se analiza con antivirus en un equipo de cuarentena. Esta práctica implementa 8.7 en el contexto de un vector de ataque muy común en entornos militares. |
| **8.15 — Registro de actividad (logging)** | Los sistemas generan registros de eventos de seguridad (accesos, errores, cambios de configuración) que se almacenan de forma segura y se revisan regularmente. | Los terminales C2 registran cada inicio de sesión, cada acceso a documentos clasificados, y cada cambio de configuración. Si ocurre un incidente, los logs son la primera fuente de evidencia para entender qué pasó. |
| **8.20 — Seguridad de redes** | Las redes están segmentadas, monitoreadas y controladas. El tráfico entre segmentos está autorizado y validado. | La red de comunicaciones tácticas está segmentada de la red administrativa. Un equipo comprometido en la red administrativa no puede acceder directamente a los sistemas C2 porque están en un segmento separado con acceso controlado. |
| **8.23 — Filtrado web** | El acceso a internet desde los sistemas de la organización está filtrado para bloquear sitios maliciosos y categorías no permitidas. | En los equipos de la jefatura de comunicaciones, el proxy filtra el acceso a categorías de sitios de riesgo elevado. Un Técnico que accidentalmente visita un sitio de phishing es bloqueado antes de que pueda descargar malware. |

!!! example "Aplicación en entorno castrense"
    El control 8.9 (gestión de la configuración) no aparece en la tabla porque es un control de proceso más que técnico, pero merece mención: establece que la configuración de seguridad de los sistemas debe ser documentada, controlada y verificada antes de poner un equipo en operación. En comunicaciones militares, esto significa que antes de desplegar cualquier equipo — radio, terminal, servidor — su configuración de seguridad debe estar definida, documentada y verificada. Los equipos no se despliegan "como vienen de fábrica".

---

## Statement of Applicability (SoA)

El **Statement of Applicability** (SoA) — Declaración de Aplicabilidad — es el documento central del SGSI que conecta la evaluación de riesgos con los controles seleccionados. Es el documento donde la organización documenta, para cada uno de los 93 controles del Annex A de ISO 27001, si lo aplica o no, y por qué.

!!! note "Definición"
    El **Statement of Applicability** es un documento requerido por ISO 27001 que lista todos los controles del Annex A e indica para cada uno: si está incluido o excluido del SGSI, la justificación de esa decisión, y si está implementado o en proceso de implementación.

### Por qué existe el SoA

El SoA existe porque no todos los controles del Annex A son aplicables a todas las organizaciones. Una empresa de desarrollo de software no tiene sala de comunicaciones cifradas; una unidad militar puede no tener operaciones en la nube. Aplicar todos los 93 controles sin distinción sería costoso, ineficiente e inapropiado para muchos contextos.

El SoA es el mecanismo que documenta las decisiones de la organización:
- **Incluido y aplicado:** El control es relevante para el contexto y está implementado.
- **Incluido y en proceso:** El control es relevante y está siendo implementado.
- **Excluido con justificación:** El control no aplica al contexto, con la razón documentada.

La justificación de las exclusiones es tan importante como la de las inclusiones. Una organización no puede excluir arbitrariamente controles que sus riesgos requieren — la exclusión debe estar respaldada por la evaluación de riesgos.

### Cómo se usa el SoA en la práctica

| Situación | Cómo usa el SoA |
|-----------|----------------|
| Implementación del SGSI | El equipo revisa los 93 controles del Annex A, evalúa cuáles aplican al alcance definido, y documenta la decisión en el SoA |
| Auditoría interna | El auditor usa el SoA para saber qué controles verificar — solo los incluidos y aplicados son auditables |
| Revisión por la dirección | El mando revisa el SoA para confirmar que las decisiones de aplicabilidad siguen siendo correctas dado el contexto actual |
| Comunicación con terceros | El SoA comunica a clientes o socios qué controles tiene la organización, sin revelar detalles de implementación |

### Ejemplo de SoA simplificado

Una Jefatura de Comunicaciones que construye un SoA básico podría tener entradas como estas:

| Control Annex A | Título | Aplicable | Justificación / Estado |
|-----------------|--------|-----------|----------------------|
| 5.1 | Políticas de seguridad | Sí | SOP de comunicaciones seguras aprobado — implementado |
| 5.9 | Inventario de activos | Sí | Registro de equipos criptográficos — en proceso |
| 5.12 | Clasificación de información | Sí | Procedimiento de clasificación — implementado |
| 7.1 | Perímetros de seguridad física | Sí | Sala de comunicaciones con acceso controlado — implementado |
| 8.17 | Sincronización de relojes | No aplica | Los sistemas de comunicaciones tácticos no dependen de sincronización NTP — excluido con justificación |

El SoA no tiene que cubrir todos los 93 controles en detalle desde el primer día — pero debe existir como documento vivo que refleje el estado actual del SGSI.

---

## Aplicación en Contexto Castrense

Los controles ISO 27002 no son ajenos a la cultura militar — muchos ya existen en las organizaciones de comunicaciones, aunque con nombres diferentes. El SGSI los hace explícitos, documentados y auditables.

**Ejemplo 1 — El SoA en una Jefatura de Comunicaciones:**
La Jefatura de Comunicaciones del Batallón de Comunicaciones N°3 construye su SoA inicial. Incluye los controles 5.1 (política), 5.9 (inventario), 5.12 (clasificación), 5.15 (control de acceso), 6.1 (habilitación de personal), 6.3 (instrucción), 7.1/7.2 (perímetros y acceso físico), 8.5 (autenticación), 8.15 (logging) y 8.20 (segmentación de red). Excluye el control 8.23 (filtrado web) porque los sistemas de comunicaciones tácticas no tienen acceso a internet. La justificación queda documentada: "Los terminales del sistema C2 están en red cerrada sin conectividad a internet — control 8.23 no aplicable."

**Ejemplo 2 — Los 4 temas en la respuesta a incidentes:**
Cuando ocurre un incidente de seguridad en la red de comunicaciones, los 4 temas trabajan en conjunto: el control organizacional 5.24/5.25 define el proceso de respuesta; el control de personas 6.8 asegura que el incidente fue reportado; el control físico 7.4 proporciona los registros de monitoreo físico que ayudan a reconstruir lo ocurrido; y los controles tecnológicos 8.15 (logs) y 8.20 (segmentación de red) limitan el impacto y proveen evidencia. Sin los 4 temas, la respuesta al incidente es incompleta.

---

## Resumen

1. ISO 27002:2022 tiene 93 controles en 4 temas: **Organizacional** (5.x — 37 controles), **Personas** (6.x — 8 controles), **Físico** (7.x — 14 controles) y **Tecnológico** (8.x — 34 controles).
2. Los controles clave por tema incluyen: 5.1 (políticas), 5.9 (inventario activos), 5.12 (clasificación), 5.15 (control acceso); 6.1 (habilitación), 6.3 (instrucción), 6.8 (reporte); 7.1/7.2 (perímetros físicos), 7.4 (monitoreo físico); 8.5 (autenticación), 8.7 (antimalware), 8.15 (logging), 8.20 (redes), 8.23 (filtrado web).
3. El **Statement of Applicability (SoA)** es el documento central del SGSI: lista los 93 controles del Annex A e indica para cada uno si se aplica, si está implementado, y la justificación de la decisión.
4. El SoA conecta la evaluación de riesgos con los controles seleccionados — es la evidencia documentada de que el SGSI está diseñado para el contexto específico de la organización.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **ISO 27002:2022 — lista de controles (Secureframe):** https://secureframe.com/hub/iso-27001/controls — Descripción de los 93 controles en lenguaje accesible con casos de uso.
- **Statement of Applicability — guía práctica (Advisera):** https://advisera.com/27001academy/knowledgebase/the-importance-of-statement-of-applicability-for-iso-27001/ — Cómo construir un SoA con ejemplos.
- **Diferencia entre Annex A e ISO 27002 (ISO27001Security):** https://www.iso27001security.com/html/27001.html — Explicación técnica de cómo los dos documentos se complementan.

---

*Siguiente: [ISO 27005 — Gestión del Riesgo (Parte 1)](iso27005-riesgo-p1.md)*
