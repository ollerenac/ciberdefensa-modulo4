---
# Horas asignadas: 1 hr
# Tipo: Examen
---

# Examen — Gestión de Riesgos y Vulnerabilidades

> **Duración:** 1 hora | **Asignatura:** Gestión de Riesgos y Vulnerabilidades | **Unidad:** Mitigación y Control de Riesgos

## Instrucciones generales

- **Duración:** 60 minutos.
- **Puntaje total:** 100 puntos.
- **Materiales permitidos:** ninguno — examen a libro cerrado.
- **Instrucciones de entrega:** escribir nombre y grado en la parte superior de la hoja antes de comenzar.
- Responder las tres partes en orden. No pasar a la siguiente parte sin completar la anterior.
- En la Parte A, escribir las respuestas directamente sobre las líneas en blanco de la tabla. Usar los valores A (Alta), M (Media) o B (Baja) para las columnas de probabilidad e impacto.

---

## Escenario

La **Jefatura de Comunicaciones del Batallón de Comunicaciones N°3** (JC-BC3) ha completado una revisión interna de seguridad de sus sistemas de información. La sala de comunicaciones cuenta con un servidor de archivos, cuatro terminales C2 (sistema de mando y control), un conjunto de radios HF/VHF para comunicaciones tácticas, y seis laptops de campo utilizadas en operaciones fuera del cuartel.

Durante la revisión se identificaron varias situaciones de riesgo sin resolver:

- Las laptops de campo no tienen cifrado de disco habilitado y se han dejado sin supervisión en vehículos tácticos en al menos dos ocasiones en los últimos 3 meses.
- El firmware de las radios HF tiene una vulnerabilidad publicada (CVE con score 7.5) para la cual el fabricante aún no ha liberado un parche.
- Un miembro del equipo de turno ha estado compartiendo su contraseña de acceso al servidor de archivos con el personal del siguiente turno "para ahorrar tiempo en el cambio".

El Técnico de guardia ha recibido la tarea de evaluar estos riesgos y proponer acciones.

---

## Parte A — Matriz de Riesgo (30 puntos)

**Instrucciones:** Complete las celdas marcadas con __________ basándose en el escenario descrito y en los conocimientos de la asignatura. Justifique brevemente su respuesta en la columna de Tratamiento.

| # | Activo | Amenaza | Vulnerabilidad | Prob. (A/M/B) | Impacto (A/M/B) | Riesgo | Tratamiento propuesto |
|---|--------|---------|----------------|---------------|-----------------|--------|----------------------|
| 1 | Sistema de comunicaciones táctico (radios HF) | Malware / explotación de firmware | __________ | Alta | __________ | __________ | __________ |
| 2 | __________ | Acceso físico no autorizado | Sin cifrado de disco activo | __________ | Alta | __________ | __________ |
| 3 | Base de datos de personal (servidor de archivos) | __________ | Parches del sistema operativo sin aplicar (90 días) | Media | __________ | __________ | __________ |
| 4 | Laptops de campo | Robo o pérdida del equipo | Sin cifrado de disco | __________ | __________ | __________ | __________ |
| 5 | Red de la unidad | Insider threat | __________ | Baja | __________ | __________ | __________ |

---

## Parte B — Selección Múltiple (40 puntos — 10 puntos por pregunta)

**Instrucciones:** Marque con una X la opción correcta. Solo una respuesta es correcta por pregunta.

---

**P1.** Un Técnico detecta que la radio táctica VHF del puesto de mando avanzado tiene el firmware sin actualizar desde hace 12 meses. El fabricante publicó un boletín de seguridad indicando que esa versión tiene una vulnerabilidad conocida. ¿Cuál de los siguientes términos describe correctamente el firmware sin actualizar?

- A) Una amenaza
- B) Un impacto
- C) Una vulnerabilidad
- D) Un riesgo

---

**P2.** Durante la revisión de seguridad de la JC-BC3, el Técnico detecta que un miembro del equipo de turno comparte su contraseña de acceso al servidor de archivos con el personal del turno siguiente. Según la taxonomía de tipos de riesgo castrense, ¿en qué categoría principal se clasifica este comportamiento?

- A) Riesgo Operacional — afecta directamente la ejecución de la misión por falla de proceso de mando
- B) Riesgo Físico — implica acceso no autorizado a instalaciones de la unidad
- C) Riesgo Humano (negligencia) — violación de la política de credenciales por comodidad operacional
- D) Riesgo TI — vulnerabilidad técnica en el sistema de autenticación del servidor

---

