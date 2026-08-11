---
quick_task: 260810-rjh
description: "Auditar pedagógicamente las cuatro asignaturas del curso y definir el backlog de mejora"
type: execute
mode: quick-full
status: planned
files_modified:
  - .planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md
autonomous: true

must_haves:
  truths:
    - "Las cuatro asignaturas se evalúan por separado con evidencia concreta del repositorio, no por extensión aparente o número de páginas"
    - "La auditoría distingue fundamentos, ejemplos, práctica guiada, ejercicios, evaluación, preparación docente, adecuación horaria y reproducibilidad de laboratorio"
    - "La recomendación de VM compara Kali con una distribución generalista y define criterios de OVA, recursos, red, snapshots y seguridad sin construir aún la imagen"
    - "La sección ISO identifica lo que el profesor necesita para dictar, practicar y evaluar sin reproducir el texto protegido de las normas"
    - "El cierre prioriza mejoras atómicas y ejecutables como futuras gsd-quick, cada una con criterio de aceptación"
  artifacts:
    - path: ".planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md"
      provides: "Diagnóstico pedagógico, decisión preliminar de plataforma Linux, brechas de guía ISO y backlog priorizado"
      contains: "## Diagnóstico por asignatura"
  key_links:
    - from: ".planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md"
      to: "docs/"
      via: "referencias a archivos y patrones observados en cada asignatura"
      pattern: "docs/"
    - from: ".planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md"
      to: "ISO, Kali, Oracle VirtualBox, Ubuntu/Debian y Microsoft"
      via: "tabla de trazabilidad con afirmación, edición/versión, organismo, URL oficial y fecha de consulta"
      pattern: "(iso\\.org|kali\\.org|virtualbox\\.org|ubuntu\\.com|debian\\.org|microsoft\\.com)"
---

<objective>
Evaluar el Módulo 4 completo como material listo para dictar, identificar brechas reales y ordenar su corrección mediante futuras tareas gsd-quick.

Purpose: La versión v1.0 fue verificada contra requisitos de existencia, estructura y build, pero ahora se requiere una auditoría pedagógica y operativa: riqueza de fundamentos, ejemplos, ejercicios, soporte al profesor, exactitud actual y uniformidad del laboratorio.

Output: Un único informe `260810-rjh-AUDIT.md`. Esta tarea no modifica lecciones, exámenes, navegación ni soluciones de laboratorio.
</objective>

<tasks>

<task type="auto">
  <name>Task 1: Construir la rúbrica y auditar las cuatro asignaturas</name>
  <files>
    .planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md
  </files>
  <action>
Leer `.planning/PROJECT.md`, `.planning/ROADMAP.md`, `mkdocs.yml` y todos los Markdown curriculares relevantes bajo `docs/`. Construir una rúbrica de 0 a 4 para: fundamentos; ejemplos contextualizados; demostraciones o práctica guiada; ejercicios del alumno; calidad/alineación de evaluación; guía y solucionario para el profesor; adecuación a horas y audiencia; vigencia técnica; reproducibilidad del laboratorio.

Evaluar separadamente: (1) Seguridad de la Información y Criptografía, 32 h; (2) Capas SI, 48 h, desglosando Familia ISO 27000 y Defensa en Profundidad; (3) Gestión de Riesgos y Vulnerabilidades, 8 h; (4) Conferencias Magistrales, 3 h. Para cada una registrar fortalezas, brechas, evidencia por archivo, severidad e impacto docente. No confundir longitud con profundidad ni un examen con práctica formativa.
  </action>
  <verify>
    <automated>REPORT=.planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md; test -f "$REPORT" &amp;&amp; rg -q '^## Rúbrica' "$REPORT" &amp;&amp; rg -q '^## Diagnóstico por asignatura' "$REPORT" &amp;&amp; for p in 'Seguridad de la Información' 'Capas SI' 'Gestión de Riesgos' 'Conferencias Magistrales'; do rg -q "$p" "$REPORT" || exit 1; done &amp;&amp; test "$(rg -o 'docs/[A-Za-z0-9_./-]+\.md' "$REPORT" | sort -u | wc -l)" -ge 4 &amp;&amp; rg -o 'docs/[A-Za-z0-9_./-]+\.md' "$REPORT" | sort -u | while IFS= read -r path; do test -f "$path" || { echo "Ruta citada inexistente: $path"; exit 1; }; done</automated>
  </verify>
  <done>
Cada asignatura tiene puntuación explicada, evidencias, fortalezas, carencias y una conclusión sobre si está lista para dictarse sin preparación adicional.
  </done>
</task>

<task type="auto">
  <name>Task 2: Evaluar la plataforma OVA y las necesidades docentes de ISO 27000</name>
  <files>
    .planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md
  </files>
  <action>
