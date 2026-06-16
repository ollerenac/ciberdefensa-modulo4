---
phase: 03-asignatura-2-capas-si
plan: "01"
subsystem: content
tags: [mkdocs, markdown, iso27000, iso27001, iso27002, iso27005, sgsi, castrense]

requires:
  - phase: 02-asignatura-1-seguridad-criptografia
    provides: theory-page pattern, military-context section convention, MkDocs admonition style

provides:
  - 8 theory pages covering the ISO 27000 family (CONT-13..CONT-20)
  - iso27000-generalidades-p1.md / p2.md — origin of standards, SGSI structure
  - iso27001-requisitos-p1.md / p2.md — clause 4-10, PDCA, SoA
  - iso27002-controles-p1.md / p2.md — 93 controls in 4 themes, control selection
  - iso27005-riesgo-p1.md / p2.md — risk identification, evaluation, treatment

affects: [03-02, 03-03, 03-04]

tech-stack:
  added: []
  patterns:
    - "Theory page: objective block + conceptual body + Aplicación en Contexto Castrense section"
    - "MkDocs Material admonitions: !!! note, !!! warning, !!! tip"
    - "Military examples: JC-BC3, SCIF, C2, puesto de mando"

key-files:
  created:
    - docs/capas-si/familia-iso27000/iso27000-generalidades-p1.md
    - docs/capas-si/familia-iso27000/iso27000-generalidades-p2.md
    - docs/capas-si/familia-iso27000/iso27001-requisitos-p1.md
    - docs/capas-si/familia-iso27000/iso27001-requisitos-p2.md
    - docs/capas-si/familia-iso27000/iso27002-controles-p1.md
    - docs/capas-si/familia-iso27000/iso27002-controles-p2.md
    - docs/capas-si/familia-iso27000/iso27005-riesgo-p1.md
    - docs/capas-si/familia-iso27000/iso27005-riesgo-p2.md
  modified: []

key-decisions:
  - "Aplicación en Contexto Castrense section on every page uses JC-BC3 (Jefatura de Comunicaciones del Batallón de Comunicaciones N°3) as the recurring fictional unit"
  - "Qualitative risk matrix 3×3 (Probabilidad × Impacto) presented as the primary evaluation method for military context"
  - "ISO 27002 controls grouped by the 4 official themes (Organizacional, Personas, Físico, Tecnológico) rather than the old 14-domain structure"

patterns-established:
  - "Theory page structure: frontmatter horas/tipo → h1 title → objective block → conceptual body sections → Aplicación en Contexto Castrense"
  - "Recapitulación blockquote on p2 pages to bridge from p1 content"

requirements-completed: [CONT-13, CONT-14, CONT-15, CONT-16, CONT-17, CONT-18, CONT-19, CONT-20]

duration: ~15min
completed: 2026-06-16
---

# Plan 03-01: ISO 27000 Family Theory Pages Summary

**8 páginas teóricas de la familia ISO/IEC 27000 escritas con sección de aplicación castrense en cada una, cubriendo generalidades, ISO 27001, ISO 27002 e ISO 27005**

## Performance

- **Duration:** ~15 min
- **Started:** 2026-06-16T09:16:58Z
- **Completed:** 2026-06-16T14:05:00Z (session limit interruption — SUMMARY.md written in recovery)
- **Tasks:** 3/3
- **Files modified:** 8

## Accomplishments
- 8 theory pages reemplazando stubs de Fase 1 — todos con estructura completa (objetivo, cuerpo conceptual, aplicación castrense)
- Sección "Aplicación en Contexto Castrense" en todas las páginas usando JC-BC3 como unidad de referencia
- Cobertura completa de la familia ISO 27000: generalidades → gestión → controles → riesgo

## Task Commits

1. **Task 1: Generalidades ISO 27000 (CONT-13, CONT-14)** — `150edb1` (feat)
2. **Task 2: ISO 27001 requisitos y estructura ISO 27002 (CONT-15, CONT-16, CONT-17)** — `5667e32` (feat)
3. **Task 3: SoA y gestión riesgo ISO 27005 (CONT-18, CONT-19, CONT-20)** — `cb84d89` (feat)

## Files Created/Modified
- `docs/capas-si/familia-iso27000/iso27000-generalidades-p1.md` — Origen BS 7799, SGSI estructura (159 líneas)
- `docs/capas-si/familia-iso27000/iso27000-generalidades-p2.md` — Familia de normas, relaciones (132 líneas)
- `docs/capas-si/familia-iso27000/iso27001-requisitos-p1.md` — Cláusulas 4-10, PDCA (197 líneas)
- `docs/capas-si/familia-iso27000/iso27001-requisitos-p2.md` — SoA, ciclo mejora continua (189 líneas)
- `docs/capas-si/familia-iso27000/iso27002-controles-p1.md` — 93 controles en 4 temas (99 líneas)
- `docs/capas-si/familia-iso27000/iso27002-controles-p2.md` — Selección de controles, Annex A (159 líneas)
- `docs/capas-si/familia-iso27000/iso27005-riesgo-p1.md` — Proceso riesgo, activos CIA (212 líneas)
- `docs/capas-si/familia-iso27000/iso27005-riesgo-p2.md` — Matriz 3×3, tratamiento (164 líneas)

## Decisions Made
- Método cualitativo (matriz Probabilidad × Impacto 3×3) elegido como enfoque primario — más adecuado para contexto militar donde el costo no es la única métrica
- ISO 27002 presentado con estructura de 4 temas (2022) en lugar de los 14 dominios de la versión 2013 — alineado con la edición vigente

## Deviations from Plan
None — plan ejecutado según especificación. SUMMARY.md fue escrito en recuperación post-session-limit; todo el contenido fue committed antes de la interrupción.

## Issues Encountered
El agente ejecutor alcanzó el límite de sesión del proveedor antes de poder escribir y commitear SUMMARY.md. El contenido de las 8 páginas teóricas estaba completamente committed (3 commits). SUMMARY.md fue escrito manualmente por el orquestador en recuperación.

## Next Phase Readiness
- Páginas teóricas ISO 27000 listas para referencia desde labs (03-02) y examen (EXAM-03)
- Wave 2 (03-03 — Defensa en Profundidad) puede ejecutarse una vez mergeada esta wave
- Sin bloqueadores

---
*Phase: 03-asignatura-2-capas-si*
*Completed: 2026-06-16*
