# MoodleCloud Examen Parcial Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Entregar un banco privado y auditado de 20 preguntas fáciles, importarlo en un examen MoodleCloud de 10 preguntas aleatorias y publicar únicamente instrucciones seguras para los alumnos.

**Architecture:** MoodleCloud aloja usuarios, banco, intentos y calificaciones. El repositorio sólo conserva herramientas genéricas, instrucciones sin secretos y pruebas; preguntas, respuestas, credenciales, URL operativa y evidencias se guardan en `.private/examen-parcial/`, ignorado por Git. El quiz toma 3 preguntas de Controles, 3 de Defender, 3 de Firewall y 1 General, con una pregunta por página y navegación libre.

**Tech Stack:** MoodleCloud, formato GIFT UTF-8, CSV de usuarios Moodle, Python 3 estándar (`dataclasses`, `argparse`, `csv`, `secrets`, `unittest`), MkDocs Material.

**Spec:** `docs/superpowers/specs/2026-08-19-moodlecloud-examen-parcial-design.md`

## Global Constraints

- MoodleCloud será el servidor; no desarrollar ni desplegar una aplicación propia.
- El banco contiene exactamente 20 preguntas: 6 CDE, 6 DEF, 6 FWA y 2 GEN.
- Cada pregunta tiene cuatro alternativas, una sola correcta y dificultad básica.
- Cada intento contiene exactamente 10 preguntas aleatorias en proporción 3+3+3+1.
- Una pregunta por página, navegación libre, un intento y nota máxima 20.
- No publicar preguntas nuevas, respuestas, credenciales, URL operativa ni relación número↔nombre.
- Moodle usa cuentas pseudónimas `alumno01`–`alumno20`; los nombres reales permanecen fuera de Moodle.
- No inventar correos de terceros. El CSV final sólo se genera con emails reales o aliases únicos controlados por el instructor.
- No manipular credenciales administrativas de MoodleCloud desde scripts ni desde el repositorio.
- Todo dato sensible debe vivir bajo `.private/examen-parcial/` y nunca aparecer en `git status`.
- El banco debe aprobar validación estructural, revisión factual y ensayo real en Moodle; importar sin error no basta.

## File Map

Archivos versionados:

- `.gitignore` — excluye `.private/` y las maquetas locales `.superpowers/`.
- `scripts/validate_moodle_gift.py` — analiza el subconjunto GIFT usado por este banco y comprueba estructura, categorías, identificadores y alternativas.
- `tests/test_validate_moodle_gift.py` — pruebas unitarias del analizador y de las reglas del banco.
- `scripts/generate_moodle_users.py` — genera 20 cuentas numeradas a partir de un patrón de email controlado y escribe CSV compatible con Moodle.
- `tests/test_generate_moodle_users.py` — pruebas de unicidad, formato, matrícula y complejidad de contraseñas.
- `docs/seguridad-criptografia/en-el-ordenador/examen-parcial.md` — página pública sin preguntas ni respuestas; explica el acceso en aula.
- `docs/instructor/examen-parcial-moodlecloud.md` — runbook operativo sin claves ni secretos.
- `docs/instructor/index.md` — enlaza el runbook del examen.

Archivos privados e ignorados:

- `.private/examen-parcial/banco-moodle.gift` — banco importable de 20 preguntas.
- `.private/examen-parcial/auditoria-banco.md` — respuesta, justificación, evidencia local y fuente primaria de cada pregunta.
- `.private/examen-parcial/usuarios-numerados.csv` — carga definitiva de cuentas.
- `.private/examen-parcial/asignacion-alumnos.csv` — hoja número↔nombre para custodia del instructor.
- `.private/examen-parcial/contingencia.html` — formularios impresos A/B sin clave visible.
- `.private/examen-parcial/evidencia-moodle.md` — URL, región, configuración comprobada, resultados de ensayo e incidencias.

---

### Task 1: Proteger secretos y validar bancos GIFT

**Files:**
- Modify: `.gitignore`
- Create: `scripts/validate_moodle_gift.py`
- Create: `tests/test_validate_moodle_gift.py`

**Interfaces:**
- Consumes: texto GIFT UTF-8 con líneas `$CATEGORY`, títulos `::ID::`, una llave de apertura al final del enunciado y opciones en líneas independientes.
- Produces: `parse_bank(text: str) -> list[Question]`, `validate_bank(questions: list[Question]) -> list[str]` y CLI `python3 scripts/validate_moodle_gift.py PATH` con exit code 0/1.

- [ ] **Step 1: Añadir límites privados antes de crear secretos**

Agregar exactamente al final de `.gitignore`:

