---
phase: 04-gestion-riesgos-conferencias
reviewed: 2026-06-21T08:32:00-05:00
depth: standard
files_reviewed: 7
files_reviewed_list:
  - docs/gestion-riesgos/fundamentos-riesgo/concepto-riesgo.md
  - docs/gestion-riesgos/fundamentos-riesgo/tipos-riesgo-militar.md
  - docs/gestion-riesgos/identificacion-vulnerabilidades/metodos-identificacion.md
  - docs/gestion-riesgos/mitigacion-controles/controles-buenas-practicas.md
  - docs/gestion-riesgos/mitigacion-controles/examen.md
  - docs/conferencias/responsabilidad-social/impacto-responsabilidad-social.md
  - docs/conferencias/operaciones-paz/operaciones-paz-globales.md
findings:
  critical: 2
  warning: 6
  info: 2
  total: 10
status: issues_found
---

# Phase 04: Code Review Report

**Reviewed:** 2026-06-21T08:32:00-05:00
**Depth:** standard
**Files Reviewed:** 7
**Status:** issues_found

## Summary

Reviewed all 7 content files produced by Phase 4 against the ROADMAP, plan must_haves, and acceptance criteria. The five theory pages (CONT-29 through CONT-32) and the two conference pages (CONT-33, CONT-34) deliver substantive, well-structured content that correctly replaces all stubs. The bridge from ISO 27005, the 4-category military risk taxonomy, the CVE/NVD workflow, the vulnerability report template, and both magistral conferences are all present and pedagogically appropriate for the NCO audience.

Two blockers concentrate in the exam file: the instructor answer key carries a scoring arithmetic error that makes Part A ungradable without editorial intervention, and the question count does not match the ROADMAP's "10 preguntas mixtas" specification in a student-visible way. The remaining six findings are warnings and informational items affecting consistency and quality.

No stub placeholders remain. No code blocks appear in either conference file. The `# Tipo: Conferencia` frontmatter is correctly set. CONT-34 word count (2,436) comfortably meets the 2,000–2,500 word target. `Ley N°29733` appears exactly once. The JC-BC3 fictional unit is used consistently throughout.

---

## Critical Issues

### CR-01: Exam Part A answer key states 15 graded cells but the matrix has 20 blank answer cells

**File:** `docs/gestion-riesgos/mitigacion-controles/examen.md:151`

**Issue:** The instructor answer key says "Criterio de corrección: 2 puntos por cada celda en blanco correctamente completada. Las filas tienen entre 3 y 4 celdas evaluables. Total: 15 celdas × 2 puntos = 30 puntos." However, the student-facing matrix has exactly 4 blank cells per row × 5 rows = 20 blank cells. All 20 cells are bolded in the key table (signaling they are expected answers). The arithmetic `15 × 2 = 30` cannot be reconciled with `20 × 2 = 40` without explicit editorial guidance on which cells are excluded. The column "Tratamiento propuesto" appears to be the 5 cells not counted — but neither the student instructions nor the scoring note say this. An instructor correcting this exam for the first time cannot determine: (a) which 5 cells to exclude, (b) what Tratamiento is worth if it is not part of the 30 pts, and (c) whether to award 1.5 pts per cell instead.

**Fix:** Choose one of two consistent approaches and update the answer key accordingly.

**Option A — Exclude Tratamiento from cell-point scoring (preferred, since Tratamiento is free-text):**
```
Criterio de corrección: 2 puntos por cada celda de contenido en blanco correctamente
completada (Activo/Amenaza/Vulnerabilidad, Probabilidad, Impacto, Nivel de Riesgo).
La columna "Tratamiento propuesto" se evalúa cualitativamente: respuesta razonada y
coherente con el nivel de riesgo = correcta; respuesta ausente o contradictoria = 0 pts.
Celdas de contenido evaluadas: 3 por fila × 5 filas = 15 celdas × 2 puntos = 30 puntos.
```