**P3.** El Técnico de la JC-BC3 recibe un boletín de Microsoft Patch Tuesday que indica que el sistema operativo Windows Server 2016 del servidor de archivos tiene una vulnerabilidad con score CVSS 9.2. Según los plazos operacionales de la asignatura, ¿cuál es la acción correcta?

- A) Documentar la vulnerabilidad en el registro de riesgos y revisarla en el próximo ciclo mensual
- B) Elaborar un plan de mitigación para los próximos 30 días
- C) Informar al Jefe de sección en el próximo reporte de turno y planificar la remediación en 7 días
- D) Escalar inmediatamente al Jefe de sección y aplicar el parche o control compensatorio dentro de las 24 horas

---

**P4.** El IDS de la red de la JC-BC3 genera una alerta cuando detecta un volumen inusual de tráfico saliente desde el servidor de archivos hacia una dirección IP desconocida. ¿Qué tipo de control es el IDS en este escenario?

- A) Preventivo — porque bloquea el tráfico no autorizado antes de que salga de la red
- B) Detectivo — porque alerta sobre un comportamiento anómalo que ya está ocurriendo
- C) Correctivo — porque restaura el estado normal de la red después del incidente
- D) Preventivo y correctivo a la vez — porque detiene el tráfico y notifica al administrador simultáneamente

---

## Parte C — Respuesta Corta (30 puntos — 10 puntos por pregunta)

**Instrucciones:** Responder de forma concisa. Las respuestas no deben superar las 5 líneas por pregunta.

---

**P5.** La laptop del Oficial de Inteligencia de la JC-BC3 fue olvidada dentro de un vehículo táctico sin supervisión durante 3 horas. La laptop no tiene cifrado de disco habilitado. Complete los siguientes campos para una fila del registro de riesgo de este activo:

- **Activo:** ________________________________________________________________________
- **Amenaza:** _______________________________________________________________________
- **Vulnerabilidad:** _________________________________________________________________
- **Probabilidad (A/M/B) y justificación:** ______________________________________________
- **Impacto (A/M/B) y justificación:** _________________________________________________

---

**P6.** El proveedor de software de gestión de comunicaciones usado en la JC-BC3 publica el siguiente aviso de seguridad:

> "Vulnerabilidad identificada en CommManager Pro versión 3.2.1 y anteriores. Score CVSS base: **9.2**. La vulnerabilidad permite a un atacante con acceso a la red ejecutar código arbitrario sin autenticación. Se recomienda actualizar a la versión 3.3.0 inmediatamente."

Responda las siguientes preguntas:

a) ¿A qué banda de severidad CVSS corresponde el score 9.2?

b) ¿La vulnerabilidad obliga a tomar acción inmediata o puede esperar al ciclo de mantenimiento mensual? Justifique.

c) Escriba la primera línea del campo "Descripción" del reporte de vulnerabilidad para este hallazgo.

---

**P7.** Describa en 2 a 3 oraciones la cadena de reporte de vulnerabilidades en la JC-BC3: ¿quién detecta la vulnerabilidad, quién recibe el reporte inicial, y quién toma la decisión final de acción?

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

## Parte D — Profundización (sin puntuación adicional — práctica formativa)

**Instrucciones:** Estas preguntas no suman puntos al total pero sirven para autoevaluación. Responder si el tiempo lo permite.

---

**P8.** El Técnico de la JC-BC3 identifica que las radios HF del batallón usan firmware con una vulnerabilidad con CVSS 7.5 y sin parche disponible del fabricante. ¿Cuál es el control compensatorio más adecuado?

- A) Dejar las radios fuera de servicio hasta que el parche esté disponible
- B) Reducir la exposición a frecuencias no operacionales y verificar integridad de firmware post-sesión mientras se espera el parche
- C) Actualizar manualmente el firmware descargando la versión del fabricante de internet
- D) Aceptar el riesgo sin documentación porque no hay solución disponible

---

**P9.** ¿Cuál de los siguientes describe mejor la diferencia entre una vulnerabilidad y una amenaza?

- A) Una vulnerabilidad es externa y una amenaza es interna al sistema
- B) Una vulnerabilidad es una debilidad en el activo; una amenaza es el agente que puede explotarla
- C) Una vulnerabilidad solo aplica a software; una amenaza aplica a hardware y personas
- D) No hay diferencia operacional — ambos términos se usan indistintamente en los registros de riesgo

---

**P10.** El Oficial de la JC-BC3 debe decidir si acepta o mitiga una vulnerabilidad con CVSS 3.2 en un sistema de backup secundario que no está conectado a la red operacional. ¿Qué tratamiento es más apropiado?

