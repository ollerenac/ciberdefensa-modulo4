---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab Auditoría ISO 27001 — Parte 1 (Identificación de Brechas)

!!! danger "Solo para instructores"
    Este documento contiene la rúbrica completa con rangos de respuesta aceptables,
    criterios de corrección y notas de timing para dictar el lab. No mostrar a los alumnos.
    Está excluido del nav público de MkDocs pero es visible en el repositorio GitHub.

---

## Rúbrica de Evaluación por Control

La rúbrica tiene tres niveles por control (D-09). No hay una respuesta modelo única: la auditoría ISO admite interpretación basada en el escenario. Puntaje máximo: 20 puntos (1 pt por control).

| N° | Control | Respuesta aceptable (Cumple — 1 pt) | Respuesta parcialmente aceptable (0.5 pt) | No cumple (0 pt) | Puntos |
|----|---------|--------------------------------------|-------------------------------------------|-----------------|--------|
| 1 | § 5.1 Política de seguridad | Alumno marca "No cumple" y describe que no existe documento escrito / las normas son verbales | Alumno marca "Parcial" y menciona que hay normas pero no documentadas | Alumno marca "Cumple" sin justificación, o no completa la columna de evidencia | 1 pt |
| 2 | § 5.2 Roles y responsabilidades | Alumno marca "Parcial" o "No cumple" y señala que el Jefe de Comunicaciones actúa informalmente sin cargo formal ni descripción de funciones | Menciona que hay un responsable informal pero no cita la ausencia de documentación | Marca "Cumple" porque "alguien se hace cargo" sin evaluar la formalidad | 1 pt |
| 3 | § 5.9 Inventario de activos | Alumno marca "Parcial" o "No cumple" e indica que la planilla Excel existe pero no está actualizada (8 meses de antigüedad) | Menciona que hay un inventario sin citar que está desactualizado | Marca "Cumple" porque "existe una planilla" | 1 pt |
| 4 | § 5.12 Clasificación de la información | Alumno marca "Parcial" e indica que hay marcas (RESERVADO/NORMAL) pero sin criterios escritos y que el personal nuevo desconoce los criterios | Menciona que hay clasificación pero no cita la ausencia de criterios escritos | Marca "Cumple" porque "los documentos tienen marca" | 1 pt |
| 5 | § 5.15 Control de acceso | Alumno marca "No cumple" e identifica las cuentas genéricas compartidas (usuario "admin", contraseña compartida) como brecha de control de acceso | Menciona el problema de contraseñas compartidas sin citar el control de acceso nominal | Marca "Cumple" o no describe la brecha de cuentas genéricas | 1 pt |
| 6 | § 5.24 Procedimiento de incidentes | Alumno marca "No cumple" e indica que no existe procedimiento formal de gestión de incidentes | Menciona que los incidentes se resuelven informalmente sin citar ausencia de procedimiento | Marca "Cumple" porque "los incidentes se resuelven" | 1 pt |
| 7 | § 5.25 Registro de incidentes | Alumno marca "No cumple" y cita que el incidente de la terminal C2 se resolvió reinstalando sin documentar causa raíz | Menciona que no se documenta pero no cita el incidente específico del escenario | Marca "Cumple" porque "el problema se arregló" | 1 pt |
| 8 | § 6.3 Capacitación periódica | Alumno marca "Parcial" e indica: hay charla de inducción (30 min) pero no hay capacitación periódica ni registro de asistencia | Menciona que hay capacitación inicial pero no cita ausencia de periodicidad ni de registro | Marca "Cumple" porque "hay una charla al entrar" | 1 pt |
| 9 | § 7.1 Perímetro físico | Alumno marca "Parcial" e indica que la sala tiene llave (perímetro existe) pero el control es deficiente (acceso fuera de horario sin control adecuado) | Menciona la llave sin evaluar el control de acceso en ausencia del Jefe | Marca "No cumple" sin reconocer que existe algún perímetro | 1 pt |
| 10 | § 7.2 Control de acceso físico | Alumno marca "No cumple" o "Parcial" e indica que personal no autorizado (guardián) accede a la sala sin registro cuando el Jefe está de franco | Menciona el problema de la llave sin describir el acceso no registrado | Marca "Cumple" porque "la sala tiene llave" | 1 pt |
| 11 | § 7.4 Monitoreo físico | Alumno marca "No cumple" e indica que no hay cámaras, alarmas ni registro de ingreso mencionados en el escenario | Menciona ausencia de monitoreo sin detallar qué tipo | Marca "Cumple" sin evidencia del escenario | 1 pt |
| 12 | § 8.5 Autenticación segura | Alumno marca "No cumple" e identifica cuentas genéricas con contraseña compartida como incumplimiento de autenticación individual | Menciona que las contraseñas son compartidas sin ligar al control de autenticación | Marca "Cumple" porque "los sistemas requieren contraseña" | 1 pt |
| 13 | § 8.7 Protección contra malware | Alumno marca "Parcial" e indica: Windows Defender está activo (parcial cumplimiento) pero las actualizaciones están deshabilitadas (brecha) | Menciona Defender activo sin señalar las actualizaciones deshabilitadas | Marca "Cumple" porque "tienen antivirus" o "No cumple" sin reconocer que existe protección básica | 1 pt |
| 14 | § 8.8 Gestión de vulnerabilidades | Alumno marca "No cumple" e indica que las actualizaciones automáticas están deshabilitadas para "no interferir con operaciones" | Menciona actualizaciones deshabilitadas sin explicar la brecha de gestión de vulnerabilidades | Marca "Cumple" porque "el sistema tiene Windows actualizado" (sin evidencia) | 1 pt |
| 15 | § 8.9 Configuración documentada | Alumno marca "No cumple" e indica que no se menciona ninguna línea base de configuración ni procedimiento de gestión de configuración en el escenario | Menciona ausencia de documentación sin ligar al concepto de línea base | Marca "Cumple" sin evidencia del escenario | 1 pt |
| 16 | § 8.15 Logging | Alumno marca "No cumple" e indica que no hay política de logging y el servidor de archivos no genera registros de acceso | Menciona ausencia de logs sin describir cuál sistema específico no tiene registros | Marca "Cumple" porque "el sistema operativo genera eventos" (sin evidencia de que estén activados) | 1 pt |
| 17 | § 8.20 Segmentación de red | Alumno marca "No cumple" o "Parcial" e indica que no se menciona segmentación en el escenario; los sistemas C2, administrativos y el servidor comparten la misma red sin evidencia de control | Menciona ausencia de segmentación sin identificar los sistemas expuestos | Marca "Cumple" sin evidencia del escenario | 1 pt |
| 18 | § 5.37 Procedimientos operacionales documentados | Alumno marca "No cumple" e indica que el escenario no menciona procedimientos escritos para las operaciones de TI o comunicaciones | Menciona ausencia de documentación sin ligar a procedimientos operacionales | Marca "Cumple" porque "el personal sabe qué hacer" | 1 pt |
| 19 | § 6.8 Reporte de eventos | Alumno marca "No cumple" e indica que no existe mecanismo formal de reporte (el incidente de la terminal C2 no fue reportado formalmente) | Menciona ausencia de canal de reporte sin citar evidencia del escenario | Marca "Cumple" porque "el Jefe escucha los problemas" | 1 pt |
| 20 | § 5.30 Continuidad de operaciones | Alumno marca "No cumple" e indica que no se menciona ningún plan de continuidad; el incidente de la terminal C2 implicó reinstalación sin plan previo | Menciona ausencia de plan sin citar el incidente como evidencia | Marca "Cumple" sin evidencia del escenario | 1 pt |