```gitignore

# Exámenes y credenciales locales — nunca publicar
.private/

# Maquetas temporales de diseño
.superpowers/
```

Run: `git check-ignore -v .private/examen-parcial/banco-moodle.gift .superpowers/brainstorm/mockup.html`

Expected: dos líneas que señalan las nuevas reglas de `.gitignore`.

- [ ] **Step 2: Escribir pruebas fallidas del parser GIFT**

Crear `tests/test_validate_moodle_gift.py` con `unittest`. Incluir como mínimo:

```python
import unittest

from scripts.validate_moodle_gift import Question, parse_bank, validate_bank


VALID_ONE = """$CATEGORY: $course$/top/Controles defensivos

::CDE-01::Pregunta sencilla {
=Correcta
~Incorrecta uno
~Incorrecta dos
~Incorrecta tres
}
"""


class ParseGiftTests(unittest.TestCase):
    def test_parses_category_id_stem_and_four_options(self):
        questions = parse_bank(VALID_ONE)
        self.assertEqual(len(questions), 1)
        self.assertEqual(questions[0].qid, "CDE-01")
        self.assertEqual(questions[0].category, "Controles defensivos")
        self.assertEqual(questions[0].correct, ("Correcta",))
        self.assertEqual(len(questions[0].wrong), 3)

    def test_reports_duplicate_choices_and_forbidden_wording(self):
        question = Question(
            qid="GEN-01",
            category="Conceptos generales",
            stem="¿Cuál es correcta?",
            correct=("Opción A",),
            wrong=("Opción A", "Todas las anteriores", "Opción D"),
        )
        errors = validate_bank([question])
        self.assertTrue(any("alternativas repetidas" in item for item in errors))
        self.assertTrue(any("frase prohibida" in item for item in errors))


if __name__ == "__main__":
    unittest.main()
```

- [ ] **Step 3: Confirmar el fallo inicial**

Run: `python3 -m unittest tests.test_validate_moodle_gift -v`

Expected: `ModuleNotFoundError` para `scripts.validate_moodle_gift`.

- [ ] **Step 4: Implementar el modelo y parser mínimo**

Crear `scripts/validate_moodle_gift.py` con:

```python
from __future__ import annotations

import argparse
import re
from collections import Counter
from dataclasses import dataclass
from pathlib import Path


EXPECTED_COUNTS = {
    "Controles defensivos": 6,
    "Microsoft Defender": 6,
    "Windows Firewall": 6,
    "Conceptos generales": 2,
}
PREFIX_BY_CATEGORY = {
    "Controles defensivos": "CDE",
    "Microsoft Defender": "DEF",
    "Windows Firewall": "FWA",
    "Conceptos generales": "GEN",
}
TITLE_RE = re.compile(
    r"^::(?P<qid>(?:CDE|DEF|FWA|GEN)-\d{2})::(?P<stem>.+?)\s+\{$"
)
FORBIDDEN = ("todas las anteriores", "ninguna de las anteriores")


@dataclass(frozen=True)
class Question:
    qid: str
    category: str
    stem: str
    correct: tuple[str, ...]
    wrong: tuple[str, ...]


def parse_bank(text: str) -> list[Question]:
    questions: list[Question] = []
    category: str | None = None
    lines = text.splitlines()
    index = 0

    while index < len(lines):
        line = lines[index].strip()
        if not line or line.startswith("//"):
            index += 1
            continue
        if line.startswith("$CATEGORY:"):
            category = line.rsplit("/", 1)[-1].strip()
            index += 1
            continue

        match = TITLE_RE.fullmatch(line)
        if not match:
            raise ValueError(f"Línea GIFT no reconocida {index + 1}: {line}")
        if category is None:
            raise ValueError(f"Pregunta sin categoría en línea {index + 1}")

        correct: list[str] = []
        wrong: list[str] = []
        index += 1
        while index < len(lines) and lines[index].strip() != "}":
            option = lines[index].strip()
            if option.startswith("="):
                correct.append(option[1:].strip())
            elif option.startswith("~"):
                wrong.append(option[1:].strip())
            elif option:
                raise ValueError(
                    f"Alternativa GIFT no reconocida {index + 1}: {option}"
                )
            index += 1
        if index >= len(lines):
            raise ValueError(f"Pregunta {match.group('qid')} sin llave de cierre")

        questions.append(
            Question(
                qid=match.group("qid"),
                category=category,
                stem=match.group("stem").strip(),
                correct=tuple(correct),
                wrong=tuple(wrong),
            )
        )
        index += 1

    return questions
```

- [ ] **Step 5: Implementar reglas de validación y CLI**

Completar el mismo archivo con funciones que:

