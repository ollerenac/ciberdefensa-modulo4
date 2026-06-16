---
phase: 03-asignatura-2-capas-si
plan: "03"
subsystem: content
tags: [mkdocs, markdown, defensa-en-profundidad, capas-si, nist-800-61, tabletop, ransomware, castrense]

dependency_graph:
  requires:
    - phase: 03-asignatura-2-capas-si
      plan: "01"
      provides: theory-page pattern, iso27000 context
  provides:
    - 8 theory pages covering Defensa en Profundidad unit (CONT-21..CONT-28)
    - capa-humana-perimetral-p1.md / p2.md — intro model (7-layer ASCII diagram) + perimeter (firewall, DMZ, VLANs)
    - capa-red-endpoint-p1.md / p2.md — Red Interna (TLS, VPN, NAC) + Endpoint (EDR, patches, USB)
    - capa-aplicaciones-datos-p1.md / p2.md — Applications (auth, OWASP Top 10) + Data (classification, BitLocker, DLP)
    - capa-activos-criticos-p1.md / p2.md — Critical assets + NIST SP 800-61 tabletop ransomware
  affects: [03-04]

tech-stack:
  added: []
  patterns:
    - "Defense-in-depth 7-layer model: ASCII stack diagram in CONT-21 anchors all subsequent pages"
    - "Layer-opening blockquote: 'Esta clase cubre Capa N: [Nombre]...' on every CONT-22..28"
    - "Parte 2 recap blockquote bridging from Parte 1 content"
    - "Tabletop exercise format: !!! info role cards + !!! example inject cards inside !!! danger container"

key-files:
  created: []
  modified:
    - docs/capas-si/defensa-profundidad/capa-humana-perimetral-p1.md
    - docs/capas-si/defensa-profundidad/capa-humana-perimetral-p2.md
    - docs/capas-si/defensa-profundidad/capa-red-endpoint-p1.md
    - docs/capas-si/defensa-profundidad/capa-red-endpoint-p2.md
    - docs/capas-si/defensa-profundidad/capa-aplicaciones-datos-p1.md
    - docs/capas-si/defensa-profundidad/capa-aplicaciones-datos-p2.md
    - docs/capas-si/defensa-profundidad/capa-activos-criticos-p1.md
    - docs/capas-si/defensa-profundidad/capa-activos-criticos-p2.md

decisions:
  - "7-layer defense-in-depth model chosen as backbone (vs. NIST CSF or 4-pair ROADMAP) — ASCII stack in CONT-21 anchors CONT-22..28; each page references its specific layer"
  - "CONT-28 tabletop roles renamed to Comandante / Oficial de Comunicaciones / Técnico de Sistemas (from RESEARCH: Responsable de Seguridad / Comandante / Técnico TI) — matches parallel_execution note in plan frontmatter"
  - "Inject 1 references WannaCry/SMBv1 explicitly for pedagogical continuity with CONT-01 Fase 2 (D-13)"
  - "CONT-28 tabletop section wrapped in !!! danger admonition as requested in parallel_execution note"
  - "No 'Aplicación en Contexto Castrense' dedicated section in CONT-21..28 (D-10 applies only to CONT-13..20); military examples integrated inline via !!! example admonitions"

metrics:
  duration_minutes: 25
  completed_date: "2026-06-16"
  task_count: 3
  file_count: 8
---

# Phase 03 Plan 03: Defensa en Profundidad — 8 Páginas Teóricas (CONT-21..28) Summary

**One-liner:** 8 páginas de la unidad Defensa en Profundidad (modelo de 7 capas) escritas con diagrama ASCII ancla en CONT-21, capas 2-6 detalladas con ejemplos castrenses inline, y CONT-28 como clase tabletop self-contained con NIST SP 800-61 + escenario de ransomware con referencia a WannaCry.

---

## What Was Built

### Task 1 — CONT-21 y CONT-22 (capa-humana-perimetral-p1.md, p2.md)

- **`docs/capas-si/defensa-profundidad/capa-humana-perimetral-p1.md`** (CONT-21, 1 hr): Introducción al modelo de defensa en profundidad. Incluye el diagrama ASCII de 7 capas obligatorio (PATTERNS §"CONT-21 ONLY") dentro de bloque de código fenced — ancla conceptual de toda la unidad. Cubre la Capa 1 Humana: ingeniería social (tabla de 5 técnicas), políticas de uso aceptable y procedimientos de reporte. Ejemplo castrense inline: ataque de phishing con firmware falso para radio táctica HF que instala RAT.