---

## Errores Comunes y Cómo Manejarlos en Clase

| Error frecuente | Causa probable | Qué decirle al alumno |
|-----------------|---------------|----------------------|
| Marcar "Cumple" porque "algo existe" (ej. planilla Excel, llave en la puerta) | El alumno evalúa existencia en lugar de eficacia del control | "El control ISO no pregunta si algo existe, sino si funciona y está gestionado. Una planilla sin actualizar no cumple el inventario." |
| Confundir § 5.15 (control de acceso lógico) con § 7.2 (control de acceso físico) | Ambos controles se activan con el mismo escenario de cuentas/llave | "§ 5.15 habla de cuentas de usuario en sistemas. § 7.2 habla de quién entra a la sala física. Pueden estar relacionados pero son controles distintos." |
| Dejar la columna de evidencia en blanco | El alumno no sabe qué escribir | "Copia una frase del escenario que justifique tu marca. Si marcas 'No cumple', ¿qué en el escenario te indica que no cumple?" |
| Marcar § 8.13 "Parcial" porque "Windows Defender está activo" pero sin citar actualizaciones | El alumno no lee el escenario completo | "Vuelve al escenario: ¿qué dice sobre las actualizaciones de Defender?" |
| Recuento final incorrecto (no suman 20) | Error de conteo | Pedir al alumno que cuente fila a fila en voz alta |

---

## Notas de Dictado (timing sugerido LAB-06: 2 horas)

| Bloque | Actividad | Tiempo |
|--------|-----------|--------|
| Apertura | Presentar el escenario JC-BC3 (leer en voz alta, aclarar dudas sobre activos y prácticas descritas) | 15 min |
| Intro al checklist | Explicar los tres estados posibles (Cumple/Parcial/No cumple) y la columna de evidencia | 5 min |
| Trabajo en parejas — controles 1-10 | Alumnos completan la primera mitad del checklist; el instructor circula y orienta | 30 min |
| Pausa de revisión | El instructor revisa controles 1-5 con la clase (preguntar a dos o tres parejas su respuesta y discutir) | 10 min |
| Trabajo en parejas — controles 11-20 | Alumnos completan la segunda mitad | 25 min |
| Cierre y recuento | Cada pareja cuenta sus resultados; el instructor pregunta cuántas brechas encontraron y discute divergencias | 15 min |
| **Total** | | **100 min (~2 hrs)** |

!!! tip "Consejo de dictado"
    Si el grupo termina antes, pedir que hagan el recuento por área temática: "¿cuántas brechas son físicas? ¿cuántas son de gestión? ¿cuántas son técnicas?" Esto prepara el terreno para la priorización de la Parte 2.