- exijan 20 preguntas y conteos 6+6+6+2;
- exijan IDs únicos y prefijo acorde a la categoría;
- exijan una respuesta correcta y tres incorrectas;
- rechacen enunciados o alternativas vacíos;
- rechacen alternativas repetidas ignorando mayúsculas y espacios;
- rechacen las frases `todas las anteriores` y `ninguna de las anteriores`;
- rechacen enunciados duplicados ignorando mayúsculas y espacios;
- impriman todos los errores y terminen con exit code 1;
- impriman `OK: 20 preguntas; categorías 6+6+6+2` y terminen con 0 al aprobar.

La interfaz final será:

```python
def _normalized(value: str) -> str:
    return " ".join(value.casefold().split())


def validate_bank(questions: list[Question]) -> list[str]:
    errors: list[str] = []
    if len(questions) != 20:
        errors.append(f"se esperaban 20 preguntas y se encontraron {len(questions)}")

    category_counts = Counter(question.category for question in questions)
    for category, expected in EXPECTED_COUNTS.items():
        actual = category_counts.get(category, 0)
        if actual != expected:
            errors.append(
                f"categoría {category}: se esperaban {expected} y se encontraron {actual}"
            )
    for category in sorted(set(category_counts) - set(EXPECTED_COUNTS)):
        errors.append(f"categoría desconocida: {category}")

    id_counts = Counter(question.qid for question in questions)
    stem_counts = Counter(_normalized(question.stem) for question in questions)
    for qid, count in id_counts.items():
        if count > 1:
            errors.append(f"ID duplicado: {qid}")
    for stem, count in stem_counts.items():
        if count > 1:
            errors.append(f"enunciado duplicado: {stem}")

    for question in questions:
        expected_prefix = PREFIX_BY_CATEGORY.get(question.category)
        if expected_prefix and not question.qid.startswith(f"{expected_prefix}-"):
            errors.append(
                f"{question.qid}: prefijo incompatible con {question.category}"
            )
        if len(question.correct) != 1:
            errors.append(f"{question.qid}: debe tener una respuesta correcta")
        if len(question.wrong) != 3:
            errors.append(f"{question.qid}: debe tener tres distractores")

        choices = question.correct + question.wrong
        if not question.stem.strip() or any(not choice.strip() for choice in choices):
            errors.append(f"{question.qid}: contiene texto vacío")
        normalized_choices = [_normalized(choice) for choice in choices]
        if len(set(normalized_choices)) != len(normalized_choices):
            errors.append(f"{question.qid}: contiene alternativas repetidas")

        searchable = _normalized(" ".join((question.stem, *choices)))
        for phrase in FORBIDDEN:
            if phrase in searchable:
                errors.append(f"{question.qid}: contiene frase prohibida: {phrase}")

    return errors


def main() -> int:
    parser = argparse.ArgumentParser()
    parser.add_argument("path", type=Path)
    args = parser.parse_args()
    try:
        questions = parse_bank(args.path.read_text(encoding="utf-8"))
    except (OSError, UnicodeError, ValueError) as exc:
        print(f"ERROR: {exc}")
        return 1
    errors = validate_bank(questions)
    if errors:
        for error in errors:
            print(f"ERROR: {error}")
        return 1
    print("OK: 20 preguntas; categorías 6+6+6+2")
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

- [ ] **Step 6: Ampliar pruebas a la matriz completa**

Añadir helpers que construyan 6 CDE, 6 DEF, 6 FWA y 2 GEN en memoria. Comprobar
que una matriz válida devuelve `[]` y que fallan independientemente: 19
preguntas, categoría desconocida, prefijo incorrecto, dos correctas, sólo dos
distractores, ID duplicado y enunciado duplicado.

- [ ] **Step 7: Ejecutar pruebas y comprobaciones del repositorio**

Run: `python3 -m unittest tests.test_validate_moodle_gift -v`

Expected: todas las pruebas `OK`.

Run: `git diff --check`

Expected: sin salida.

- [ ] **Step 8: Commit**

```bash
git add .gitignore scripts/validate_moodle_gift.py tests/test_validate_moodle_gift.py
git commit -m "test(examen): validar bancos GIFT privados"
```

---

### Task 2: Redactar y auditar el banco privado de 20 preguntas

**Files:**
- Create, ignored: `.private/examen-parcial/banco-moodle.gift`
- Create, ignored: `.private/examen-parcial/auditoria-banco.md`

**Interfaces:**
- Consumes: las seis lecciones vigentes de la unidad y fuentes primarias enlazadas desde ellas.
- Produces: banco GIFT que aprueba `validate_moodle_gift.py` y auditoría trazable de sus 20 respuestas.

- [ ] **Step 1: Crear la estructura privada y verificar que está ignorada**

Crear ambos archivos mediante `apply_patch`, no mediante redirecciones de shell.

Run: `git check-ignore -v .private/examen-parcial/banco-moodle.gift .private/examen-parcial/auditoria-banco.md`

Expected: ambos coinciden con `.private/`.

- [ ] **Step 2: Fijar la cobertura exacta sin reutilizar enunciados públicos**

Usar estos IDs y conceptos:

| ID | Concepto | Evidencia local principal |
|---|---|---|
| CDE-01 | control preventivo | `controles-defensivos-p1.md`, «Qué es un control de seguridad» |
| CDE-02 | disponibilidad en la Triada CIA | `controles-defensivos-p1.md`, «Triada CIA» |
| CDE-03 | puerto abierto y servicio en escucha | `controles-defensivos-p1.md`, «La Superficie de Ataque» |
| CDE-04 | finalidad de `Get-LocalUser` | `controles-defensivos-p1.md`, Práctica 2 |
| CDE-05 | significado operativo de indicador rojo | `controles-defensivos-p1.md`, «Interpretando los colores» |
| CDE-06 | reducción de servicios innecesarios | `controles-defensivos-p2.md`, «Deshabilitar Servicios Innecesarios» |
| DEF-01 | detección por firmas | `antivirus-avanzados-p1.md`, «Cómo Funciona la Detección por Firmas» |
| DEF-02 | actualización con `Update-MpSignature` | `antivirus-avanzados-p1.md`, «Actualización de Definiciones» |
| DEF-03 | diferencia Quick Scan/Full Scan | `antivirus-avanzados-p2.md`, «Escaneos Programados» |
| DEF-04 | finalidad de la cuarentena | `antivirus-avanzados-p2.md`, «Gestión de Cuarentena» |
| DEF-05 | finalidad segura de EICAR | `antivirus-avanzados-p1.md`, Práctica 13 |
| DEF-06 | riesgo de una exclusión | `antivirus-avanzados-p1.md`, «Exclusiones: Necesidad y Riesgo» |
| FWA-01 | función principal del firewall | `cortafuegos-p1.md`, «¿Qué es un Firewall?» |
| FWA-02 | retorno de conexiones stateful | `cortafuegos-p1.md`, «Filtrado Stateful vs. Stateless» |
| FWA-03 | perfil Público en red no confiable | `cortafuegos-p1.md`, «Los Tres Perfiles de Red» |
| FWA-04 | diferencia inbound/outbound | `cortafuegos-p1.md`, «Reglas de Entrada vs. Salida» |
| FWA-05 | precedencia de una regla Block aplicable | `cortafuegos-p1.md`, Prueba 2 |
| FWA-06 | significado de `DROP` en el log | `cortafuegos-p2.md`, «Logging del Firewall» |
| GEN-01 | principio de mínimo privilegio | `controles-defensivos-p1.md`, «El Principio de Mínimo Privilegio» |
| GEN-02 | línea base y restauración tras una práctica | patrón de preparación/limpieza de los laboratorios de la unidad |

- [ ] **Step 3: Redactar primero la auditoría factual**

Para cada ID, completar privadamente estas columnas:

```markdown
| ID | Respuesta correcta | Justificación | Evidencia local | Fuente primaria | Revisión |
|---|---|---|---|---|---|
```

La columna `Revisión` sólo admite `APROBADA` después de verificar que la lección
y la fuente primaria sostienen la misma respuesta. Si difieren, corregir primero
el enunciado del banco; no forzar una clave para que coincida con material
desactualizado.

- [ ] **Step 4: Redactar el banco en el subconjunto GIFT validado**

Usar exactamente este patrón por pregunta, con una línea vacía entre bloques:

```gift
$CATEGORY: $course$/top/Controles defensivos

