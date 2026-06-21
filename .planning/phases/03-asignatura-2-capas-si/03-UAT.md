---
status: complete
phase: 03-asignatura-2-capas-si
source:
  - .planning/phases/03-asignatura-2-capas-si/03-01-SUMMARY.md
  - .planning/phases/03-asignatura-2-capas-si/03-02-SUMMARY.md
  - .planning/phases/03-asignatura-2-capas-si/03-03-SUMMARY.md
  - .planning/phases/03-asignatura-2-capas-si/03-04-SUMMARY.md
started: 2026-06-21T06:55:00-05:00
updated: 2026-06-21T06:55:00-05:00
---

## Current Test

[testing complete]

## Tests

### 1. ISO 27000 Theory Pages — File Completeness (CONT-13..20)
expected: 8 theory pages exist, all between 99–212 lines
result: pass
method: auto-verified
notes: iso27000-gen p1(159) p2(132) · iso27001-req p1(197) p2(189) · iso27002-ctrl p1(99) p2(159) · iso27005-risk p1(212) p2(164)

### 2. Aplicación en Contexto Castrense — All 8 ISO Pages
expected: Every ISO theory page has a ## Aplicación en Contexto Castrense section
result: pass
method: auto-verified
notes: grep confirmed 8/8 pages have the heading

### 3. EXAM-03 Question Count
expected: Exactly 15 questions labelled P1..P15 in examen-parcial.md
result: pass
method: auto-verified
notes: grep -o P[0-9]+ | sort -u returned P1–P15 (15 total)

### 4. EXAM-03 Answer Key Present
expected: Instructor answer key embedded in examen-parcial.md inside a danger admonition
result: pass
method: auto-verified
notes: 8 matches for danger/Solo instructor/Clave keywords

### 5. Defensa en Profundidad Theory Pages — File Completeness (CONT-21..28)
expected: 8 theory pages exist, all substantive (>100 lines each)
result: pass
method: auto-verified
notes: perimetral p1(128) p2(214) · red-endpoint p1(194) p2(105) · apps-datos p1(133) p2(141) · activos-criticos p1(131) p2(280)

### 6. Tabletop Exercise Structure (CONT-28)
expected: Exactly 3 role cards (!!! info "Tarjeta de Rol"), exactly 4 inject cards (!!! example "Inject"), WannaCry/SMBv1 referenced
result: pass
method: auto-verified
notes: 3 role cards ✓ · 4 inject cards ✓ · WannaCry: 2 mentions ✓

### 7. EXAM-04 Question Count
expected: Exactly 15 questions labelled P1..P15 in examen-final.md
result: pass
method: auto-verified
notes: grep -o P[0-9]+ | sort -u returned P1–P15 (15 total)

### 8. EXAM-04 ASCII Network Scenario
expected: examen-final.md contains a JC-BC3 ASCII network map with intentional security gaps
result: pass
method: auto-verified
notes: 25 hits for JC-BC3/VLAN/Firewall/DMZ keywords

### 9. Sequential Navigation — ISO Unit
expected: iso27005-riesgo-p2.md "Continúa en" link points to lab-auditoria-p1.md (not directly to examen-parcial.md)
result: pass
method: auto-verified
notes: CR-02 fix confirmed: "Continúa en: [Lab: Taller de Auditoría ISO 27001 — Parte 1](lab-auditoria-p1.md)"

### 10. Instructor Solutions — Both Present with Rubrics
expected: Both lab-auditoria-p1-solucion.md and lab-auditoria-p2-solucion.md have multi-level rubrics (Cumple/Parcial/No cumple)
result: pass
method: auto-verified
notes: LAB-06 sol 69 lines · LAB-07 sol 94 lines · 26 rubric-level keyword matches in LAB-06 sol

### 11. Theory Page Clarity (ISO 27001)
expected: |
  iso27001-requisitos-p1.md opens with a clear objective block, a clause summary table
  with one-liner descriptions, and military framing — readable by a non-specialist soldier
result: pass

### 12. Lab Self-Sufficiency (LAB-06)
expected: |
  lab-auditoria-p1.md provides enough scenario context that a student pair can work
  independently for 2 hours — the JC-BC3 scenario, checklist, and delivery instructions
  are complete without requiring the instructor to fill gaps
result: pass

### 13. Exam Difficulty Calibration (EXAM-04)
expected: |
  EXAM-04 scenario (ASCII network map + 5 listed threats) is appropriately complex for
  a 48-hour Capas SI course — challenging but solvable within 60 minutes using concepts
  from CONT-21..28
result: pass

## Summary

total: 13
passed: 13
issues: 0
pending: 0
skipped: 0

## Gaps

[none yet]