- **`docs/capas-si/defensa-profundidad/capa-humana-perimetral-p2.md`** (CONT-22, 4 hrs): Capa 2 Perimetral. Abre con referencia al modelo establecido en Parte 1. 5 secciones densas: firewall perimetral (3 generaciones + tabla comparativa), DMZ (diseño doble-firewall con diagrama ASCII de flujo, tabla de zonas por nivel de confianza), proxy inverso, segmentación de red (tabla comparativa sin vs. con segmentación), VLANs (tabla de asignación por zona con riesgo VLAN hopping). Ejemplos castrenses inline (concentrador VPN en DMZ para acceso remoto de oficiales; VLAN 40 aislada para radios tácticas IP).

### Task 2 — CONT-23..26 (capa-red-endpoint-p1.md, p2.md, capa-aplicaciones-datos-p1.md, p2.md)

- **`docs/capas-si/defensa-profundidad/capa-red-endpoint-p1.md`** (CONT-23, 4 hrs): Capa 3 Red Interna. TLS: handshake conceptual con diagrama ASCII secuencial, tabla de conceptos clave (certificado, CA, PFS), advertencia sobre TLS 1.0/1.1. VPN: tabla comparativa site-to-site vs. acceso remoto + tabla de protocolos (IPSec, OpenVPN, WireGuard). NAC: postura del dispositivo, tabla de criterios 802.1X, diagrama de flujo supplicant/authenticator/RADIUS. Ejemplo castrense: VPN IPSec sobre enlace de radio táctica; NAC bloqueando portátil personal en 802.1X.

- **`docs/capas-si/defensa-profundidad/capa-red-endpoint-p2.md`** (CONT-24, 1 hr): Capa 4 Endpoint. EDR vs. antivirus tradicional (tabla de capacidades). Gestión de parches con WannaCry/SMBv1 como caso de estudio (patch disponible 2 meses antes del ataque). Control USB: tabla de controles con efectividad + Stuxnet como referencia histórica de vector USB en air-gap.

- **`docs/capas-si/defensa-profundidad/capa-aplicaciones-datos-p1.md`** (CONT-25, 3 hrs): Capa 5 Aplicaciones. Autenticación (tabla de 3 factores + MFA), autorización (DAC/MAC/RBAC + privilege creep), validación de entrada. OWASP Top 10 (2021): tabla completa de 10 categorías con concepto simple y ejemplo de riesgo para cada una. Dos ejemplos castrenses: SQL injection en sistema de permisos; misconfiguration (A05+A07) en panel de administración de sistema de información de misión.

- **`docs/capas-si/defensa-profundidad/capa-aplicaciones-datos-p2.md`** (CONT-26, 2 hrs): Capa 6 Datos. Clasificación en 4 niveles militares (Público/Interno/Confidencial/Secreto) con tabla de reglas de manejo por nivel. BitLocker: diagrama conceptual sin/con cifrado, modos de autenticación (solo TPM / TPM+PIN / TPM+PIN+USB), nota sobre clave de recuperación. DLP: tabla de canales monitoreados con controles, DLP por contenido vs. etiqueta. Ejemplo castrense: DLP bloqueando envío de adjunto "Confidencial" a correo personal.

### Task 3 — CONT-27 y CONT-28 (capa-activos-criticos-p1.md, p2.md)

- **`docs/capas-si/defensa-profundidad/capa-activos-criticos-p1.md`** (CONT-27, 2 hrs): Capa 7 Activos Críticos. Definición de activo crítico. Tabla de activos críticos militares (C2, radios, SCIF, puesto de mando avanzado, servidor de directorio, backup) con impacto de compromiso. Criterios de criticidad (4 dimensiones). Tabla de criticidad con clasificación por activo. BCP/DRP: RTO, RPO, failover, backup offsite. Tabla de estrategias de recuperación (hot/warm/cold standby) por nivel de criticidad. Ejemplo castrense: warm standby del servidor C2 con snapshots cada 30 min para cumplir RTO 2 hrs / RPO 30 min.