::CDE-01::Enunciado directo sin datos secretos {
=Respuesta correcta
~Distractor inequívocamente incorrecto uno
~Distractor inequívocamente incorrecto dos
~Distractor inequívocamente incorrecto tres
}
```

No incluir feedback dentro de GIFT; la justificación permanece en la auditoría
privada. Evitar caracteres GIFT reservados `=`, `~`, `#`, `{` y `}` dentro de los
textos para no depender de escapes.

- [ ] **Step 5: Ejecutar validación automática**

Run: `python3 scripts/validate_moodle_gift.py .private/examen-parcial/banco-moodle.gift`

Expected: `OK: 20 preguntas; categorías 6+6+6+2`.

- [ ] **Step 6: Ejecutar revisión de dificultad y fuga**

Run: `rg -ni "excepto|no es|incorrecta|todas las anteriores|ninguna de las anteriores" .private/examen-parcial/banco-moodle.gift`

Expected: sin coincidencias. Reformular cualquier pregunta negativa.

Run: `git status --short`

Expected: ninguno de los dos archivos privados aparece.

- [ ] **Step 7: Revisión humana del banco**

El instructor lee las 20 preguntas sin mirar la clave y confirma para cada una:
comprensión en una sola lectura, una única respuesta evidente para quien estudió,
distractores no engañosos y nivel básico. Registrar `APROBADA` en las 20 filas.

