---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab Auditoría ISO 27001 — Parte 2 (Plan de Tratamiento)

!!! danger "Solo para instructores"
    Este documento contiene la rúbrica completa con rangos de respuesta aceptables para el plan
    de tratamiento, criterios de evaluación por brecha, errores comunes y notas de timing.
    No mostrar a los alumnos. Visible en el repositorio GitHub pero excluido del nav público.

---

## Rúbrica de Evaluación del Plan de Tratamiento

La rúbrica evalúa la calidad del plan de tratamiento para cada brecha (D-09). No hay una respuesta única: la priorización y las acciones correctivas admiten variación razonada. Lo que se evalúa es si el alumno aplica correctamente el proceso ISO 27005.

**Puntaje total sugerido: 20 puntos** — dividido en cuatro áreas evaluadas:

| Área | Descripción | Puntaje máximo |
|------|-------------|----------------|
| Informe de brechas | Brecha identificada con descripción clara e impacto potencial | 6 pts (3 brechas × 2 pts) |
| Priorización | Prioridad asignada correctamente según criterios dados | 4 pts |
| Plan de tratamiento | Acciones correctivas con responsable y plazo por brecha alta/media | 8 pts (4 brechas × 2 pts) |
| Párrafo de conclusión | Identifica cantidad de brechas y argumenta la más crítica | 2 pts |

---

### Criterios para el Informe de Brechas

| Brecha | Plan de tratamiento aceptable (2 pts) | Parcialmente aceptable (1 pt) | Insuficiente (0 pts) |
|--------|--------------------------------------|-------------------------------|----------------------|
| § 5.1 Sin política de seguridad | Acción: redactar política; Responsable: Jefe de Comunicaciones u Oficial de Seguridad; Plazo: 30-60 días; Prioridad: Alta (cualquier argumento razonado que ligue política a control de comportamiento organizacional) | Menciona "redactar política" sin responsable ni plazo concreto | Solo dice "cumplir con el control" o "implementar el requisito" sin especificar acción |
| § 5.15 / § 8.5 Cuentas genéricas | Acción: crear cuentas nominales y desactivar cuenta genérica; Responsable: Técnico TI; Plazo: 7-30 días; Prioridad: Alta (acceso no trazable = riesgo crítico) | Menciona cambiar contraseñas sin eliminar cuentas genéricas | Solo dice "mejorar contraseñas" sin crear cuentas nominales |
| § 8.15 Sin logging | Acción: activar logging en servidor de archivos con retención; Responsable: Técnico TI; Plazo: 7-15 días; Prioridad: Alta (sin logs no hay trazabilidad de incidentes) | Menciona "activar logs" sin especificar el servidor de archivos ni la retención | Solo dice "implementar monitoreo" genérico |
| § 5.24 Sin procedimiento de incidentes | Acción: redactar procedimiento de reporte e indicar canal (quién recibe, cómo contener); Responsable: Jefe de Comunicaciones; Plazo: 30 días; Prioridad: Alta (sin procedimiento el próximo incidente se pierde igual) | Menciona "crear procedimiento" sin definir canal de reporte | Solo dice "gestionar mejor los incidentes" |
| § 8.8 Actualizaciones deshabilitadas | Acción: programar ventana de mantenimiento mensual fuera de horario operacional; Responsable: Técnico TI; Plazo: 15-30 días; Prioridad: Media (riesgo existe pero hay mitigación parcial por Defender activo) | Menciona "habilitar actualizaciones" sin gestionar el conflicto con operaciones | Solo dice "activar actualizaciones automáticas" (ignora el contexto operacional) |
| § 6.3 Sin capacitación periódica | Acción: planificar capacitación trimestral con registro; Responsable: Jefe de Comunicaciones; Plazo: 60 días; Prioridad: Media | Menciona "dar capacitación" sin indicar frecuencia ni registro | Solo dice "capacitar al personal" |

---

### Criterios para la Priorización

La priorización es correcta si el alumno puede articular el razonamiento. Se acepta variación siempre que esté justificada:

