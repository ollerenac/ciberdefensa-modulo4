---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Métodos Básicos de Identificación y Priorización de Vulnerabilidades

> **Duración:** 2 horas | **Asignatura:** Gestión de Riesgos y Vulnerabilidades | **Unidad:** Identificación y Evaluación de Vulnerabilidades

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Localizar el registro oficial de un CVE y su enriquecimiento técnico a partir del identificador.
- Extraer la severidad CVSS, su versión, fuente y vector, además de los sistemas afectados.
- Diseñar controles compensatorios para una vulnerabilidad sin parche disponible.
- Aplicar PDCA para convertir una alerta de vulnerabilidad en una mejora permanente del SGSI.

---

## Qué es CVE — El Lenguaje Común de las Vulnerabilidades

Cuando un investigador descubre una falla en Windows, en el firmware de una radio, o en el software de gestión de una red, esa falla necesita un nombre estándar para que todos hablen del mismo problema. Ese nombre es un **CVE**.

!!! note "Definición"
    **CVE (Common Vulnerabilities and Exposures)** es el catálogo público de identificadores estandarizados para vulnerabilidades conocidas. Cada registro tiene un ID único con el formato **CVE-AÑO-NÚMERO** (ejemplo: CVE-2024-1234). Una organización autorizada publica el registro oficial en el [Programa CVE](https://www.cve.org/); después, la [National Vulnerability Database (NVD)](https://nvd.nist.gov) agrega datos de severidad y de los sistemas afectados.

**Por qué importa para el Técnico:** Cuando un proveedor publica una alerta de seguridad, la referencia con un CVE-ID. El Técnico puede usar ese identificador para consultar el registro oficial, contrastarlo con NVD y revisar el aviso del proveedor. Así determina qué falla existe, qué sistemas están afectados y qué tan urgente es actuar.

---

## Cómo Leer una Entrada CVE

El objetivo no es escribir CVE — es leer los registros existentes y tomar decisiones con ellos. Para una investigación completa se consultan tres capas:

1. **CVE.org:** identificador, descripción y referencias del registro oficial.
2. **NVD:** datos complementarios, como la severidad CVSS y las configuraciones afectadas.
3. **Aviso del proveedor:** productos afectados, contexto técnico y acciones dirigidas a sus clientes.

### Ejemplo: Leer una entrada CVE

**CVE-2024-XXXX** | CVSS Base Score: **8.8 (High)**

| Campo | Contenido |
|-------|-----------|
| **Descripción** | Vulnerabilidad de desbordamiento de búfer en el servicio SMB de Windows que permite ejecución remota de código sin autenticación previa. |
| **Sistemas afectados** | Windows 10 (todas las versiones), Windows 11 (versiones anteriores a 22H2) |
| **Vector de ataque** | Red — accesible desde la red sin requerir interacción del usuario |
| **Puntuación CVSS** | 8.8 — **High (Alta)** → requiere acción en 7 días o menos |
| **Solución disponible** | Sí — parche de seguridad MS24-XXX (Windows Update) |

!!! warning "El número solo no basta"
    La forma correcta de registrar una evaluación es **CVSS 3.1: 8.8 — fuente NVD — vector `CVSS:3.1/...`**. Un mismo CVE puede mostrar evaluaciones de fuentes diferentes o generaciones distintas de CVSS. No se promedian: se conserva la procedencia de cada una.

**¿Qué hace el Técnico con este CVE?**

1. **Verifica si sus sistemas están afectados.** Si los terminales C2 corren Windows 10 o Windows 11 anterior a 22H2, están en la lista.
2. **Consulta si hay parche disponible.** El campo "Solución disponible" dice que sí existe — el parche MS24-XXX aplica vía Windows Update.
3. **Prioriza por score:** 8.8 = High (Alta) → plazo de remediación: dentro de 7 días.
4. **Aplica el parche y documenta** la acción en el registro de mantenimiento de la unidad.

!!! tip "Regla de los dos pasos"
    Antes de aplicar cualquier parche en sistemas de producción (especialmente terminales C2), el Técnico informa al Jefe inmediato. No hay actualizaciones silenciosas en sistemas críticos — cada cambio debe ser conocido y trazable.

---

## Interpretación del Score CVSS v3.1

El score CVSS (Common Vulnerability Scoring System) es un número del 0 al 10 que resume la severidad de una vulnerabilidad. El Técnico no necesita saber cómo se calcula — necesita saber qué significa la banda en la que cae el número y qué acción tomar.

### Tabla de Severidad y Plazo de Remediación

| Banda | Rango | Significado | Acción del Técnico | Plazo |
|-------|-------|-------------|-------------------|-------|
| **Critical** | 9.0 – 10.0 | Vulnerabilidad explotable remotamente sin autenticación, con impacto total en el sistema | Escalar inmediatamente al Jefe. Aislar sistemas afectados si es posible. Aplicar parche o control compensatorio de emergencia. | Dentro de las **24 horas** siguientes a la detección |
| **High** | 7.0 – 8.9 | Vulnerabilidad grave con alta probabilidad de explotación o impacto significativo | Planificar remediación con fecha comprometida. Informar al Jefe en el próximo reporte de turno. | Mitigar en **7 días** |
| **Medium** | 4.0 – 6.9 | Vulnerabilidad que requiere condiciones específicas para ser explotada (ej. acceso local, interacción del usuario) | Incluir en el plan de mantenimiento del ciclo actual. No es emergencia, pero no se ignora. | Plan de remediación en **30 días** |
| **Low** | 0.1 – 3.9 | Vulnerabilidad con impacto mínimo o muy difícil de explotar | Documentar en el registro de vulnerabilidades. Revisar en el próximo ciclo de auditoría. | Documentar y revisar en el **ciclo regular** |

!!! note "Lo que no importa del score"
    La diferencia entre 8.1 y 8.8 no es operacionalmente relevante — ambas son "High" y requieren el mismo plazo de 7 días. Lo que importa es la **banda**, no el número exacto. Un score 9.0 es Critical aunque sea solo 0.1 puntos más alto que un 8.9 High — esa diferencia de banda sí importa (24 hrs vs. 7 días).

---

## Métodos de Identificación sin Herramientas Especializadas

Los Técnicos de NCO no tienen acceso a herramientas como Nessus, Qualys o OpenVAS. Pero hay tres métodos que no requieren ningún software adicional:

### Método 1: Windows Update / Windows Security

El panel de Windows Update muestra qué parches están pendientes. Cada actualización crítica corresponde a uno o más CVEs. El Técnico revisa:

- **Actualizaciones de seguridad pendientes:** ¿Hay parches marcados como "Crítico" o "Importante"?
- **Última fecha de actualización:** Si el sistema no se actualiza desde hace más de 30 días, hay acumulación de vulnerabilidades.
- **Windows Security > Protección frente a amenazas:** Muestra si el antivirus está activo y actualizado.

Este método no identifica el CVE específico de cada falla, pero permite detectar si el sistema está en un estado vulnerable por falta de parches.

### Método 2: Búsqueda directa en NVD por producto y versión

Si el Técnico sabe qué software y versión corre en un sistema, puede buscar directamente en [https://nvd.nist.gov](https://nvd.nist.gov):

1. Ir a nvd.nist.gov → sección "Vulnerability Search".
2. Buscar por nombre de producto (ej. "Adobe Acrobat 2020", "Windows Server 2016").
3. Filtrar por fecha: últimos 90 días.
4. Revisar los CVEs en bandas High y Critical.
5. Verificar si la versión del sistema de la unidad está en la lista de "sistemas afectados".

Este método requiere tiempo pero es muy preciso para software específico que se sabe que está en uso.

### Método 3: Boletines de seguridad del fabricante — Microsoft Patch Tuesday

Microsoft publica cada **segundo martes del mes** (conocido como "Patch Tuesday") la lista completa de vulnerabilidades que corrige en sus actualizaciones de ese ciclo. Cada entrada incluye el CVE-ID, la severidad y los sistemas afectados.

El Técnico puede:
- Revisar el boletín mensual en [https://msrc.microsoft.com/update-guide/](https://msrc.microsoft.com/update-guide/)
- Filtrar por "Critical" y "High".
- Comparar con los sistemas de la unidad.
- Priorizar actualizaciones según el plazo de la banda.

Este método no requiere acceso a los sistemas — solo conexión a internet para leer el boletín. Es útil para planificar el ciclo de actualizaciones mensual.

---

## Priorización por Impacto en Misión

El score CVSS da la severidad técnica de una vulnerabilidad. Pero en el entorno militar, hay un segundo criterio: la **criticidad del activo afectado**. Los dos criterios combinados determinan la prioridad real.

### Tabla de Decisión: CVSS × Criticidad del Activo

| Score CVSS | Activo de Misión Crítica (C2, radios tácticas, servidor de comunicaciones) | Activo Administrativo (impresora, laptop de oficina, equipo de uso general) |
|------------|--------------------------------------------------------------------------|----------------------------------------------------------------------------|
| **Critical (9–10)** | Acción inmediata. Escalar en horas, no en días. | Acción en 24 horas — misma urgencia por la gravedad de la falla. |
| **High (7–8.9)** | Tratar como Critical: escalar en 24 horas. El activo amplifica el impacto. | Tratar como High estándar: mitigar en 7 días. |
| **Medium (4–6.9)** | Tratar como High: plan de remediación en 7 días. | Tratar como Medium estándar: plan en 30 días. |
| **Low (0.1–3.9)** | Documentar y revisar en el próximo ciclo de auditoría. | Documentar. No es acción prioritaria. |

**Regla de elevación:** Si una vulnerabilidad High afecta el sistema C2, se trata como Critical. Si la misma vulnerabilidad High afecta una laptop de uso administrativo sin datos sensibles, se mantiene como High estándar. La criticidad del activo puede subir una banda completa la prioridad de acción.

---

## Aplicación en Contexto Castrense

### Ejercicio por grupos: Alerta Zero-Day — del CVE al SGSI

> **Duración:** 60 minutos | **Modalidad:** 8 grupos | **Producto:** ficha de una página y exposición de 90 segundos

#### Situación

Son las 08:00. La **Jefatura de Comunicaciones del Batallón de Comunicaciones N.° 3 (JC-BC3)** recibe una alerta sobre una vulnerabilidad explotada activamente. La organización confirma que utiliza al menos un sistema afectado, pero todavía no existe parche ni solución oficial disponible.

Cada grupo recibe solamente un CVE. Debe investigar la vulnerabilidad, identificar qué activo de la JC-BC3 podría estar afectado y preparar una respuesta defensiva para el mando.

!!! warning "Reglas del escenario"
    - Aunque estos CVE son históricos, deben tratarlos como si acabaran de publicarse.
    - Ignoren los campos **Solutions**, parches, actualizaciones y mitigaciones históricas del proveedor.
    - No busquen instrucciones ni herramientas para atacar sistemas.
    - No realicen cambios en equipos ni en la red real del aula.
    - Los controles propuestos deben ser defensivos, reversibles y verificables.

#### Asignación

| Grupo | CVE asignado |
|-------|--------------|
| 1 | `CVE-2017-0144` |
| 2 | `CVE-2020-1472` |
| 3 | `CVE-2021-44228` |
| 4 | `CVE-2017-5638` |
| 5 | `CVE-2014-6271` |
| 6 | `CVE-2018-13379` |
| 7 | `CVE-2019-19781` |
| 8 | `CVE-2023-34362` |

#### Fuentes permitidas

- [CVE.org](https://www.cve.org/) — registro oficial de la vulnerabilidad.
- [NVD](https://nvd.nist.gov/) — severidad CVSS y sistemas afectados.
- Aviso oficial del proveedor del producto afectado.

Usen la **[ficha imprimible del ejercicio](ficha-zero-day-pdca.md)** para organizar el trabajo y entregar una sola página por grupo. La ficha incluye un glosario breve de los términos necesarios.

#### Parte 1 — Conciencia de la vulnerabilidad

Completen cuatro hallazgos con sus propias palabras:

| Campo | Respuesta del grupo |
|-------|---------------------|
| ¿De qué trata la vulnerabilidad? | |
| Producto, componente y versiones afectadas | |
| CVSS: puntaje, severidad, versión, fuente y vector | |
| Consecuencia, activo plausible de la JC-BC3 y prioridad | |

El **vector CVSS** es la cadena que comienza con `CVSS:` y resume los factores usados en la calificación. Solo deben copiarla de la fuente y citar su procedencia; no tienen que calcularla.

#### Parte 2 — Respuesta mediante PDCA

En el ciclo formal, la cuarta fase es **Actuar**. En este escenario incluye reaccionar ante los resultados, corregir la respuesta y mejorar el SGSI.

| Fase | Preguntas que deben responder |
|------|-------------------------------|
| **Planear** | Identifiquen el activo y la misión expuestos. Definan un objetivo defensivo y un responsable. |
| **Hacer** | Propongan dos acciones temporales: una para reducir la exposición y otra para mantener la continuidad de la operación. |
| **Verificar** | Indiquen una comprobación que demuestre que el uso autorizado continúa y que la vía no autorizada quedó restringida. Señalen además un registro o alerta que revisarían. |
| **Actuar** | Decidan qué control mantendrían o ajustarían, qué riesgo aún permanece y qué mejora incorporarían al SGSI. |

No basta con escribir “instalar firewall”, “activar antivirus” o “usar MFA”. Expliquen brevemente **cómo reduce la exposición y cómo comprobarían que funcionó**.

#### Parte 3 — Capacidad permanente para el SGSI

La respuesta no termina cuando se contiene el zero-day. Propongan una capacidad permanente que incluya:

- fuente de alertas, responsable y frecuencia de revisión;
- inventario de productos, versiones y dependencias;
- configuración segura de referencia, revisión de cambios y tratamiento de excepciones;
- evidencia o indicador que se informará al mando.

Ejemplo de indicador: **porcentaje de vulnerabilidades Critical evaluadas dentro de las primeras 24 horas**.

La **configuración segura** o *hardening* reduce funciones y accesos innecesarios. Una excepción es un sistema que no puede cumplirla por una necesidad operativa; debe quedar aprobada, protegida con otro control y revisada periódicamente.

#### Organización de los 60 minutos

| Tiempo | Actividad |
|--------|-----------|
| 0–5 min | Explicación del escenario, reglas y asignación |
| 5–20 min | Investigación en fuentes oficiales |
| 20–40 min | Elaboración de la ficha y respuesta PDCA |
| 40–52 min | Ocho exposiciones de 90 segundos |
| 52–60 min | Comparación de respuestas y cierre del instructor |

#### Evaluación — 20 puntos

| Criterio | Puntos |
|----------|--------|
| Investigación correcta de la vulnerabilidad y sistemas afectados | 5 |
| CVSS con puntaje, severidad, versión, fuente y vector | 3 |
| Acciones PDCA concretas y aplicables al escenario | 6 |
| Verificación mediante pruebas y evidencias | 3 |
| Mejora permanente incorporada al SGSI | 3 |
| **Total** | **20** |

---

## Resumen

- **CVE** es el identificador estándar de vulnerabilidades. CVE.org publica el registro oficial y NVD lo enriquece con análisis técnico.
- El **score CVSS** debe registrarse con versión, fuente y vector. Tiene cuatro bandas: Critical (9–10), High (7–8.9), Medium (4–6.9) y Low (0.1–3.9).
- Los **tres métodos sin herramientas especializadas**: Windows Update para parches pendientes, búsqueda en NVD por producto/versión, y boletines Microsoft Patch Tuesday.
- La **criticidad del activo** puede elevar la prioridad de acción: una vulnerabilidad High en un sistema C2 se trata como Critical.
- El Técnico combina severidad, exposición y criticidad del activo; después aplica controles, conserva evidencia y mejora el SGSI mediante PDCA.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- National Vulnerability Database — NVD: [https://nvd.nist.gov](https://nvd.nist.gov) — Fuente primaria para búsqueda de CVEs por producto, versión o ID.
- Microsoft Security Update Guide: [https://msrc.microsoft.com/update-guide/](https://msrc.microsoft.com/update-guide/) — Boletines mensuales Patch Tuesday con severidad y sistemas afectados.

---
*Siguiente: [Controles, Buenas Prácticas y Reporte Técnico](../mitigacion-controles/controles-buenas-practicas.md)*
