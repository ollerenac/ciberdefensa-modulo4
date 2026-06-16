---
# Horas asignadas: 1 hr
# Tipo: Examen
---

# Examen Parcial — Capas SI (Familia ISO 27000)

> **Duración:** 60 minutos | **Preguntas:** 15 | **Valor:** 15 puntos (1 punto por pregunta)

!!! warning "Instrucciones"
    - Responder de forma individual. No consultar apuntes, libros ni dispositivos electrónicos.
    - Para las preguntas de opción múltiple (Sección A), marcar **una sola respuesta** por pregunta.
    - Para las preguntas de Sección B, trazar una línea o escribir la letra que corresponde a cada norma.
    - Para las preguntas de respuesta corta (Sección C), escribir en el espacio indicado.
    - Escribir nombre completo y número de identificación antes de comenzar.
    - Al terminar, entregar la hoja de respuestas al instructor. Conservar el enunciado.

---

## Sección A: Opción Múltiple (Preguntas 1-5)

*Cada pregunta vale 1 punto. Marcar una sola respuesta.*

---

**P1.** ¿Cuál es la principal diferencia entre ISO 27001 e ISO 27002?

- (A) ISO 27001 es una guía voluntaria de buenas prácticas; ISO 27002 es un estándar auditable
- (B) ISO 27001 especifica los requisitos del SGSI (auditable); ISO 27002 proporciona guía de implementación de controles
- (C) ISO 27001 cubre gestión de riesgos; ISO 27002 cubre gestión de incidentes
- (D) ISO 27001 aplica solo a organizaciones militares; ISO 27002 aplica a organizaciones civiles

---

**P2.** Una organización acaba de finalizar un ciclo completo PDCA de su SGSI. En la fase "Check" (Verificar), ¿qué actividad es la más representativa?

- (A) Redactar la política de seguridad y obtener la aprobación de la dirección
- (B) Implementar los controles seleccionados en el plan de tratamiento de riesgos
- (C) Realizar una auditoría interna y una revisión por la dirección del SGSI
- (D) Identificar y registrar todos los activos de información de la organización

---

**P3.** ISO 27002:2022 organiza sus controles en cuatro temas. ¿Cuál de las siguientes listas corresponde correctamente a esos cuatro temas?

- (A) Prevención, Detección, Respuesta, Recuperación
- (B) Organizacional, Personas, Físico, Tecnológico
- (C) Confidencialidad, Integridad, Disponibilidad, Trazabilidad
- (D) Planificación, Implementación, Monitoreo, Mejora

---

**P4.** En el proceso de gestión de riesgos según ISO 27005, ¿qué significa la opción de tratamiento "Transferir"?

- (A) Reducir la probabilidad o el impacto mediante la implementación de controles
- (B) Aceptar el riesgo documentado dentro del apetito de riesgo de la organización
- (C) Delegar el riesgo a un tercero mediante seguro o contrato de externalización
- (D) Eliminar la actividad que genera el riesgo para que no pueda materializarse

---

**P5.** ¿Qué es el SoA (Statement of Applicability) en el contexto de ISO 27001?

- (A) Un registro de todos los incidentes de seguridad ocurridos en el año
- (B) Un documento que indica qué controles del Anexo A aplican a la organización y cuáles se excluyen, con justificación
- (C) El certificado emitido por el organismo de certificación tras la auditoría exitosa
- (D) El inventario completo de activos de información clasificados por valor

---

## Caso Práctico

*Leer antes de responder la Sección B.*

La **Sección de Comunicaciones del Regimiento de Infantería N°8** (SC-RI8) es una unidad de 15 personas que opera equipos de radio, terminales de sistema C2 y un servidor de archivos compartidos. Durante una evaluación interna se identificaron las siguientes situaciones:

- No existe documento de política de seguridad aprobado por el Comandante.
- El inventario de activos (radios, terminales, servidores) está desactualizado en 6 meses.
- Las contraseñas del servidor de archivos son genéricas y compartidas entre todo el personal.
- El personal no ha recibido capacitación en seguridad en los últimos 12 meses.
- Windows Defender está activo, pero las actualizaciones automáticas están deshabilitadas.
- No existe procedimiento para reportar incidentes de seguridad.

---

## Sección B: Asociación de Conceptos (Preguntas 6-11)

*Las preguntas 6-9 se responden usando el caso práctico anterior. Las preguntas 10-11 son conceptuales. Trazar una línea o escribir la letra que corresponde. Cada respuesta correcta vale 1 punto.*

---

**P6.** Asociar cada situación de la SC-RI8 con el control ISO 27001:2022 que incumple:

| Situación en la SC-RI8 | Control ISO 27001:2022 |
|------------------------|------------------------|
| Sin política de seguridad aprobada | ___ |
| Inventario de activos desactualizado | ___ |
| Contraseñas genéricas compartidas | ___ |
| Sin capacitación en seguridad | ___ |

**Opciones:**
- A) § 6.3 — Concienciación, educación y formación
- B) § 5.1 — Política de seguridad de la información
- C) § 8.5 — Autenticación segura
- D) § 5.9 — Inventario de activos de información