**No commit:** los dos entregables contienen respuestas y permanecen ignorados.

---

### Task 3: Generar cuentas numeradas y material de asignación

**Files:**
- Create: `scripts/generate_moodle_users.py`
- Create: `tests/test_generate_moodle_users.py`
- Create, ignored: `.private/examen-parcial/usuarios-numerados.csv`
- Create, ignored: `.private/examen-parcial/asignacion-alumnos.csv`

**Interfaces:**
- Consumes: `email_template` con `{number:02d}`, `course_shortname` existente en Moodle y ruta privada de salida.
- Produces: 20 filas CSV con campos `username,password,firstname,lastname,email,idnumber,course1,role1,emailstop` y hoja de asignación sin nombres precargados.

- [ ] **Step 1: Escribir pruebas fallidas del generador**

Crear `tests/test_generate_moodle_users.py` con casos para:

```python
import unittest

from scripts.generate_moodle_users import generate_rows


class GenerateRowsTests(unittest.TestCase):
    def test_generates_twenty_unique_numbered_students(self):
        rows = generate_rows(
            email_template="docente+alumno{number:02d}@institucion.edu",
            course_shortname="SEGINFO-ORD",
            password_factory=lambda number: f"ClaveSegura{number:02d}!",
        )
        self.assertEqual(len(rows), 20)
        self.assertEqual(rows[0]["username"], "alumno01")
        self.assertEqual(rows[-1]["username"], "alumno20")
        self.assertEqual(rows[0]["role1"], "student")
        self.assertEqual(len({row["email"] for row in rows}), 20)

    def test_rejects_template_without_number_field(self):
        with self.assertRaisesRegex(ValueError, "number:02d"):
            generate_rows(
                email_template="docente@institucion.edu",
                course_shortname="SEGINFO-ORD",
            )
```

- [ ] **Step 2: Confirmar el fallo inicial**

Run: `python3 -m unittest tests.test_generate_moodle_users -v`

Expected: `ModuleNotFoundError` para `scripts.generate_moodle_users`.

- [ ] **Step 3: Implementar generación segura**

Crear funciones con estas firmas:

```python
def secure_password(number: int) -> str:
    """Return 14+ chars with upper, lower, digit and !@%+-_."""


def generate_rows(
    email_template: str,
    course_shortname: str,
    password_factory=secure_password,
) -> list[dict[str, str]]:
    """Return alumno01..alumno20 and reject duplicate/invalid emails."""


def write_outputs(
    rows: list[dict[str, str]],
    users_path: Path,
    assignment_path: Path,
) -> None:
    """Write UTF-8 CSV files and chmod them to 0o600."""
```

El generador debe:

- exigir `{number:02d}` literalmente en el patrón;
- validar emails mediante estructura local@dominio, sin afirmar que el dominio
  pertenece al instructor;
- generar usuarios, emails y passwords únicos;
- usar `Alumno` como nombre y `01`–`20` como apellido e identificador;
- fijar `course1=SEGINFO-ORD`, `role1=student` y `emailstop=1`;
- escribir la hoja de asignación con columnas
  `numero,username,nombre_completo,firma_entrega` y los dos últimos campos vacíos;
- no imprimir contraseñas en stdout.

- [ ] **Step 4: Implementar CLI sin valores inseguros por defecto**

La CLI exige todos los valores variables:

```text
python3 scripts/generate_moodle_users.py \
  --email-template 'docente+alumno{number:02d}@example.invalid' \
  --course-shortname 'SEGINFO-ORD' \
  --users-output .private/examen-parcial/usuarios-numerados.csv \
  --assignment-output .private/examen-parcial/asignacion-alumnos.csv
```

`example.invalid` es un dominio reservado y la orden sólo ilustra la sintaxis; no
debe ejecutarse hasta que el instructor introduzca un mailbox y dominio reales
que controle.

- [ ] **Step 5: Ejecutar pruebas**

Run: `python3 -m unittest tests.test_generate_moodle_users -v`

Expected: todas las pruebas `OK`.

- [ ] **Step 6: Commit del generador, sin credenciales**

