---
# Horas asignadas: 3 hrs
# Tipo: Laboratorio (versión alumno)
---

# Lab: Taller de Auditoría ISO 27001 — Parte 2 (Plan de Tratamiento)

> **Duración:** 3 horas | **Unidad:** La Familia ISO 27000

En la sesión anterior (Parte 1) identificamos las brechas de cumplimiento en la **Jefatura de Comunicaciones del Batallón de Comunicaciones N°3** (JC-BC3). En esta sesión construiremos el plan de tratamiento de esas brechas: priorizaremos según riesgo, seleccionaremos controles ISO 27002 y asignaremos responsables y plazos.

!!! warning "Instrucciones generales"
    - Trabajar en las mismas parejas de la Parte 1.
    - Usar el checklist completado en la sesión anterior como insumo. Si no se completó, el instructor proporcionará un checklist de referencia.
    - Registrar todas las respuestas en papel o cuaderno — esta página sirve como guía visual.
    - No se utiliza ningún software adicional.
    - El objetivo no es el plan perfecto, sino aplicar el proceso ISO 27005 a un caso real.

## Objetivos

Al finalizar este laboratorio, el alumno habrá:

- Clasificado las brechas identificadas en Parte 1 por nivel de prioridad (alto / medio / bajo).
- Seleccionado al menos un control ISO 27002:2022 por cada brecha de prioridad alta o media.
- Elaborado un plan de tratamiento con acción, responsable, plazo y prioridad para cada brecha.
- Producido un informe de brechas resumido con los resultados del taller.

---

## Escenario: Jefatura de Comunicaciones del Batallón de Comunicaciones N°3

La **Jefatura de Comunicaciones del Batallón de Comunicaciones N°3** (JC-BC3) es la misma unidad evaluada en la Parte 1. Los controles que resultaron en estado "Parcial" o "No cumple" en el checklist de la sesión anterior son el punto de partida de esta sesión.

!!! note "Recordatorio del escenario"
    La JC-BC3 opera 12 radios tácticas HF/VHF, 3 terminales de sistemas C2, 2 computadoras administrativas y 1 servidor de archivos compartidos. Las principales brechas identificadas en Parte 1 incluían: ausencia de política de seguridad, cuentas genéricas, logging desactivado, sin procedimiento de incidentes y sin capacitación periódica.

---

## Repaso de Brechas Identificadas

Antes de iniciar el plan de tratamiento, revisar en el checklist de Parte 1 cuántos controles resultaron en cada categoría:

| Categoría | Cantidad (completar con los resultados propios) |
|-----------|------------------------------------------------|
| Cumple | ___ controles |
| Parcial | ___ controles |
| No cumple | ___ controles |
| **Total brechas a tratar (Parcial + No cumple)** | **___ controles** |

Las brechas a tratar son todos los controles en estado "Parcial" o "No cumple". Si el número es mayor a 15, el instructor indicará cuáles priorizar para esta sesión.

---

## Criterios de Priorización

Para cada brecha, asignar una prioridad usando los siguientes criterios:

| Prioridad | Criterio |
|-----------|---------|
| **Alta** | La ausencia del control expone directamente la confidencialidad, integridad o disponibilidad de activos críticos (radios, sistemas C2). El riesgo es inmediato y el impacto operacional sería grave. |
| **Media** | La ausencia del control aumenta la superficie de ataque o dificulta la respuesta a incidentes, pero no implica exposición directa inmediata. El riesgo es real pero tolerable a corto plazo si se gestiona. |
| **Baja** | La ausencia del control es una carencia de madurez organizacional (documentación, procesos) sin exposición técnica inmediata. Se puede planificar con menor urgencia. |

---

## Elaboración del Informe de Brechas

Para cada control en estado "Parcial" o "No cumple", completar una fila del informe de brechas:

| N° Control | Cláusula | Descripción de la brecha | Impacto potencial | Prioridad (A/M/B) |
|-----------|----------|--------------------------|-------------------|-------------------|
| (ej.) 1 | § 5.1 | No existe política de seguridad documentada | Personal desconoce las normas; imposible auditar cumplimiento | Alta |
| | | | | |
| | | | | |
| | | | | |

Completar una fila por cada brecha identificada en la Parte 1.

---

## Plan de Tratamiento

Para cada brecha de prioridad **Alta** o **Media**, completar una fila del plan de tratamiento con la acción concreta que la organización debe implementar:

| N° | Brecha | Control ISO 27002:2022 | Acción correctiva | Responsable sugerido | Plazo sugerido | Prioridad |
|----|--------|------------------------|-------------------|----------------------|----------------|-----------|
| 1 | Sin política de seguridad (§ 5.1) | § 5.1 Políticas de seguridad de la información | Redactar y aprobar una política de seguridad básica que cubra uso aceptable de equipos, contraseñas y reporte de incidentes | Jefe de Comunicaciones + Oficial de Seguridad | 30 días | Alta |
| 2 | Cuentas genéricas compartidas (§ 5.15 / § 8.5) | § 5.15 Control de acceso, § 8.5 Autenticación segura | Crear cuentas nominales individuales en el servidor; desactivar cuenta "admin" genérica; establecer política de contraseñas | Técnico TI de la unidad | 15 días | Alta |
| 3 | Logging desactivado (§ 8.15) | § 8.15 Logging | Activar registro de eventos en el servidor de archivos; configurar retención mínima de 90 días | Técnico TI de la unidad | 15 días | Alta |
| 4 | Sin procedimiento de incidentes (§ 5.24) | § 5.24 Gestión de incidentes de seguridad de la información | Redactar un procedimiento simple de 1 página: cómo reportar, quién recibe el reporte, pasos de contención inicial | Jefe de Comunicaciones | 30 días | Alta |
| 5 | Actualizaciones deshabilitadas (§ 8.8) | § 8.8 Gestión de vulnerabilidades técnicas | Programar ventana de mantenimiento mensual para aplicar actualizaciones; documentar la decisión de excluir horarios operacionales | Técnico TI de la unidad | 30 días | Media |
| 6 | Sin capacitación periódica (§ 6.3) | § 6.3 Concienciación, educación y formación | Planificar charla trimestral de 1 hora sobre seguridad; registrar asistencia | Jefe de Comunicaciones | 60 días | Media |
| (completar con las brechas propias) | | | | | | |

!!! tip "Guía para la acción correctiva"
    Una buena acción correctiva responde a tres preguntas: ¿Qué se va a hacer exactamente? ¿Quién es el responsable de ejecutarlo? ¿Para cuándo debe estar listo? Sin esas tres respuestas, la acción no es ejecutable.

---

## Entrega

Al finalizar la sesión (3 horas), el alumno entrega en papel:

1. El informe de brechas completo con prioridad asignada a cada brecha.
2. El plan de tratamiento con al menos una acción por cada brecha de prioridad Alta y Media.
3. Un párrafo de conclusión: ¿cuántas brechas tiene la JC-BC3? ¿Cuál es la brecha más crítica y por qué?

---

<!-- Solución disponible para el instructor en: docs/instructor/lab-auditoria-p2-solucion.md -->

*Continúa en: [Examen Parcial — Capas SI](examen-parcial.md)*
