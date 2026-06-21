---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Métodos Básicos de Identificación y Priorización de Vulnerabilidades

> **Duración:** 2 horas | **Asignatura:** Gestión de Riesgos y Vulnerabilidades | **Unidad:** Identificación y Evaluación de Vulnerabilidades

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Buscar una entrada CVE en nvd.nist.gov dado su identificador.
- Extraer la severidad CVSS v3.1 (Critical/High/Medium/Low) y los sistemas afectados de una ficha CVE.
- Priorizar la remediación de tres vulnerabilidades usando la regla de severidad → plazo.
- Identificar dos métodos de detección de vulnerabilidades sin herramientas especializadas.

---

## Qué es CVE — El Lenguaje Común de las Vulnerabilidades

Cuando un investigador descubre una falla en Windows, en el firmware de una radio, o en el software de gestión de una red, esa falla necesita un nombre estándar para que todos hablen del mismo problema. Ese nombre es un **CVE**.

!!! note "Definición"
    **CVE (Common Vulnerabilities and Exposures)** es una lista pública de identificadores estandarizados para vulnerabilidades de seguridad conocidas. Cada entrada tiene un ID único con el formato **CVE-AÑO-NÚMERO** (ejemplo: CVE-2024-1234). La lista es mantenida por **MITRE Corporation** y publicada en la **National Vulnerability Database (NVD)** del NIST en: [https://nvd.nist.gov](https://nvd.nist.gov)

**Por qué importa para el Técnico:** Cuando Microsoft publica un parche de seguridad en su boletín mensual, lo referencia con un CVE-ID. El Técnico puede buscar ese ID en nvd.nist.gov y entender exactamente qué falla corrige el parche, qué sistemas están afectados, y qué tan urgente es aplicarlo. Sin el CVE, el Técnico solo sabe que "hay un parche" — con el CVE, sabe si ese parche resuelve un riesgo Crítico o uno de severidad Baja.

---

## Cómo Leer una Entrada CVE

El objetivo no es escribir CVEs — es leer los que ya existen y tomar decisiones con ellos. Cada entrada en nvd.nist.gov tiene siempre los mismos campos. Aprender a leer esos campos es la habilidad central de esta clase.

### Ejemplo: Leer una entrada CVE

**CVE-2024-XXXX** | CVSS Base Score: **8.8 (High)**

| Campo | Contenido |
|-------|-----------|
| **Descripción** | Vulnerabilidad de desbordamiento de búfer en el servicio SMB de Windows que permite ejecución remota de código sin autenticación previa. |
| **Sistemas afectados** | Windows 10 (todas las versiones), Windows 11 (versiones anteriores a 22H2) |
| **Vector de ataque** | Red — accesible desde la red sin requerir interacción del usuario |
| **Puntuación CVSS** | 8.8 — **High (Alta)** → requiere acción en 7 días o menos |
| **Solución disponible** | Sí — parche de seguridad MS24-XXX (Windows Update) |

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

### Ejercicio guiado: Priorizar tres CVEs para la JC-BC3

El Técnico de guardia de la Jefatura de Comunicaciones del Batallón de Comunicaciones N°3 recibe la siguiente lista de vulnerabilidades detectadas en la revisión semanal:

| CVE | Score | Producto afectado | Activos de la JC-BC3 afectados |
|-----|-------|-------------------|---------------------------------|
| CVE-2024-A001 | **9.8 (Critical)** | Windows Server 2016 — servicio RDP | Servidor de archivos de la JC-BC3 (Windows Server 2016) |
| CVE-2024-A002 | **7.5 (High)** | Adobe Acrobat Reader 2020 | 4 terminales C2 con Adobe Acrobat instalado |
| CVE-2024-A003 | **4.3 (Medium)** | Mozilla Firefox versiones anteriores a 120 | 6 laptops de campo (Firefox 118) |

**Pasos de priorización:**

1. **CVE-2024-A001 (Score 9.8, activo servidor de archivos):** Critical en activo compartido que almacena documentos de planificación. Prioridad máxima. Acción: escalar inmediatamente al Jefe de sección, desactivar RDP si no es necesario para la operación, planificar parche de emergencia en 24 horas.

2. **CVE-2024-A002 (Score 7.5, activos terminales C2):** High en activos de misión crítica → elevar a tratamiento Critical. Los terminales C2 no pueden quedar con esta vulnerabilidad durante 7 días — acción en 24 horas. Actualizar Adobe Acrobat en los 4 terminales.

3. **CVE-2024-A003 (Score 4.3, laptops de campo):** Medium en activos administrativos. No es emergencia. Incluir en el plan de mantenimiento del ciclo actual — actualizar Firefox en las 6 laptops dentro de los próximos 30 días.

!!! tip "Respuesta correcta"
    Orden de prioridad: CVE-2024-A001 (Critical, servidor) = CVE-2024-A002 (High elevado a Critical por activo C2) > CVE-2024-A003 (Medium estándar en laptops). Los dos primeros se tratan en paralelo dentro de las 24 horas; el tercero entra al plan mensual.

---

## Resumen

- **CVE** es el identificador estándar de vulnerabilidades. El formato es CVE-AÑO-NÚMERO. La base de datos oficial es [nvd.nist.gov](https://nvd.nist.gov).
- El **score CVSS v3.1** tiene cuatro bandas: Critical (9–10), High (7–8.9), Medium (4–6.9), Low (0.1–3.9). La banda determina el plazo de acción — no el número exacto.
- Los **tres métodos sin herramientas especializadas**: Windows Update para parches pendientes, búsqueda en NVD por producto/versión, y boletines Microsoft Patch Tuesday.
- La **criticidad del activo** puede elevar la prioridad de acción: una vulnerabilidad High en un sistema C2 se trata como Critical.
- El Técnico **no calcula** el score CVSS — lo lee de la ficha y actúa según la banda.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- National Vulnerability Database — NVD: [https://nvd.nist.gov](https://nvd.nist.gov) — Fuente primaria para búsqueda de CVEs por producto, versión o ID.
- Microsoft Security Update Guide: [https://msrc.microsoft.com/update-guide/](https://msrc.microsoft.com/update-guide/) — Boletines mensuales Patch Tuesday con severidad y sistemas afectados.

---
*Siguiente: [Controles, Buenas Prácticas y Reporte Técnico](../mitigacion-controles/controles-buenas-practicas.md)*