```bash
git add scripts/generate_moodle_users.py tests/test_generate_moodle_users.py
git commit -m "feat(examen): generar cuentas numeradas de Moodle"
```

- [ ] **Step 7: Checkpoint humano — proporcionar aliases controlados**

El instructor confirma un patrón de 20 emails reales o aliases únicos que recibe
y controla. Ejecutar entonces la CLI con ese patrón real y verificar:

Run: `wc -l .private/examen-parcial/usuarios-numerados.csv .private/examen-parcial/asignacion-alumnos.csv`

Expected: 21 líneas en cada archivo, incluyendo cabecera.

Run: `git status --short`

Expected: ninguno de los CSV aparece.

---

### Task 4: Sustituir el examen público y añadir el runbook del instructor

**Files:**
- Modify: `docs/seguridad-criptografia/en-el-ordenador/examen-parcial.md`
- Create: `docs/instructor/examen-parcial-moodlecloud.md`
- Modify: `docs/instructor/index.md`

**Interfaces:**
- Consumes: decisiones de la especificación y nombres exactos de los artefactos privados.
- Produces: instrucciones públicas sin banco/clave y un procedimiento operativo reproducible sin contraseñas.

- [ ] **Step 1: Reemplazar íntegramente la página pública del examen**

El nuevo contenido debe indicar:

```markdown
---
# Horas asignadas: 1 hr
# Tipo: Examen
---

# Examen Parcial — Unidad: En el Ordenador

El examen se realiza en la plataforma MoodleCloud indicada por el instructor en
el aula. Esta página no contiene el banco de preguntas ni la clave.

## Antes de comenzar

- Reciba del instructor su usuario numerado y contraseña individual.
- Confirme que el número entregado coincide con la hoja de asistencia.
- Abra únicamente la dirección o el código QR proyectado por el instructor.
- No inicie el intento hasta recibir la contraseña del cuestionario.

## Modalidad

- 10 preguntas de opción múltiple seleccionadas al azar de un banco de 20.
- Una sola respuesta correcta por pregunta.
- Una pregunta por página y navegación libre para revisar.
- Un solo intento; nota máxima: 20 puntos.
- La entrega sólo termina al pulsar «Enviar todo y terminar» y confirmar.

## Si ocurre un problema

No abra otra cuenta ni empiece un segundo intento. Levante la mano y conserve la
pantalla visible para que el instructor registre la incidencia.
```

No añadir URL, QR, horario, contraseña, preguntas ni respuestas.

- [ ] **Step 2: Crear el runbook público sin secretos**

`docs/instructor/examen-parcial-moodlecloud.md` debe contener, en este orden:

1. advertencia de que el sitio, banco y credenciales son privados;
2. preflight 24 horas antes;
3. importación GIFT y verificación 6+6+6+2;
4. tabla exacta de configuración Moodle del Task 5;
5. carga de un usuario piloto y luego los 20;
6. apertura, supervisión y cierre;
7. exportación de resultados;
8. manejo de credencial errónea, desconexión, entrega accidental y pregunta
   defectuosa;
9. limpieza: suspender cuentas, retirar contraseña activa y custodiar exportes;
10. referencia a `.private/examen-parcial/` sin reproducir contenido.

- [ ] **Step 3: Enlazar el runbook desde el índice del instructor**

Añadir un enlace descriptivo a `examen-parcial-moodlecloud.md`. No añadirlo al
`nav` público de `mkdocs.yml`.

- [ ] **Step 4: Verificar que desapareció la fuga activa**

Run: `rg -n "Solo instructor|Clave de Respuestas|Respuesta modelo|\*\*P[0-9]+\.\*\*" docs/seguridad-criptografia/en-el-ordenador/examen-parcial.md`

Expected: sin coincidencias.

Run: `rg -ni "alumno0[1-9]|alumno1[0-9]|alumno20|(password|contraseña)\\s*[:=]\\s*\\S+" docs/instructor/examen-parcial-moodlecloud.md`

Expected: ninguna credencial concreta; menciones conceptuales a “contraseña” son
aceptables sólo si no contienen un valor.

- [ ] **Step 5: Construir el sitio**

Run: `python3 -m mkdocs build --strict`

Expected: exit code 0.

- [ ] **Step 6: Commit**

```bash
git add docs/seguridad-criptografia/en-el-ordenador/examen-parcial.md docs/instructor/examen-parcial-moodlecloud.md docs/instructor/index.md
git commit -m "docs(examen): trasladar evaluacion parcial a MoodleCloud"
```

---

### Task 5: Configurar MoodleCloud con un usuario piloto

**Files:**
- Create, ignored: `.private/examen-parcial/evidencia-moodle.md`