**Option B — Count all 20 cells at 1.5 pts each:**
```
Criterio de corrección: 1.5 puntos por cada celda en blanco correctamente completada.
Total: 20 celdas × 1.5 puntos = 30 puntos.
```

---

### CR-02: EXAM-05 has 7 student-visible questions, not the 10 required by the ROADMAP specification

**File:** `docs/gestion-riesgos/mitigacion-controles/examen.md:49,91`

**Issue:** The ROADMAP states "Matriz de riesgo completable (escenario dado) + 10 preguntas mixtas." The plan acceptance criterion (04-01-PLAN.md line 328) resolves this as "3 columnas evaluables de la matriz + 4 MCQ + 3 respuesta corta = 10 ítems evaluables," treating the matrix's three main column-types (Probabilidad, Impacto, Nivel de Riesgo) as three distinct question items. This counting logic exists only inside the plan document — it is entirely invisible to any instructor or student reading the exam. From the exam's perspective, Parte A is a single matrix task (not 3 questions), Parte B has 4 MCQ (P1–P4), and Parte C has 3 short-answer questions (P5–P7), totalling 7 student-visible questions. If an instructor is told "this exam has 10 questions" and opens the file, they count 7. This creates a credibility problem for the course material.

**Fix:** Add 3 more student-visible questions to Parte B or Parte C to reach an honest count of 10 numbered questions. Suggested additions for Parte B (10 pts each — adjust totals to maintain 100 pts or rebalance Parte A scoring):

```markdown
**P5 (nueva — Parte B MCQ).** El Técnico de la JC-BC3 identifica que las radios HF del
batallón usan firmware con una vulnerabilidad con CVSS 7.5 y sin parche disponible del
fabricante. ¿Cuál es el control compensatorio más adecuado?

- A) Dejar las radios fuera de servicio hasta que el parche esté disponible
- B) Reducir la exposición a frecuencias no operacionales y verificar integridad de firmware
   post-sesión mientras se espera el parche
- C) Actualizar manualmente el firmware descargando la versión del fabricante de internet
- D) Aceptar el riesgo sin documentación porque no hay solución disponible
```

Alternatively, renumber the exam parts and promote 3 of the existing Parte A matrix rows into individually-scored scenario questions. Either approach produces an exam whose stated "10 preguntas" count is visible and auditable by any reader.

---

## Warnings

### WR-01: CVSS Low band lower bound inconsistently defined as 0–3.9 vs 0.1–3.9

**File:** `docs/gestion-riesgos/identificacion-vulnerabilidades/metodos-identificacion.md:71,129,163`

**Issue:** The main severity table (line 71) correctly states `Low | 0.1 – 3.9`, matching CVSS v3.1 specification (FIRST.org). However, the asset-criticality decision table (line 129) labels the band `Low (0–3.9)`, and the Resumen bullet (line 163) also says `Low (0–3.9)`. A CVSS score of 0.0 is classified as "None" (no vulnerability), not "Low." Telling students the Low band starts at 0 is technically incorrect. In practice a 0.0 score is rare, but students who look up a CVE with CVSS 0.0 on NVD would see "None" rather than "Low," creating a teachable confusion.

**Fix:** Update lines 129 and 163 to use `0.1–3.9` consistently:

Line 129: `| **Low (0.1–3.9)** | Documentar...`
Line 163: `- El **score CVSS v3.1** tiene cuatro bandas: Critical (9–10), High (7–8.9), Medium (4–6.9), Low (0.1–3.9).`

---

### WR-02: Mixed language for CVSS severity bands in CONT-31 causes student confusion at NVD

**File:** `docs/gestion-riesgos/identificacion-vulnerabilidades/metodos-identificacion.md:38,45,52`