| Prioridad esperada | Brecha | Razonamiento mínimo aceptable |
|-------------------|--------|-------------------------------|
| **Alta** | § 5.1 (política), § 5.15/8.5 (cuentas), § 8.15 (logging), § 5.24 (incidentes) | Exposición directa a pérdida de trazabilidad, acceso no autorizado o falla operacional sin capacidad de respuesta |
| **Media** | § 6.3 (capacitación), § 8.8 (actualizaciones), § 5.9 (inventario), § 5.12 (clasificación) | Riesgo real pero no implica exposición inmediata; puede tolerarse 30-90 días con atención activa |
| **Baja** | § 5.37 (procedimientos), § 8.9 (config documentada), § 5.30 (continuidad) | Carencia de madurez organizacional sin impacto técnico directo inmediato |

!!! note ""
    Si un alumno argumenta que § 8.8 (actualizaciones) es Alta porque "los sistemas C2 son críticos y sin parches están expuestos a exploits conocidos", aceptar como correcto — el razonamiento es válido aunque difiera de la clasificación sugerida.

---

### Criterios para el Párrafo de Conclusión

| Nivel | Criterio |
|-------|---------|
| **Completo (2 pts)** | Indica el número total de brechas (o un rango razonado), identifica la brecha más crítica y argumenta por qué con al menos una razón concreta (ej. "las cuentas genéricas son las más críticas porque hacen imposible auditar quién accedió a los sistemas C2") |
| **Parcial (1 pt)** | Identifica la brecha más crítica pero sin argumentar el impacto operacional |
| **Insuficiente (0 pts)** | Solo enumera brechas sin concluir o escribe "la JC-BC3 tiene muchos problemas" sin especificar |

---

## Errores Comunes y Cómo Manejarlos en Clase

| Error frecuente | Causa probable | Qué decirle al alumno |
|-----------------|---------------|----------------------|
| Acción correctiva demasiado vaga ("mejorar la seguridad") | El alumno no traduce el control ISO a una acción concreta | "Una acción debe responder: ¿qué exactamente? ¿quién lo hace? ¿para cuándo? Sin esas tres respuestas, el plan no es ejecutable." |
| Todas las brechas marcadas como Alta | El alumno no diferencia urgencia | "Si todo es urgente, nada es urgente. ¿Cuáles son las que podrían causar un incidente activo esta semana? Esas son Alta." |
| Asignar el mismo responsable a todas las acciones (siempre "el Técnico TI") | El alumno no reconoce que algunas acciones son de gestión, no técnicas | "La política de seguridad la aprueba la dirección, no el técnico TI. ¿Quién es la dirección en la JC-BC3?" |
| Plazo de "1 día" para todo | El alumno quiere ser exhaustivo | "Un plazo realista considera que la persona tiene otras responsabilidades. ¿Cuánto tiempo tomaría redactar una política de 1 página en una unidad militar real?" |
| Omitir brechas de prioridad Media del plan | El alumno solo trata las Alta | "El plan de tratamiento cubre todas las brechas que no se aceptan. ¿Han decidido aceptar el riesgo de las Media? Si no, deben planificarlas aunque sea con un plazo más largo." |

---

## Notas de Dictado (timing sugerido LAB-07: 3 horas)

| Bloque | Actividad | Tiempo |
|--------|-----------|--------|
| Apertura y recapitulación | Revisar resultados del checklist de Parte 1; el instructor pregunta cuántas brechas encontró cada pareja y las anota en pizarra | 20 min |
| Intro a priorización | Explicar criterios Alta/Media/Baja con un ejemplo: § 5.1 (política) vs. § 5.37 (procedimientos operacionales) | 10 min |
| Trabajo en parejas — informe de brechas | Alumnos completan el informe de brechas con descripción de impacto y prioridad para sus brechas identificadas | 40 min |
| Pausa de revisión | El instructor pide a dos parejas presentar 2-3 brechas con su priorización; clase discute divergencias | 15 min |
| Trabajo en parejas — plan de tratamiento | Alumnos completan el plan para las brechas Alta y Media; el instructor orienta sobre responsables y plazos | 45 min |
| Párrafo de conclusión | Alumnos escriben el párrafo de conclusión y el recuento final | 15 min |
| Cierre y debrief | El instructor presenta el plan de referencia (esta rúbrica); discute las diferencias más frecuentes | 15 min |
| **Total** | | **160 min (~3 hrs)** |

!!! tip "Consejo de dictado"
    En el bloque de revisión, buscar deliberadamente una pareja que haya marcado § 8.8 como Alta y otra que la haya marcado como Media. Usar esa divergencia para demostrar que la priorización depende de la argumentación, no de una respuesta única. Esto refuerza el mensaje de D-09 (rúbrica con rangos, no respuesta única).
