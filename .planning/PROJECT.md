# Curso de Ciberdefensa — Módulo 4: Defensiva

## What This Is

Repositorio de curso de ciberdefensa orientado a Técnicos y Sub-Oficiales del Ejército, cubriendo el Módulo 4 "Defensiva" (91 horas). El contenido se publica como GitHub Pages con navegación lateral tipo documentación técnica, con páginas en Markdown para cada clase y laboratorios separados para temas técnicos. Los ejercicios CTF usan la plataforma OFFen como complemento a labs autónomos.

## Core Value

Cada subtema tiene contenido listo para dictar, respetando exactamente las horas asignadas, con laboratorios implementables en Windows 11 sin infraestructura adicional.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Sitio GitHub Pages funcional con navegación lateral por asignatura/unidad/clase
- [ ] Contenido teórico en Markdown para todos los subtemas (91 horas, Módulo 4)
- [ ] Laboratorios técnicos con enunciado (alumno) y solución separada (instructor)
- [ ] Integración OFFen: labs autónomos + referencia al ejercicio CTF complementario
- [ ] Subtemas técnicos con lab; subtemas no-técnicos sin lab
- [ ] Exámenes parciales y finales alineados al temario
- [ ] Repositorio bajo cuenta ollerenac con .gitignore apropiado

### Out of Scope

- Módulos 1, 2, 3 del programa completo (419 hrs) — solo Módulo 4 en este sprint
- Sistema de evaluación automática o plataforma LMS — solo contenido estático
- Versión en inglés — español con tecnicismos en inglés según contexto
- Slides/presentaciones separadas — Markdown es el formato principal

## Context

**Audiencia:** Técnicos y Sub-Oficiales con nivel básico en TI (Windows, redes a nivel usuario, sin experiencia previa en seguridad formal).

**Plataforma de labs:** OFFen wargame (accesible desde Windows 11 de cada alumno). Labs autónomos primero, OFFen como complemento.

**Publicación:** GitHub Pages bajo cuenta `ollerenac` (email: ollerenac@uni.pe). Estructura navegable estilo MkDocs/Docusaurus.

**Edición:** Todo el contenido en archivos .md — el instructor puede modificar directamente en GitHub.

**Tiempo:** 91 horas totales distribuidas en 4 asignaturas. El número de horas por subtema es un hard constraint; no acarrear tiempo.

**Estructura del Módulo 4:**
- Seguridad de la Información y Criptografía: 32 horas
- Capas SI: 48 horas
- Gestión de Riesgos y Vulnerabilidades: 8 horas
- Conferencias Magistrales: 3 horas

## Constraints

- **Tiempo**: Cada subtema debe caber exactamente en sus horas asignadas — sin desborde
- **Plataforma**: Windows 11 para alumnos; labs deben funcionar en esa plataforma
- **Git**: Los commits deben hacerse desde la cuenta `ollerenac`; ningún archivo de Claude/GSD en el repo público
- **Idioma**: Español. Términos técnicos (comandos, nombres de herramientas) en inglés según corresponda
- **Formato**: Solo Markdown — no PDFs, no DOCX, no slides propietarias
- **Nivel**: Contenido calibrado para básico en TI — no asumir conocimiento previo de seguridad

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| MkDocs para GitHub Pages | Navegación lateral nativa, temas Material, edición .md directa | — Pending |
| Labs separados (alumno / instructor) | Evita que el alumno vea respuestas; instructor tiene guía completa | — Pending |
| OFFen como complemento (no pivote) | Si OFFen falla, el lab sigue funcionando autónomamente | — Pending |
| Un .md por clase/sesión | Granularidad alineada con horas asignadas; fácil de editar | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd:complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-06-15 after initialization*
