---
# Horas asignadas: 2 hrs
# Tipo: Laboratorio (versión alumno)
---

# Lab: Taller de Auditoría ISO 27001 — Parte 1 (Identificación de Brechas)

> **Duración:** 2 horas | **Unidad:** La Familia ISO 27000

!!! warning "Instrucciones generales"
    - Trabajar en parejas salvo indicación del instructor.
    - Registrar todas las respuestas en papel o cuaderno — esta página sirve como guía visual proyectada por el instructor.
    - No se utiliza ningún software adicional: el escenario está en pantalla y las respuestas van en papel.
    - Si un control del checklist es ambiguo dado el escenario, anotar la duda y continuar con el siguiente.
    - No avanzar al siguiente control sin haber marcado el estado del anterior.

## Objetivos

Al finalizar este laboratorio, el alumno habrá:

- Aplicado un checklist de controles ISO 27001:2022 a un escenario organizacional realista.
- Identificado brechas entre las prácticas actuales de una unidad de comunicaciones y los requisitos de la norma.
- Registrado evidencia observable o descripción de la brecha para cada control evaluado.
- Generado el insumo principal para la sesión siguiente (Parte 2): el listado de brechas a tratar.

---

## Escenario: Jefatura de Comunicaciones del Batallón de Comunicaciones N°3

La **Jefatura de Comunicaciones del Batallón de Comunicaciones N°3** (JC-BC3) es la unidad responsable de mantener las comunicaciones tácticas y estratégicas del batallón durante operaciones. Opera el puesto de mando de comunicaciones, administra las radios tácticas y mantiene los sistemas C2 del batallón.

A continuación se describe la situación actual de la JC-BC3 según información recabada durante una visita de evaluación interna:

- **Equipamiento:** La unidad opera 12 radios tácticas HF/VHF, 3 terminales de sistemas C2, 2 computadoras de escritorio para gestión administrativa y 1 servidor de archivos compartidos sin gestión centralizada.
- **Política de seguridad:** No existe un documento formal de política de seguridad de la información. Las normas de uso de equipos se comunican verbalmente durante la instrucción inicial y no están escritas.
- **Roles:** El Jefe de Comunicaciones asume informalmente la responsabilidad de seguridad, pero no hay un cargo formal designado ni descripción de funciones documentada.
- **Inventario de activos:** No hay un inventario actualizado de activos de información. El registro de equipos físicos existe en una planilla Excel que no se actualiza desde hace 8 meses.
- **Clasificación de la información:** Los documentos se marcan como "RESERVADO" o "NORMAL" sin criterios escritos. Personal nuevo desconoce los criterios de clasificación.
- **Control de acceso:** Las cuentas de Windows del servidor compartido son genéricas (usuario: "admin", contraseña compartida entre todos). No hay cuentas nominales por personal.
- **Concienciación y capacitación:** El personal recibe una charla de inducción de 30 minutos sobre seguridad al ingresar a la unidad. No hay capacitación periódica ni registro de asistencia.
- **Seguridad física:** La sala de equipos tiene llave, pero la porta únicamente el Jefe de Comunicaciones. Cuando el Jefe está de franco, personal sin entrenamiento específico accede usando la llave del guardián, sin registro.
- **Protección contra malware:** Las computadoras tienen Windows Defender activado, pero las actualizaciones automáticas están deshabilitadas "para no interferir con operaciones".
- **Registro de eventos (logging):** No hay política de logging. El servidor de archivos no genera ni conserva registros de acceso.
- **Gestión de incidentes:** No existe un procedimiento formal para reportar ni gestionar incidentes de seguridad. El último incidente conocido (pérdida de datos en una terminal C2) se resolvió reinstalando el sistema sin documentar la causa raíz.

---

## Checklist de Controles ISO 27001

Para cada control, marcar el estado y registrar en la columna derecha: la evidencia que sustenta el estado marcado, o la descripción de la brecha si el control no se cumple.

| N° | Control / Cláusula ISO 27001:2022 | Estado (marcar) | Evidencia observada / Brecha identificada |
|----|-----------------------------------|-----------------|-------------------------------------------|
| 1 | § 5.1 — Política de seguridad de la información documentada y aprobada por la dirección | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 2 | § 5.2 — Roles y responsabilidades de seguridad definidos formalmente y comunicados | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 3 | § 5.9 — Inventario de activos de información documentado y mantenido actualizado | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 4 | § 5.12 — Criterios de clasificación de la información documentados y conocidos por el personal | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 5 | § 5.15 — Control de acceso basado en necesidad de conocer; cuentas nominales por usuario | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 6 | § 5.24 — Procedimiento formal para la gestión de incidentes de seguridad de la información | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 7 | § 5.25 — Incidentes registrados, clasificados y documentados incluyendo causa raíz | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 8 | § 6.3 — Personal recibe capacitación periódica en seguridad con registro de asistencia | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 9 | § 7.1 — Perímetro de seguridad física definido para áreas que contienen activos críticos | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 10 | § 7.2 — Control de acceso físico: solo personal autorizado accede a áreas críticas, con registro | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 11 | § 7.4 — Monitoreo físico de áreas sensibles (cámaras, alarmas o registro de ingreso) | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 12 | § 8.5 — Autenticación segura: contraseñas individuales, no genéricas ni compartidas | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 13 | § 8.7 — Protección contra malware activa y actualizada en todos los sistemas | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 14 | § 8.8 — Gestión de vulnerabilidades: actualizaciones y parches aplicados oportunamente | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 15 | § 8.9 — Configuración de sistemas documentada y gestionada (línea base de configuración) | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 16 | § 8.15 — Registros de eventos (logs) generados, protegidos y conservados | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 17 | § 8.20 — Red con controles que limiten la propagación de incidentes (segmentación básica) | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 18 | § 5.37 — Procedimientos documentados para las operaciones de TI y comunicaciones | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 19 | § 6.8 — Mecanismo para que el personal reporte eventos de seguridad | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |
| 20 | § 5.30 — Plan de continuidad de operaciones de TI ante falla mayor o incidente grave | ☐ Cumple &nbsp; ☐ Parcial &nbsp; ☐ No cumple | |

---

## Entrega

Al finalizar la sesión (2 horas), el alumno entrega en papel:

1. El checklist completo con el estado marcado (Cumple / Parcial / No cumple) para cada uno de los 20 controles.
2. La columna "Evidencia observada / Brecha identificada" completada con al menos una observación concreta por control.
3. Un recuento final: número de controles en estado "Cumple", "Parcial" y "No cumple".

!!! tip "Para la próxima sesión"
    Conservar este checklist completado. Las brechas identificadas hoy (controles en estado "Parcial" o "No cumple") serán el insumo directo de la Parte 2, donde construiremos el plan de tratamiento.

---

<!-- Solución disponible para el instructor en: docs/instructor/lab-auditoria-p1-solucion.md -->

*Continúa en: [Taller de Auditoría (Parte 2)](lab-auditoria-p2.md)*