---

**P7.** Considerando que Windows Defender está activo pero las actualizaciones están deshabilitadas, ¿cuál es el estado de cumplimiento más correcto para el control § 8.7 (Protección contra malware)?

- A) Cumple — porque Windows Defender está activo
- B) No cumple — porque no hay ninguna protección antimalware
- C) Parcial — porque hay protección activa pero no actualizada
- D) No aplica — porque los sistemas militares no requieren antimalware

---

**P8.** La SC-RI8 no tiene procedimiento de reporte de incidentes. ¿Qué cláusula ISO 27001:2022 aborda específicamente este requisito?

- A) § 5.12 — Clasificación de la información
- B) § 5.24 — Planificación y preparación de la gestión de incidentes
- C) § 7.1 — Seguridad física perimetral
- D) § 8.20 — Seguridad de redes

---

**P9.** Si el Comandante del RI-8 decide que el riesgo derivado de las actualizaciones deshabilitadas es aceptable porque "las operaciones no pueden interrumpirse", ¿qué opción de tratamiento de riesgo ISO 27005 está aplicando?

- A) Mitigar (Modify)
- B) Transferir (Share)
- C) Aceptar (Retain)
- D) Evitar (Avoid)

---

**P10.** Asociar cada norma de la familia ISO 27000 con su propósito principal:

| Norma | Propósito |
|-------|-----------|
| ISO/IEC 27000 | ___ |
| ISO/IEC 27001 | ___ |
| ISO/IEC 27005 | ___ |

**Opciones:**
- A) Establece los requisitos auditables para un SGSI
- B) Proporciona vocabulario y definiciones de la familia de normas
- C) Define el proceso de gestión de riesgos de seguridad de la información

---

**P11.** ¿En cuál de las cuatro cláusulas de ISO 27002:2022 se encontraría el control de "gestión de incidentes de seguridad de la información" (§ 5.24)?

- A) Tema 5 — Controles organizacionales
- B) Tema 6 — Controles de personas
- C) Tema 7 — Controles físicos
- D) Tema 8 — Controles tecnológicos

---

## Sección C: Respuesta Corta (Preguntas 12-15)

*Cada pregunta vale 1 punto. Escribir la respuesta en el espacio indicado.*

---

**P12.** Explicar en una oración el propósito del ciclo PDCA aplicado a un SGSI. ¿Por qué un SGSI que no usa PDCA tiende a degradarse con el tiempo?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P13.** La SC-RI8 del caso práctico tiene contraseñas genéricas compartidas. Completar la siguiente fila de registro de riesgo según ISO 27005:

| Activo | Amenaza | Probabilidad (1-3) | Impacto (1-3) | Nivel de riesgo (P×I) | Tratamiento sugerido |
|--------|---------|-------------------|--------------|----------------------|----------------------|
| Servidor de archivos de la SC-RI8 | Acceso no autorizado a información clasificada por uso de credenciales genéricas | ______ | ______ | ______ | ______ |

*Justificar brevemente la probabilidad e impacto asignados:*

________________________________________________________________

________________________________________________________________

---

**P14.** Nombrar dos de los cuatro temas de controles de ISO 27002:2022 y dar un ejemplo de control concreto de cada uno aplicado a una unidad militar.

*Tema 1:* ____________________ *Ejemplo:* ________________________________________________________________

*Tema 2:* ____________________ *Ejemplo:* ________________________________________________________________

---

**P15.** ¿Qué es el "contexto de la organización" según la cláusula § 4 de ISO 27001, y por qué es el primer paso del SGSI antes de implementar cualquier control? Dar un ejemplo concreto aplicado a una Jefatura de Comunicaciones.

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