**Interfaces:**
- Consumes: sitio MoodleCloud administrado por el usuario, `banco-moodle.gift` aprobado y una fila piloto del CSV.
- Produces: curso, categorías, quiz y cuenta piloto configurados; evidencia privada de cada ajuste.

- [ ] **Step 1: Checkpoint humano — crear o abrir MoodleCloud**

El usuario crea el sitio, selecciona conscientemente la región permitida por su
institución y conserva las credenciales administrativas fuera del repositorio.
No pegar tokens, passwords ni cookies en la conversación o en archivos
versionados.

- [ ] **Step 2: Crear el curso antes de generar el CSV definitivo**

Crear:

- Nombre completo: `Seguridad de la Información y Criptografía`.
- Nombre corto: `SEGINFO-ORD`.
- Visibilidad: oculto mientras se configura.

Registrar en `evidencia-moodle.md` la URL privada, región y fecha, sin password.

- [ ] **Step 3: Importar el banco GIFT**

En Question bank → Import → GIFT, importar
`.private/examen-parcial/banco-moodle.gift`. Confirmar visualmente las cuatro
categorías y conteos 6, 6, 6 y 2. Previsualizar las 20 preguntas; ninguna debe
mostrar llaves, tildes de GIFT o alternativas truncadas.

- [ ] **Step 4: Crear el cuestionario con estos valores exactos**

| Ajuste Moodle | Valor |
|---|---|
| Nombre | Examen parcial — En el Ordenador |
| Abrir/Cerrar | fechas definidas por el instructor |
| Límite de tiempo | configurable; si se activa, envío automático al vencer |
| Intentos permitidos | 1 |
| Nota para aprobar | criterio institucional, sin afectar el cálculo sobre 20 |
| Página nueva | cada pregunta |
| Método de navegación | Libre |
| Barajar dentro de las preguntas | Sí |
| Comportamiento | Retroalimentación diferida |
| Nota máxima | 20 |
| Contraseña | valor temporal comunicado sólo en el aula |
| Durante el intento | no mostrar corrección |
| Inmediatamente después | ocultar correcta/incorrecta, feedback y respuesta correcta |
| Mientras el examen siga abierto | ocultar correcta/incorrecta, feedback y respuesta correcta |
| Después de cerrar | mostrar nota; liberar respuestas sólo por decisión del instructor |

- [ ] **Step 5: Añadir diez slots aleatorios**

- 3 de `Controles defensivos`.
- 3 de `Microsoft Defender`.
- 3 de `Windows Firewall`.
- 1 de `Conceptos generales`.

Activar mezcla de orden a nivel del quiz. Confirmar que la suma de máximos se
reescala a 20.

- [ ] **Step 6: Importar sólo `alumno01` como piloto**

Crear un CSV temporal privado con cabecera idéntica al definitivo y la primera
fila. Usar la pantalla de previsualización de Moodle; confirmar rol Student y
matrícula en `SEGINFO-ORD` antes de aceptar.

- [ ] **Step 7: Ensayar como alumno piloto**

Desde una ventana privada del navegador o una estación distinta:

- iniciar sesión;
- comprobar acceso al curso;
- verificar que el quiz todavía está oculto;
- habilitarlo para el piloto;
- introducir la contraseña temporal;
- comprobar 10 preguntas y navegación;
- entregar;
- confirmar que no aparece la clave.

Registrar resultados y capturas privadas en `evidencia-moodle.md`.

**No commit:** contiene URL y evidencia operacional.

---

### Task 6: Preparar contingencia, cargar la clase y ejecutar UAT final

**Files:**
- Create, ignored: `.private/examen-parcial/contingencia.html`
- Modify, ignored: `.private/examen-parcial/evidencia-moodle.md`
- Use, ignored: `.private/examen-parcial/usuarios-numerados.csv`
- Use, ignored: `.private/examen-parcial/asignacion-alumnos.csv`

**Interfaces:**
- Consumes: banco auditado, Moodle piloto aprobado y aliases controlados.
- Produces: 20 cuentas operativas, dos formularios impresos de contingencia, UAT aprobada y paquete listo para examen.

- [ ] **Step 1: Construir dos formularios de contingencia sin clave visible**

En un único HTML privado, incluir:

- Formulario A: CDE-01..03, DEF-01..03, FWA-01..03 y GEN-01.
- Formulario B: CDE-04..06, DEF-04..06, FWA-04..06 y GEN-02.
- campos para nombre, número de cuenta, fecha y firma;
- cuatro alternativas por pregunta;
- salto de página entre A y B;
- ninguna marca que revele la respuesta correcta.

