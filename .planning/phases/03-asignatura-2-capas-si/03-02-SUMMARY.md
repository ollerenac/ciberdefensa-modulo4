---
phase: 03-asignatura-2-capas-si
plan: "02"
subsystem: content
tags: [iso27001, iso27002, iso27005, labs, examen, auditoria, rubrica]
dependency_graph:
  requires: []
  provides: [LAB-06, LAB-07, EXAM-03]
  affects: [03-03, 03-04]
tech_stack:
  added: []
  patterns: [checklist-lab-sin-comandos, rubrica-rangos-D09, examen-15-preguntas]
key_files:
  created: []
  modified:
    - docs/capas-si/familia-iso27000/lab-auditoria-p1.md
    - docs/capas-si/familia-iso27000/lab-auditoria-p2.md
    - docs/instructor/lab-auditoria-p1-solucion.md
    - docs/instructor/lab-auditoria-p2-solucion.md
    - docs/capas-si/familia-iso27000/examen-parcial.md
decisions:
  - "LAB-06 usa 20 controles ISO 27001:2022 (§5.1–§5.30) cubriendo organización, personas, físico y tecnológico"
  - "JC-BC3 tiene brechas deliberadas en: política, cuentas genéricas, logging, incidentes, actualizaciones"
  - "LAB-07 abre con recap de las brechas identificadas en Parte 1 para reforzar continuidad D-08"
  - "EXAM-03: 5 MCQ + caso práctico SC-RI8 + 6 asociación + 4 respuesta corta = 15 exactas"
  - "Sección B EXAM-03 referencia el caso práctico (patrón FEATURES.md §Asignatura 2 parcial)"
metrics:
  duration_minutes: 9
  completed_date: "2026-06-16"
  task_count: 3
  file_count: 5
---

# Phase 03 Plan 02: Labs de Auditoría ISO 27001 (LAB-06, LAB-07) y Examen Parcial (EXAM-03) Summary

**One-liner:** Taller de auditoría ISO 27001 en dos partes sobre la JC-BC3 (checklist + plan de tratamiento) con rúbricas de rango por control y examen parcial de 15 preguntas con caso práctico de la SC-RI8.

---

## What Was Built

### Task 1 — LAB-06 y LAB-07 (páginas alumno)

- **`docs/capas-si/familia-iso27000/lab-auditoria-p1.md`** (LAB-06, 2 hrs): Checklist de 20 controles ISO 27001:2022 para la Jefatura de Comunicaciones del Batallón de Comunicaciones N°3 (JC-BC3). El escenario describe 11 prácticas actuales de la unidad con brechas deliberadas (política verbal, cuentas genéricas, logging desactivado, sin procedimiento de incidentes). Formato checklist-en-pantalla + alumno escribe en papel — sin bloques de código ni comandos (D-06). Incluye `!!! tip` con recordatorio de conservar el checklist para Parte 2. Comentario HTML de referencia a la solución instructor.

- **`docs/capas-si/familia-iso27000/lab-auditoria-p2.md`** (LAB-07, 3 hrs): Continuación directa de LAB-06 con la misma unidad ficticia (D-07/D-08). Abre con recap explícito: "En la sesión anterior identificamos las brechas de cumplimiento en la JC-BC3". Incluye tabla de recuento de brechas, criterios de priorización (Alta/Media/Baja), tabla de informe de brechas y plan de tratamiento con 6 filas de ejemplo. Comentario HTML a solución instructor presente.

### Task 2 — Soluciones instructor LAB-06 y LAB-07

- **`docs/instructor/lab-auditoria-p1-solucion.md`**: Rúbrica de 20 controles con tres niveles por control (correcto / parcialmente aceptable / no cumple) con criterios concretos para cada nivel (D-09 — no respuesta modelo única). Incluye tabla de errores comunes y notas de dictado con timing sumando 100 minutos.

- **`docs/instructor/lab-auditoria-p2-solucion.md`**: Rúbrica del plan de tratamiento evaluando acción, responsable y plazo por brecha. Criterios de priorización con nota explícita de que el alumno puede discrepar si argumenta correctamente. Tabla de errores comunes y notas de dictado sumando 160 minutos (3 hrs).

### Task 3 — EXAM-03 (examen parcial)

- **`docs/capas-si/familia-iso27000/examen-parcial.md`** (EXAM-03, 1 hr): Examen de 15 preguntas exactas (P1–P15) distribuidas en 3 secciones. Sección A: 5 MCQ conceptuales (ISO 27001 vs 27002, PDCA, 4 temas ISO 27002, opciones de tratamiento ISO 27005, SoA). Caso práctico breve: Sección de Comunicaciones del Regimiento de Infantería N°8 con 6 situaciones reales. Sección B: 6 preguntas de asociación (4 referencian el caso práctico, 2 conceptuales). Sección C: 4 preguntas de respuesta corta incluyendo fila de registro de riesgo ISO 27005 y pregunta de contexto § 4 de ISO 27001. Clave completa embebida en `!!! danger "Solo instructor"`.

---

## Deviations from Plan

None — el plan se ejecutó exactamente como estaba escrito. Los 5 archivos coinciden con los especificados en el frontmatter del plan.

---

## Known Stubs

None — todos los archivos tienen contenido completo listo para publicar. No quedan marcadores de placeholder ni stubs.

---

## Threat Flags

No se identificaron nuevas superficies de amenaza no contempladas en el plan. Los archivos de instructor (`lab-auditoria-p1-solucion.md`, `lab-auditoria-p2-solucion.md`) siguen el patrón establecido en Fase 2: excluidos del nav pero accesibles vía URL directa (T-03-02 en el threat register del plan — disposition: accept).

---

## Self-Check

### Files exist:

- [x] `docs/capas-si/familia-iso27000/lab-auditoria-p1.md`
- [x] `docs/capas-si/familia-iso27000/lab-auditoria-p2.md`
- [x] `docs/instructor/lab-auditoria-p1-solucion.md`
- [x] `docs/instructor/lab-auditoria-p2-solucion.md`
- [x] `docs/capas-si/familia-iso27000/examen-parcial.md`

### Commits exist:

- [x] `b2c1c68` — feat(03-02): escribir LAB-06 y LAB-07 alumno (Task 1)
- [x] `7c2377e` — feat(03-02): escribir soluciones instructor LAB-06 y LAB-07 con rúbrica (Task 2)
- [x] `7673631` — feat(03-02): escribir EXAM-03 (Task 3)

### Key acceptance criteria:

- [x] `grep "Batallón de Comunicaciones N°3"` exits 0 en ambos labs (D-07)
- [x] Sin bloques bash/cmd/powershell en LAB-06 (D-06)
- [x] Rúbricas con 3 niveles de criterio en ambas soluciones instructor (D-09)
- [x] EXAM-03 tiene exactamente 15 preguntas (Pitfall 6)
- [x] `mkdocs build --strict` exits 0

## Self-Check: PASSED