**Issue:** The CVE example block header (line 38) correctly uses the English band name as it appears on NVD: `CVSS Base Score: 8.8 (High)`. But the Puntuación CVSS table field (line 45) says `8.8 — **Alta** → requiere acción en 7 días o menos` and the four-step procedure (line 52) says `8.8 = Alta → plazo de remediación: dentro de 7 días`. Students are taught that 8.8 = "Alta" (Spanish) but when they visit nvd.nist.gov they will see "HIGH" (English, in the NVD interface). The main severity table uses the English labels (Critical/High/Medium/Low). The CVE example switches to Spanish without a bridging note. A student using the exam may write "Alta" where an instructor marking against NVD expects "High," or vice versa.

**Fix:** Align the CVE example table and the four-step procedure with the English labels used in the official severity table, and add a parenthetical translation on first use:

Line 45: `| **Puntuación CVSS** | 8.8 — **High (Alta)** → requiere acción en 7 días o menos |`
Line 52: `3. **Prioriza por score:** 8.8 = High (Alta) → plazo de remediación: dentro de 7 días.`

---

### WR-03: Code fence blocks used for non-code prose content in CONT-32

**File:** `docs/gestion-riesgos/mitigacion-controles/controles-buenas-practicas.md:79–98,130–136`

**Issue:** Two code fence blocks (` ``` `) are used for purely narrative/prose content: the four-step reporting chain (lines 79–98) and a sample compensatory-control text (lines 130–136). These are not code, commands, file paths, or data structures. Using code blocks for prose is a MkDocs anti-pattern: the monospaced font and grey background signal "technical artifact" to readers, and the content will be rendered without word-wrap on small screens. The plan for CONT-32 does not prohibit code blocks in theory pages, but this applies the wrong semantic container for organizational prose.

**Fix:** Convert both code blocks to a numbered list and a blockquote/admonition, respectively.

For the reporting chain (lines 79–98), replace the code block with a numbered prose list:
```markdown
1. **El Técnico detecta la vulnerabilidad** — completa el reporte y lo entrega al Jefe
   inmediato dentro del plazo según severidad.
2. **El Jefe inmediato evalúa** — autoriza la acción si está dentro de su autoridad y
   recursos; escala al Oficial de Seguridad si la severidad es Critical o High.
3. **El Oficial de Seguridad decide** — emite resolución escrita (mitigar / aceptar / escalar).
4. **El Técnico implementa y cierra** — aplica la remediación, documenta el resultado.
```

For the compensatory control sample (lines 130–136), replace with a `!!! example` admonition:
```markdown
!!! example "Ejemplo de control compensatorio para el campo 'Control compensatorio'"
    Control compensatorio: Hasta la publicación del parche por el fabricante, aplicar:
    (1) Restricción de uso a frecuencias operacionales autorizadas únicamente.
    (2) Verificación de integridad de firmware post-sesión según manual técnico.
    (3) Radio de respaldo designada como contingencia si se detecta comportamiento anómalo.
    Referencia: reporte VUL-2024-007, 2024-03-15.
```

---

### WR-04: Duplicate horizontal rule (`---`) in CONT-34 creates empty visual gap

**File:** `docs/conferencias/operaciones-paz/operaciones-paz-globales.md:75–77`

**Issue:** Lines 75 and 77 both contain `---` (horizontal rule), producing two consecutive dividers with a blank line between them. This renders as two stacked rules in MkDocs Material, creating an unexpected visual break between the end of `## Clasificación de Información en Coalición` and the start of `## Aplicación en Contexto Castrense`. The second rule is a leftover artifact (likely from a copy-paste during section assembly) and adds no structural or semantic value.

**Fix:** Delete line 77 (the second `---`) so only one horizontal rule separates the sections.

---

### WR-05: CONT-30 exceeds the section-density guideline for a 2-hour class

**File:** `docs/gestion-riesgos/fundamentos-riesgo/tipos-riesgo-militar.md:10–160`

**Issue:** CONT-30 has 7 content `##` sections (Por Qué el Contexto Militar Es Diferente + 4 Categorías + Interacción entre Categorías + Aplicación en Contexto Castrense), plus Objetivo, Resumen, and Para profundizar — 10 `##` sections total. The ARCHITECTURE.md density rule is 2–3 major sections per hour, giving a target of 4–6 content sections for 2 hours. The "Interacción entre Categorías" section, while pedagogically valuable, pushes the page beyond the density target by one section. In a real 2-hour class, covering 7 content sections plus the objective and summary leaves less than 15 minutes per section, which is tight for NCO-level instruction with practical examples.

**Fix:** Fold "## Interacción entre Categorías" (lines 114–126) into the closing paragraphs of "## Categoría 4 — Riesgos Humanos" as a transitional paragraph, or move it into "## Aplicación en Contexto Castrense" as a bridge before the classification exercise. Either approach preserves the content while returning the page to 6 content sections.

---

### WR-06: CONT-32 exceeds the section-density guideline for a 1-hour class

**File:** `docs/gestion-riesgos/mitigacion-controles/controles-buenas-practicas.md:10–160`

**Issue:** CONT-32 (1 hour) has 4 content `##` sections (Los Tres Tipos de Controles, La Plantilla de Reporte, La Cadena de Reporte Militar, Aplicación en Contexto Castrense). The ARCHITECTURE.md density rule for 1-hour classes is 2–3 major sections. With 4 sections and extensive subsections (three `###` under each `##`), the page represents roughly 90–100 minutes of actual delivery time for an NCO audience. Given that this page immediately precedes the exam, overrunning the time slot is operationally risky.

**Fix:** Merge "## La Plantilla de Reporte de Vulnerabilidad" and "## La Cadena de Reporte Militar" into a single section — for example "## El Reporte de Vulnerabilidad: Plantilla y Cadena" — since both sections describe the same artifact from two angles (what to write vs. who receives it). This reduces the page to 3 content sections, matching the 1-hour density target.

---

## Info

### IN-01: CONT-29 uses a fenced code block for a single-line ASCII formula

**File:** `docs/gestion-riesgos/fundamentos-riesgo/concepto-riesgo.md:56–58`

**Issue:** The formula `Amenaza + Vulnerabilidad → RIESGO → Impacto` is wrapped in a fenced code block. This works visually but applies monospaced code styling to a conceptual diagram. For a single-line text diagram, an indented blockquote (`> `) or inline bold text would be more semantically appropriate and would render more naturally in the course theme.

**Fix:** Replace the code block with an indented blockquote or inline emphasis:
```markdown
> **Amenaza + Vulnerabilidad → RIESGO → Impacto**
```

---

### IN-02: CONT-33 subsection structure conflates three conceptually distinct areas under one `##` heading

**File:** `docs/conferencias/responsabilidad-social/impacto-responsabilidad-social.md:16–43`

**Issue:** The plan specified three distinct content areas: (1) El Técnico como Actor Social, (2) Ética Profesional y Uso Responsable de Herramientas, and (3) Datos Personales y Marco Legal Peruano. All three are delivered inside the single section `## El Técnico como Actor Social: Ética, Datos y Responsabilidad Profesional`. The plan's acceptance criteria permit this ("2-3 secciones de nivel ##" is satisfied with Objetivo + this section + Aplicación = 3 sections), so this is not a plan violation. However, for a 1-hour lecture, an instructor using the page as a delivery guide benefits from clearer signposting. The three topics — custodianship of others' data, ethics of dual-use tools, and the legal framework — address different aspects and warrant visible breaks.

**Fix (optional):** Add two `###` subheadings within the single `##` to break the content into scannable segments:

```markdown
## El Técnico como Actor Social: Ética, Datos y Responsabilidad Profesional

### El custodio de datos que no son suyos

[first two paragraphs + !!! example scenario]

### El uso ético de herramientas de doble filo

[ethics paragraphs + !!! note definition]

### Datos personales y el marco legal peruano

[Ley N°29733 paragraph + three concrete behaviors]
```

This preserves the 3-section `##` structure while giving the instructor a clear progression through the 1-hour lecture.

---

_Reviewed: 2026-06-21T08:32:00-05:00_
_Reviewer: Claude (gsd-code-reviewer)_
_Depth: standard_