- A) Mitigar de inmediato — todo score CVSS requiere remediación dentro de las 24 horas
- B) Aceptar el riesgo documentando la decisión — severidad Low en un activo de baja criticidad puede ser aceptada con control administrativo
- C) Eliminar el activo — si no se puede parchar, el sistema debe ser dado de baja
- D) Transferir el riesgo contratando un proveedor externo de gestión de vulnerabilidades

---

!!! danger "Solo instructor — Clave de Respuestas"

    ## Tabla de tiempo sugerido

    | Sección | Actividad | Tiempo |
    |---------|-----------|--------|
    | — | Lectura del escenario e instrucciones | 5 min |
    | Parte A | Completar la matriz de riesgo | 20 min |
    | Parte B | Selección múltiple (4 preguntas) | 20 min |
    | Parte C | Respuesta corta (3 preguntas) | 15 min |
    | Parte D | Preguntas de profundización — sin puntuación (si queda tiempo) | — |
    | **Total** | | **60 min** |

    ---

    ## Parte A — Clave de la Matriz de Riesgo (30 puntos)

    Criterio de corrección: 2 puntos por cada celda de contenido en blanco correctamente completada (Activo/Amenaza/Vulnerabilidad, Probabilidad, Impacto, Nivel de Riesgo). La columna "Tratamiento propuesto" se evalúa cualitativamente: respuesta razonada y coherente con el nivel de riesgo = correcta; respuesta ausente o contradictoria = 0 pts. Celdas de contenido evaluadas: 3 por fila × 5 filas = 15 celdas × 2 puntos = 30 puntos.

    | # | Activo | Amenaza | Vulnerabilidad | Prob. | Impacto | Riesgo | Tratamiento |
    |---|--------|---------|----------------|-------|---------|--------|-------------|
    | 1 | Sistema de comunicaciones táctico (radios HF) | Malware / explotación de firmware | **Firmware con vulnerabilidad publicada sin parche disponible** | Alta | **Alta** | **Alto** | **Mitigar con control compensatorio: reducir exposición de las radios a frecuencias no operacionales; monitorear integridad de firmware post-sesión; planificar actualización en cuanto el parche esté disponible.** |
    | 2 | **Laptops de campo** | Acceso físico no autorizado | Sin cifrado de disco activo | **Alta** | Alta | **Alto** | **Mitigar: habilitar cifrado de disco (BitLocker) en todas las laptops de campo. Hasta que el cifrado esté activo, establecer custodia permanente de equipos — no dejarlos en vehículos sin supervisión.** |
    | 3 | Base de datos de personal (servidor de archivos) | **Acceso no autorizado / explotación de vulnerabilidad del SO** | Parches del sistema operativo sin aplicar (90 días) | Media | **Alta** | **Alto** | **Mitigar: aplicar actualizaciones de seguridad pendientes en el servidor. Priorizar las marcadas como High/Critical según el score CVSS.** |
    | 4 | Laptops de campo | Robo o pérdida del equipo | Sin cifrado de disco | **Alta** | **Alta** | **Alto** | **Mitigar: habilitar cifrado de disco (BitLocker). Control inmediato: establecer política de custodia de equipos en operaciones de campo.** |
    | 5 | Red de la unidad | Insider threat | **Contraseñas compartidas entre turnos / ausencia de política de credenciales individuales** | Baja | **Alta** | **Medio** | **Mitigar: implementar política de uso exclusivo de credenciales personales. Revocar la contraseña compartida inmediatamente. Auditar el log de accesos de los últimos 30 días.** |

    **Notas de corrección:**
    - Fila 1, Vulnerabilidad: "firmware sin parchear" o "vulnerabilidad de firmware publicada" son aceptables.
    - Fila 3, Amenaza: "acceso no autorizado", "explotación de vulnerabilidad", "malware" son todas aceptables.
    - Fila 5, Riesgo: Probabilidad Baja × Impacto Alta = **Medio** según la matriz 3×3 (ver tabla de la clase).
    - Fila 4, Prob.: Alta es correcta (laptops de campo ya han sido dejadas sin supervisión en 2 ocasiones en 3 meses, según el escenario).

    ---

    ## Parte B — Clave de Selección Múltiple (40 puntos)

    **P1 — Respuesta correcta: C) Una vulnerabilidad**

    Justificación: El firmware sin actualizar es una debilidad del activo (la radio) que puede ser explotada por una amenaza (el atacante que usa el exploit de la vulnerabilidad publicada). No es la amenaza (la amenaza es quien explota la falla), no es el impacto (que sería la pérdida de comunicación), y no es el riesgo (que es la combinación de amenaza × vulnerabilidad × probabilidad × impacto).

    Opciones incorrectas:
    - A) Incorrecto: La amenaza es el actor hostil o el malware, no el firmware desactualizado.
    - B) Incorrecto: El impacto es la consecuencia (pérdida de comunicaciones), no la vulnerabilidad.
    - D) Incorrecto: El riesgo es la probabilidad de que la amenaza explote la vulnerabilidad y cause impacto — es el concepto que engloba todos los elementos, no solo la debilidad del activo.

    ---

    **P2 — Respuesta correcta: C) Riesgo Humano (negligencia)**

    Justificación: Compartir contraseñas es un comportamiento negligente del personal — no hay intención maliciosa, pero viola la política de seguridad y destruye la trazabilidad de auditoría. Es la categoría Riesgo Humano, subcategoría negligencia.

    Opciones incorrectas:
    - A) Incorrecto: El riesgo operacional afecta la ejecución de la misión por falla de procesos de C2. Compartir contraseñas no impacta directamente la misión en tiempo real.
    - B) Incorrecto: El riesgo físico implica acceso o daño a equipos en el entorno físico. La contraseña compartida es un riesgo de autenticación, no de acceso físico.
    - D) Incorrecto: El riesgo TI es una vulnerabilidad técnica del sistema de autenticación. La falla aquí es de comportamiento humano, no del sistema técnico. Si el sistema tuviera MFA habilitado, la contraseña compartida sería menos crítica — pero el problema descrito es el comportamiento, no la configuración del sistema.

    ---

    **P3 — Respuesta correcta: D) Escalar inmediatamente al Jefe de sección y aplicar el parche o control compensatorio dentro de las 24 horas**

    Justificación: Score CVSS 9.2 = banda Critical (9.0–10.0). La regla de plazo operacional para Critical es acción dentro de las 24 horas siguientes a la detección. El activo es el servidor de archivos de la JC-BC3, que es un activo compartido crítico para la unidad — lo que confirma la urgencia.

    Opciones incorrectas:
    - A) Incorrecto: Ciclo mensual es el plazo para severidad Low (0–3.9). CVSS 9.2 es Critical.
    - B) Incorrecto: Plan de 30 días corresponde a severidad Medium (4.0–6.9).
    - C) Incorrecto: 7 días corresponde a severidad High (7.0–8.9). CVSS 9.2 supera ese umbral.

    ---

    **P4 — Respuesta correcta: B) Detectivo — porque alerta sobre un comportamiento anómalo que ya está ocurriendo**

    Justificación: El IDS en este escenario detecta tráfico inusual y genera una alerta — no bloquea el tráfico, solo detecta y alerta. Esta es la función primaria de un control detectivo: alertar cuando un incidente está ocurriendo o ya ocurrió.

    Opciones incorrectas:
    - A) Incorrecto: Un IDS en modo detección (no prevención) no bloquea el tráfico. Un IPS (Intrusion Prevention System) cumple la función preventiva de bloqueo activo.
    - C) Incorrecto: Un control correctivo restaura el estado normal después del incidente (ej. restaurar backup, revocar credenciales). El IDS no restaura nada.
    - D) Incorrecto: Un IDS estándar no bloquea el tráfico — solo alerta. La opción describe una función de IPS, no de IDS.

    ---

    ## Parte C — Respuestas Modelo (30 puntos)

    **P5 — Respuesta modelo (10 puntos)**

    - **Activo:** Laptop del Oficial de Inteligencia de la JC-BC3 (equipo de campo con documentos operacionales).
    - **Amenaza:** Acceso físico no autorizado o robo del equipo mientras estaba sin supervisión en el vehículo táctico.
    - **Vulnerabilidad:** Sin cifrado de disco habilitado — cualquier persona con acceso físico puede leer los archivos sin necesidad de credenciales.
    - **Probabilidad (Alta / Media):** Media o Alta son ambas aceptables. Justificación para Alta: el equipo ya estuvo sin supervisión en vehículo táctico; la conducta es recurrente. Justificación para Media: no hay evidencia de intento de acceso real, solo exposición física. El instructor acepta cualquiera de las dos si la justificación es consistente.
    - **Impacto (Alta):** Alta. La laptop del Oficial de Inteligencia contiene con alta probabilidad información clasificada (planes de operaciones, inteligencia de situación). Un compromiso de esa información puede afectar la seguridad del personal y la integridad de la misión.

    Criterio de puntaje: 2 puntos por campo correctamente completado con justificación coherente. Total: 5 campos × 2 puntos = 10 puntos.

    ---

    **P6 — Respuesta modelo (10 puntos)**

    a) **Banda Critical (9.0–10.0).** Score 9.2 cae en la banda Critical.

    b) **Acción inmediata obligatoria — no puede esperar al ciclo mensual.** La banda Critical tiene un plazo operacional de 24 horas. Adicionalmente, el aviso indica que la vulnerabilidad permite ejecución de código sin autenticación desde la red — un atacante con acceso a la red de la unidad puede explotar la falla sin credenciales. Esperar 30 días expone la red de la JC-BC3 a un riesgo inaceptable durante ese período.

    c) **Primera línea del campo Descripción (ejemplo aceptable):**
    "CommManager Pro versión 3.2.1 instalada en [nombre del sistema] presenta una vulnerabilidad Critical (CVSS 9.2) que permite ejecución remota de código sin autenticación por un actor con acceso a la red de la unidad."

    El instructor evalúa: ¿incluye el nombre del producto? ¿incluye la severidad o score? ¿describe el impacto (ejecución de código)? ¿menciona que no requiere autenticación? 2-3 de estos elementos = puntaje completo.

    Criterio de puntaje: a) 2 puntos, b) 4 puntos (2 por la respuesta + 2 por la justificación), c) 4 puntos.

    ---

    **P7 — Respuesta modelo (10 puntos)**

    "El Técnico de turno detecta la vulnerabilidad durante la revisión de sistemas o en el ejercicio de sus funciones. Completa el reporte de vulnerabilidad con los datos del hallazgo y lo entrega al Jefe inmediato dentro del plazo según la severidad (24 horas para Critical, 7 días para High). El Jefe inmediato evalúa si puede actuar con sus propios recursos o escala al Oficial de Seguridad. El Oficial de Seguridad toma la decisión formal: mitigar con los controles disponibles, aceptar el riesgo residual por escrito, o escalar al mando superior si el impacto supera su nivel de autoridad."

    Criterio de aceptación (10 puntos): La respuesta debe mencionar al menos tres actores (Técnico, Jefe, Oficial de Seguridad) con su rol específico. Respuestas que mencionan solo dos actores o que no diferencian quién decide vs. quién reporta reciben puntaje parcial (5-7 puntos).

    ---

    ## Parte D — Clave de Profundización (sin puntuación — práctica formativa)

    **P8 — Respuesta correcta: B) Reducir la exposición a frecuencias no operacionales y verificar integridad de firmware post-sesión mientras se espera el parche**

    Justificación: Sin parche disponible, la acción correcta es aplicar controles compensatorios que reduzcan la superficie de ataque: limitar el uso de las radios a frecuencias autorizadas (reduce la ventana de ataque si la vulnerabilidad requiere exposición en frecuencias específicas) y verificar la integridad del firmware tras cada sesión (detecta modificaciones no autorizadas). La opción A paraliza la operación innecesariamente. La opción C introduce un riesgo adicional al descargar firmware de internet sin verificación de cadena de suministro. La opción D es inaceptable — el riesgo sin documentación no es aceptado, es ignorado.

    ---

    **P9 — Respuesta correcta: B) Una vulnerabilidad es una debilidad en el activo; una amenaza es el agente que puede explotarla**

    Justificación: La distinción fundamental de la taxonomía de riesgo: la vulnerabilidad es la condición del activo (el firmware sin parchear, la contraseña débil, el puerto abierto), mientras que la amenaza es el actor o evento que puede aprovecharla (el atacante externo, el malware, el insider). Las opciones A, C y D contienen afirmaciones incorrectas — las vulnerabilidades pueden ser de hardware, física o de proceso, no solo de software; y los dos términos tienen significados técnicos distintos y no son intercambiables.

    ---

    **P10 — Respuesta correcta: B) Aceptar el riesgo documentando la decisión — severidad Low en un activo de baja criticidad puede ser aceptada con control administrativo**

    Justificación: CVSS 3.2 es banda Low (0.1–3.9). Un activo de backup secundario sin conexión a la red operacional tiene criticidad baja. La combinación de baja severidad técnica + baja criticidad del activo hace que la aceptación formal del riesgo con documentación sea el tratamiento apropiado. La opción A es incorrecta — el plazo de 24 horas es para Critical (9.0–10.0). La opción C no corresponde a ningún tratamiento estándar en la taxonomía (eliminar un activo útil por una vulnerabilidad Low es desproporcionado). La opción D es incorrecta en el contexto castrense — los riesgos en sistemas de la unidad no se transfieren a proveedores externos de esa forma.