- **`docs/capas-si/defensa-profundidad/capa-activos-criticos-p2.md`** (CONT-28, 3 hrs): Página única — teoría NIST SP 800-61 (1 hr) + tabletop ransomware (2 hrs). Parte teórica: 4 fases NIST con actividades, tablas de fuentes de detección, advertencia sobre restaurar sin corregir el vector. Tabletop: sección envuelta en `!!! danger` como solicitado. Preparación del instructor (instrucciones de división del aula). 3 tarjetas de rol (`!!! info`): Comandante, Oficial de Comunicaciones, Técnico de Sistemas — cada una con Tu rol / Lo que sabes al inicio / Decisiones que debes tomar. 4 inject cards (`!!! example`): Detección (Inject 1 con referencia explícita a WannaCry/SMBv1), Contención (backup en riesgo), Erradicación (reimaginar vs. limpiar), Recuperación (reconectar vs. esperar). Debrief con 5 preguntas de cierre. Footer → examen-final.md.

---

## Deviations from Plan

### Auto-resolved — Role names in CONT-28

**Found during:** Task 3

**Issue:** The PLAN.md PATTERNS section specified role names as "Responsable de Seguridad, Comandante/Oficial, Técnico TI". The `parallel_execution` note in the execution prompt specified "Comandante, Oficial de Comunicaciones, Técnico de Sistemas".

**Fix:** Used the names from the `parallel_execution` note (more specific and authoritative as a direct execution constraint): Comandante, Oficial de Comunicaciones, Técnico de Sistemas. These names are more appropriate for the military audience.

**Impact:** Role names changed; structure and quantity (3 roles) unchanged. No acceptance criteria violated.

---

## Known Stubs

None — all 8 files have complete, publication-ready content. No placeholder text or stub markers remain in any file.

---

## Threat Flags

No new threat surface introduced. All 8 files are static Markdown content without server-side processing, authentication, or dynamic components. STRIDE analysis from the plan's `<threat_model>` remains accurate (T-03-01: Tampering via git — accepted).

---

## Self-Check

### Files exist:

- [x] `docs/capas-si/defensa-profundidad/capa-humana-perimetral-p1.md`
- [x] `docs/capas-si/defensa-profundidad/capa-humana-perimetral-p2.md`
- [x] `docs/capas-si/defensa-profundidad/capa-red-endpoint-p1.md`
- [x] `docs/capas-si/defensa-profundidad/capa-red-endpoint-p2.md`
- [x] `docs/capas-si/defensa-profundidad/capa-aplicaciones-datos-p1.md`
- [x] `docs/capas-si/defensa-profundidad/capa-aplicaciones-datos-p2.md`
- [x] `docs/capas-si/defensa-profundidad/capa-activos-criticos-p1.md`
- [x] `docs/capas-si/defensa-profundidad/capa-activos-criticos-p2.md`

### Commits exist:

- [x] `637bd27` — feat(03-03): escribir CONT-21 y CONT-22
- [x] `3b64ffa` — feat(03-03): escribir CONT-23..26
- [x] `569332b` — feat(03-03): escribir CONT-27 y CONT-28

### Key acceptance criteria:

- [x] `grep -qi "defensa en profundidad" capa-humana-perimetral-p1.md` exits 0
- [x] `grep -qE "Capa [1-7]" capa-humana-perimetral-p1.md` exits 0 (diagrama ASCII de stack)
- [x] `grep -q "Capa 2: Perimetral" capa-humana-perimetral-p2.md` exits 0
- [x] `grep -qE "DMZ|VLAN" capa-humana-perimetral-p2.md` exits 0
- [x] `grep -q "Capa 3" capa-red-endpoint-p1.md` exits 0
- [x] `grep -q "Capa 4" capa-red-endpoint-p2.md` exits 0
- [x] `grep -q "Capa 5" capa-aplicaciones-datos-p1.md` exits 0
- [x] `grep -q "Capa 6" capa-aplicaciones-datos-p2.md` exits 0
- [x] `grep -qi "OWASP" capa-aplicaciones-datos-p1.md` exits 0
- [x] `grep -qiE "clasificación|BitLocker|DLP" capa-aplicaciones-datos-p2.md` exits 0
- [x] `grep -q "Capa 7" capa-activos-criticos-p1.md` exits 0
- [x] `grep -qiE "misión crítica|continuidad" capa-activos-criticos-p1.md` exits 0
- [x] `grep -qi "NIST SP 800-61" capa-activos-criticos-p2.md` exits 0
- [x] 3 tarjetas de rol (Comandante, Oficial de Comunicaciones, Técnico de Sistemas)
- [x] 4 inject cards (Detección, Contención, Erradicación, Recuperación)
- [x] `grep -q "WannaCry" capa-activos-criticos-p2.md` exits 0 (Inject 1)
- [x] `grep -q "examen-final.md" capa-activos-criticos-p2.md` exits 0
- [x] `mkdocs build --strict` exits 0

## Self-Check: PASSED