!!! danger "Solo instructor — Clave de Respuestas"

    ## Clave de Respuestas

    ### Sección A: Opción Múltiple

    | Pregunta | Respuesta | Justificación |
    |----------|-----------|---------------|
    | P1 | **B** | ISO 27001 es el estándar auditable (certificable); ISO 27002 es guía de implementación para sus controles |
    | P2 | **C** | "Check" = auditoría interna + revisión por la dirección. "A" es Plan, "B" es Do, "D" es parte de Plan |
    | P3 | **B** | Los cuatro temas de ISO 27002:2022 son: Organizacional (5), Personas (6), Físico (7), Tecnológico (8) |
    | P4 | **C** | "Transferir" = seguro o externalización. "A" = Mitigar; "B" = Aceptar; "D" = Evitar |
    | P5 | **B** | El SoA declara qué controles del Anexo A aplican, cuáles se excluyen y por qué — documento obligatorio de ISO 27001 |

    ---

    ### Sección B: Asociación de Conceptos

    **P6 — Asociación situación-control:**
    - Sin política de seguridad → **B** (§ 5.1)
    - Inventario desactualizado → **D** (§ 5.9)
    - Contraseñas genéricas → **C** (§ 8.5)
    - Sin capacitación → **A** (§ 6.3)

    *Criterio mínimo para el punto:* Las cuatro asociaciones correctas. Aceptar si el alumno solo comete un error de asociación (0.5 pt).

    **P7 — Cumplimiento § 8.7:** **C** (Parcial). Defender activo = existe protección; actualizaciones deshabilitadas = no actualizada. Marcar "Cumple" (A) o "No cumple" (B) sin matices = 0 pt.

    **P8 — Cláusula para procedimiento de incidentes:** **B** (§ 5.24). Las demás cláusulas no abordan la gestión de incidentes.

    **P9 — Opción de tratamiento:** **C** (Aceptar / Retain). El Comandante documenta y acepta el riesgo dentro de su apetito. No es Mitigar (no implementa control), no es Transferir (no hay tercero), no es Evitar (no elimina la actividad).

    **P10 — Asociación norma-propósito:**
    - ISO/IEC 27000 → **B** (vocabulario y definiciones)
    - ISO/IEC 27001 → **A** (requisitos auditables del SGSI)
    - ISO/IEC 27005 → **C** (gestión de riesgos)

    *Criterio mínimo:* Las tres correctas. Aceptar si el alumno solo comete un error (0.5 pt).

    **P11 — Tema de § 5.24:** **A** (Tema 5 — Controles organizacionales). La gestión de incidentes es un proceso organizacional. El número "5" en § 5.24 indica que pertenece al Tema 5.

    ---

    ### Sección C: Respuestas Modelo

    **P12 — PDCA y degradación del SGSI:**
    Respuesta modelo: "El ciclo PDCA asegura que el SGSI se planifique, implemente, verifique y mejore continuamente; sin él, los controles se vuelven obsoletos ante nuevas amenazas y el sistema pierde efectividad."
    *Criterio mínimo:* Mencionar mejora continua y/o que las amenazas evolucionan y el SGSI debe adaptarse. Aceptar cualquier formulación que capture la idea de ciclo iterativo.

    **P13 — Fila de registro de riesgo:**
    Respuesta de referencia (acepta variaciones razonadas):

    | Activo | Amenaza | Probabilidad | Impacto | Nivel | Tratamiento |
    |--------|---------|-------------|---------|-------|-------------|
    | Servidor de archivos SC-RI8 | Acceso no autorizado por credenciales genéricas | 3 (Alta — cualquiera con la contraseña puede acceder) | 3 (Alto — información clasificada comprometida) | 9 — CRÍTICO | Mitigar: crear cuentas nominales y desactivar cuenta genérica |

    *Criterio mínimo:* Probabilidad ≥ 2 (el riesgo es real con contraseña compartida), Impacto ≥ 2 (información de la unidad), Nivel = P×I calculado correctamente, Tratamiento diferente de "Aceptar". Variaciones razonadas: Probabilidad 2/Impacto 3 o similar = aceptar con justificación.

    **P14 — Dos temas ISO 27002 con ejemplo militar:**
    Aceptar cualquier dos de los cuatro temas con ejemplo que sea plausible en contexto militar. Ejemplos de referencia:
    - Organizacional: "§ 5.1 Política de seguridad — la Jefatura de Comunicaciones aprueba una directiva de uso de equipos tácticos"
    - Personas: "§ 6.3 Capacitación — charla trimestral sobre ciberseguridad para todo el personal de comunicaciones"
    - Físico: "§ 7.2 Control de acceso físico — registro de entrada a la sala del sistema C2"
    - Tecnológico: "§ 8.15 Logging — activar registro de acceso en el servidor de archivos de la unidad"

    *Criterio mínimo:* Nombre correcto del tema + ejemplo que mencione un activo militar reconocible (radio táctica, sistema C2, servidor de unidad, sala de equipos). No aceptar ejemplos genéricos sin contexto militar.

    **P15 — Contexto de la organización (§ 4):**
    Respuesta modelo: "El contexto de la organización (§ 4) significa identificar quién es la organización, qué hace y qué riesgos la rodean antes de diseñar controles. Es el primer paso porque sin entender el contexto, los controles pueden ser inadecuados — una pequeña Jefatura de Comunicaciones de batallón no necesita los mismos controles que un cuartel general de división. Ejemplo: la JC-BC3 define su contexto identificando misión (soporte de comunicaciones tácticas), activos críticos (radios HF/VHF, sistemas C2), partes interesadas (Comandante de batallón) y amenazas prioritarias (interceptación de señales, acceso físico no autorizado)."
    *Criterio mínimo:* Mencionar que el contexto determina qué controles aplican + dar un ejemplo con al menos un activo o amenaza del contexto castrense.

    ---

    ## Notas de Aplicación

    **Distribución del tiempo recomendada:**

    | Sección | Preguntas | Tiempo sugerido |
    |---------|-----------|----------------|
    | Lectura del caso práctico | — | 5 minutos |
    | Sección A | P1-P5 | 15 minutos |
    | Sección B | P6-P11 | 20 minutos |
    | Sección C | P12-P15 | 15 minutos |
    | Revisión final | — | 5 minutos |
    | **Total** | **15 preguntas** | **60 minutos** |