Contrastar los laboratorios técnicos existentes con documentación oficial vigente. Comparar Kali Linux con Ubuntu LTS o Debian estable como guest común para VirtualBox sobre Windows 10/11, considerando: propósito pedagógico defensivo, estabilidad, superficie/herramientas preinstaladas, actualizaciones, OVA, snapshots, Guest Additions, 16 GB de RAM host, 20 vCPU, red NAT/host-only y aislamiento. Recomendar una base y una alternativa; proponer recursos mínimos/recomendados y política de snapshots, sin crear la VM.

Revisar el bloque ISO contra las ediciones actuales de ISO/IEC 27001, 27002 y 27005 usando páginas oficiales. Identificar qué necesita una guía docente: objetivos, mapa conceptual, caso conductor, notas por sesión, ejemplos de evidencia, instrucciones de ejercicios, respuestas/rúbricas, errores frecuentes, tiempos y límites de copyright. No copiar controles o cláusulas de las normas más allá de lo permitido; resumir y enlazar fuentes oficiales.
  </action>
  <verify>
    <automated>REPORT=.planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md; rg -q '^## Plataforma Linux y OVA' "$REPORT" &amp;&amp; rg -q '^## Guía docente ISO 27000' "$REPORT" &amp;&amp; rg -q '^\| Afirmación \| Edición/versión \| Organismo \| URL oficial \| Fecha de consulta \|' "$REPORT" &amp;&amp; for u in 'https://([^ )]*\.)?iso\.org/' 'https://([^ )]*\.)?kali\.org/' 'https://([^ )]*\.)?virtualbox\.org/' 'https://([^ )]*\.)?(ubuntu\.com|debian\.org)/' 'https://([^ )]*\.)?microsoft\.com/'; do rg -q "$u" "$REPORT" || exit 1; done</automated>
  </verify>
  <done>
Existe una recomendación defendible de plataforma Linux y una especificación de alcance para la futura guía docente ISO.
  </done>
</task>

<task type="auto">
  <name>Task 3: Priorizar el backlog de siguientes gsd-quick</name>
  <files>
    .planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md
  </files>
  <action>
Convertir cada brecha material en una tarea futura pequeña y comprobable. Ordenar por P0/P1/P2, dependencias y valor docente. Separar, como mínimo, decisiones de plataforma/OVA, construcción y prueba de OVA, guía docente ISO, ejercicios ISO, validación de labs técnicos, refuerzo de cada asignatura y revisión de exámenes. Cada ítem debe incluir alcance, archivos probables y criterio de aceptación. Evitar una megatarea que cambie todo el curso a la vez.
  </action>
  <verify>
    <automated>rg -q '^## Backlog priorizado de gsd-quick' .planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md &amp;&amp; for p in 'P0' 'P1' 'P2' 'Dependencias' 'Criterio de aceptación' 'Siguiente paso recomendado'; do rg -q "$p" .planning/quick/260810-rjh-auditar-pedagogicamente-las-cuatro-asign/260810-rjh-AUDIT.md || exit 1; done</automated>
  </verify>
  <done>
El usuario puede elegir la siguiente gsd-quick sin volver a investigar el estado general del curso.
  </done>
</task>

</tasks>

<verification>
- Confirmar que no se modificaron lecciones ni configuración con `git diff --quiet -- docs mkdocs.yml requirements.txt .planning/PROJECT.md .planning/ROADMAP.md .planning/REQUIREMENTS.md` y `test -z "$(git ls-files --others --exclude-standard -- docs)"`.
- Confirmar que el estado Git ajeno a los artefactos ignorados de esta quick conserva la huella inicial: `test "$(git status --porcelain=v1 --untracked-files=all | sha256sum | cut -d' ' -f1)" = "a3bc5a120e8d50b48ea381cded074e861792528eb170729321982e94620b8722"`. La huella corresponde únicamente a cambios preexistentes del usuario: `.continue-here.md` eliminado y `BRIEF.md`, `SCENARIOS_ES.md`, `temas.txt` sin seguimiento.
- Confirmar que el informe cubre las cuatro asignaturas y ambos temas transversales (OVA e ISO docente).
- Confirmar que todo hecho externo inestable o actual está respaldado por fuente primaria oficial.
- Confirmar que hechos observados, inferencias y recomendaciones están diferenciados.
- Ejecutar los tres bloques `<automated>` y exigir código de salida 0.
</verification>

<success_criteria>
- Informe único, accionable y basado en evidencia.
- Ninguna lección existente modificada.
- Decisión de VM presentada como recomendación preliminar, no como OVA ya validada.
- Backlog listo para ejecución incremental mediante gsd-quick.
</success_criteria>