La clave permanece exclusivamente en `auditoria-banco.md`.

- [ ] **Step 2: Revisar impresión**

Abrir localmente `contingencia.html`, imprimir a PDF sólo para inspección y
confirmar: sin cortes de pregunta, alternativas legibles, identificación visible
y ausencia de clave. El PDF temporal también debe permanecer bajo `.private/`.

- [ ] **Step 3: Cargar las 19 cuentas restantes**

En Moodle, previsualizar primero el CSV completo. Confirmar 20 filas totales,
usuarios únicos, emails únicos, curso `SEGINFO-ORD` y rol Student. Como
`alumno01` ya existe, seleccionar el modo que conserva esa cuenta y añade o
actualiza sin duplicarla.

- [ ] **Step 4: Ejecutar cinco previsualizaciones administrativas**

En cada una registrar en `evidencia-moodle.md`:

- 10 preguntas;
- 3 CDE, 3 DEF, 3 FWA y 1 GEN;
- ningún ID repetido;
- cuatro alternativas por pregunta;
- una pregunta por página.

- [ ] **Step 5: Probar calificación y visibilidad**

Con una cuenta de prueba autorizada, realizar un intento controlado con cinco
aciertos y cinco errores según la auditoría privada.

Expected:

- nota `10/20`;
- intento registrado con timestamps;
- no se muestran respuesta correcta ni feedback antes del cierre;
- un segundo intento es rechazado.

- [ ] **Step 6: Probar una desconexión breve**

Durante un intento de prueba, responder al menos tres preguntas, desconectar la
red, restablecerla e iniciar sesión con la misma cuenta. Registrar si Moodle
conservó cada respuesta. Si alguna se pierde, añadir al runbook la instrucción de
esperar confirmación de guardado antes de avanzar y repetir hasta obtener un
procedimiento confiable.

- [ ] **Step 7: Probar reporte y exportación**

Desde Results/Grades, verificar usuario, nota, preguntas, respuestas y tiempo.
Exportar CSV/Excel a `.private/examen-parcial/` y comprobar que `alumnoNN` permite
cruzar la nota con la hoja de asignación.

- [ ] **Step 8: Cierre de seguridad**

- Cambiar la contraseña usada en el ensayo.
- Ocultar el quiz hasta el momento real.
- Confirmar que no existen cuentas compartidas.
- Guardar credenciales y relación número↔nombre bajo custodia del instructor.
- Confirmar que la contingencia no contiene clave.

- [ ] **Step 9: Ejecutar verificación completa del repositorio**

Run: `python3 -m unittest discover -s tests -p 'test_*.py' -v`

Expected: todas las pruebas `OK`.

Run: `python3 scripts/validate_moodle_gift.py .private/examen-parcial/banco-moodle.gift`

Expected: `OK: 20 preguntas; categorías 6+6+6+2`.

Run: `python3 -m mkdocs build --strict`

Expected: exit code 0.

Run: `git diff --check && git status --short`

Expected: sin errores; ningún archivo bajo `.private/` o `.superpowers/` aparece.

- [ ] **Step 10: Registrar ajustes públicos descubiertos por la UAT**

Si la interfaz real de MoodleCloud usa etiquetas distintas a las del runbook,
corregir sólo esos nombres y repetir MkDocs. Si no hubo diferencias, no crear un
commit vacío.

```bash
git add docs/instructor/examen-parcial-moodlecloud.md
git commit -m "docs(examen): ajustar runbook tras ensayo MoodleCloud"
```

Ejecutar ese commit únicamente si el archivo cambió.

---

## Human Checkpoints

1. Crear/autorizar el sitio MoodleCloud y escoger región de datos.
2. Proporcionar emails reales o un patrón de aliases únicos controlados.
3. Aprobar humanamente las 20 preguntas y respuestas privadas.
4. Introducir la contraseña temporal del cuestionario fuera del repositorio.
5. Ejecutar desde una estación de alumno la UAT de acceso y desconexión.

## Definition of Done

- Banco privado nuevo, 20/20 respuestas auditadas y validadas.
- MoodleCloud entrega 10 preguntas balanceadas y aleatorias a cada cuenta.
- Flujo de revisión, envío, nota sobre 20 y bloqueo de segundo intento probado.
- Respuestas ocultas durante la ventana de examen.
- 20 cuentas numeradas y matriculadas sin almacenar nombres reales en Moodle.
- Exportación de notas cruzable con la hoja número↔nombre.
- Contingencia A/B imprimible disponible sólo al instructor.
- Página pública sin preguntas ni clave.
- MkDocs strict, pruebas Python y validador GIFT en verde.
- Ningún secreto visible en Git o en el sitio público.
