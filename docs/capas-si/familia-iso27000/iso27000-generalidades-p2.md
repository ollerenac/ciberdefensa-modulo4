---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# ISO 27000 — Generalidades (Parte 2)

> **Duración:** 2 horas | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Describir el ciclo PDCA y explicar cómo se aplica al Sistema de Gestión de la Seguridad de la Información.
- Distinguir entre postura de seguridad real y cumplimiento normativo (compliance vs. security posture).
- Identificar los beneficios concretos que aporta un SGSI a una organización o unidad militar.

---

> **Recapitulación:** En la clase anterior (Parte 1) cubrimos el origen de los estándares ISO 27000, la estructura de la familia (27000 vocabulario, 27001 requisitos, 27002 controles, 27005 riesgo), y los cuatro componentes fundamentales de un SGSI. Esta clase continúa con el motor que hace funcionar el SGSI: el ciclo PDCA y los beneficios reales que aporta.

---

## El Ciclo PDCA Aplicado al SGSI

Un SGSI no es un proyecto con fecha de inicio y fecha de fin. Es un ciclo permanente de mejora. La herramienta conceptual que describe ese ciclo es el modelo **PDCA** (Plan-Do-Check-Act), también llamado ciclo de Deming por el estadístico W. Edwards Deming, quien lo popularizó en la gestión de calidad en la década de 1950.

ISO 27001 adoptó el ciclo PDCA como el principio organizativo del SGSI porque captura una verdad fundamental: la seguridad de la información en el mundo real nunca está "completa". Las amenazas evolucionan, los sistemas cambian, el personal rota, surgen nuevas vulnerabilidades. Un sistema de gestión que no se revisa y mejora continuamente se vuelve obsoleto.

```
     ┌─────────────────────────────────────────────────────┐
     │                    CICLO PDCA                       │
     │                                                     │
     │   ┌──────────┐          ┌──────────┐               │
     │   │  PLAN    │ ──────▶  │   DO     │               │
     │   │ Planear  │          │ Ejecutar │               │
     │   └──────────┘          └──────────┘               │
     │        ▲                      │                     │
     │        │                      ▼                     │
     │   ┌──────────┐          ┌──────────┐               │
     │   │   ACT    │ ◀──────  │  CHECK   │               │
     │   │  Actuar  │          │ Verificar│               │
     │   └──────────┘          └──────────┘               │
     └─────────────────────────────────────────────────────┘
```

### Las cuatro fases aplicadas a la seguridad de la información

**PLAN — Planear (Cláusulas 4-7 de ISO 27001):**
En esta fase la organización define el alcance del SGSI, identifica sus activos de información, evalúa los riesgos, selecciona los controles que va a implementar y establece los objetivos de seguridad. Es la fase donde se toman las decisiones: ¿qué queremos proteger? ¿de qué amenazas? ¿qué controles son adecuados para nuestro contexto?

**DO — Ejecutar (Cláusula 8 de ISO 27001):**
En esta fase se implementan los controles planificados. Se instalan las herramientas técnicas, se redactan y distribuyen las políticas, se capacita al personal, se configuran los sistemas según los requisitos de seguridad decididos en la fase anterior. Es la fase de acción.

**CHECK — Verificar (Cláusula 9 de ISO 27001):**
En esta fase se mide si los controles funcionan como se esperaba. Incluye auditorías internas (¿se cumplen las políticas?), revisión de métricas de seguridad (¿cuántos incidentes hubo este mes?), y revisión por la dirección (¿el SGSI sigue siendo adecuado para el contexto actual?). Sin verificación, no hay forma de saber si la seguridad real mejoró o solo la documentación.

**ACT — Actuar (Cláusula 10 de ISO 27001):**
En esta fase se corrigen las no-conformidades encontradas en la fase CHECK, se implementan mejoras, y se actualiza el SGSI para reflejar los cambios en el contexto o en los riesgos. Al final de la fase ACT, el ciclo vuelve a comenzar — con un SGSI mejorado que inicia un nuevo ciclo PLAN.

!!! example "Aplicación en entorno castrense"
    La Jefatura de Comunicaciones del Batallón aplica PDCA a la gestión de su material criptográfico:
    **PLAN:** Define qué equipos usan cifrado, qué algoritmos se cargan, con qué frecuencia se renueva el material criptográfico, y qué procedimiento sigue si un equipo se pierde.
    **DO:** Carga el material criptográfico en los radios tácticos según el plan, documenta los equipos que lo recibieron, y capacita al personal en el procedimiento de pérdida de equipo.
    **CHECK:** Realiza una auditoría mensual para verificar que todos los equipos tienen el material correcto y que los registros están al día. Revisa si hubo incidentes de seguridad relacionados con comunicaciones.
    **ACT:** Si la auditoría detecta que un equipo tiene material criptográfico vencido, se corrige inmediatamente y se actualiza el procedimiento para evitar que vuelva a ocurrir.

---

## Beneficios del SGSI

Implementar un SGSI tiene beneficios concretos — no solo para la organización en abstracto, sino para las personas que trabajan en ella.

**Para la dirección / el mando:**
El SGSI proporciona visibilidad del estado real de la seguridad. Sin él, el Comandante no sabe qué activos existen, qué tan vulnerables son, ni si los controles que se dicen implementados realmente funcionan. Con un SGSI, esa información está documentada, medida y reportada.

**Para el personal técnico:**
El SGSI define responsabilidades claras. Los Técnicos saben qué sistemas son de su responsabilidad, qué controles deben mantener, y qué procedimiento seguir cuando algo falla. No hay ambigüedad sobre quién hace qué en un incidente.

**Para la continuidad de la misión:**
Un SGSI incluye planes de continuidad para los activos más críticos. Si un sistema falla durante una operación, el plan de continuidad define cómo se mantiene la misión con recursos alternativos o en modo degradado. La improvisación bajo presión genera errores; el plan documentado reduce esa improvisación.

**Para la respuesta a incidentes:**
Cuando ocurre un incidente de seguridad, la organización tiene un inventario de activos (sabe qué fue afectado), un registro de controles implementados (sabe qué protecciones existían), y un proceso de gestión de incidentes (sabe cómo responder). Sin SGSI, el primer paso de cualquier respuesta es tratar de entender qué hay en el entorno — lo que consume tiempo crítico.

---

## Compliance vs. Security Posture

Esta distinción es una de las más importantes del curso, y una de las más frecuentemente mal entendidas.

**Compliance** (cumplimiento normativo) significa que la organización puede demostrar que cumple con los requisitos de un estándar o regulación. Es un estado documentado y verificable: "tenemos política de contraseñas documentada y aprobada" o "nuestros servidores tienen los parches al día". El compliance se demuestra ante auditores, clientes o reguladores.

**Security posture** (postura de seguridad) significa qué tan bien protegida está realmente la organización frente a amenazas actuales. Es el estado real de seguridad, no el estado documentado.

!!! warning "El peligro del compliance sin postura"
    Una organización puede estar en compliance total con ISO 27001 y tener una postura de seguridad débil. Esto ocurre cuando la documentación existe pero los controles no funcionan en la práctica: la política de contraseñas existe pero nadie la cumple, el plan de respuesta a incidentes está redactado pero nadie lo conoce, las auditorías internas se realizan pero no se corrigen las no-conformidades encontradas. El compliance es condición necesaria pero no suficiente para una buena postura de seguridad.

La relación correcta entre los dos conceptos es: el SGSI es el mecanismo que, cuando funciona bien, hace que el compliance y la postura de seguridad converjan. Un SGSI que solo genera papeles sin mejorar controles reales es burocracia, no seguridad.

Para los Técnicos: su trabajo diario — configurar equipos correctamente, actualizar parches, verificar que los controles funcionan — es lo que crea postura de seguridad real. La documentación del SGSI registra y formaliza ese trabajo, pero no lo sustituye.

---

## Aplicación en Contexto Castrense

**Ejemplo 1 — PDCA y el puesto de mando avanzado:**
Un puesto de mando avanzado desplegado en campo aplica el ciclo PDCA a sus comunicaciones seguras aunque no lo llame "SGSI". PLAN: antes del despliegue, define qué canales serán cifrados, quién tiene qué radio, y cuáles son los procedimientos de comunicación en caso de interferencia. DO: despliega con los equipos configurados según el plan. CHECK: cada 24 horas, el Técnico de comunicaciones verifica que todos los equipos funcionan correctamente y que no hay señales de interferencia activa. ACT: si detecta que un canal está comprometido o interferido, ejecuta el procedimiento de cambio de frecuencia y actualiza el plan para los días siguientes. Este ciclo no tiene nombre formal, pero es PDCA aplicado a la seguridad operacional.

**Ejemplo 2 — Compliance vs. postura en sistemas C2:**
Una unidad puede tener documentado en su SOP (Standard Operating Procedure) que todos los sistemas C2 deben tener contraseña de acceso de 12 caracteres mínimo — eso es compliance con una política interna. Pero si en la práctica los operadores usan contraseñas simples para no tener que reescribleas durante operaciones largas, la postura de seguridad real es deficiente pese al cumplimiento formal. Un SGSI que funciona detecta esta brecha en la fase CHECK mediante auditorías reales, y la corrige en la fase ACT.

---

## Resumen

1. El ciclo **PDCA** (Plan-Do-Check-Act) es el principio organizativo del SGSI: es un ciclo permanente, no un proyecto con fecha de fin.
2. Cada fase del PDCA corresponde a cláusulas de ISO 27001: PLAN (cláusulas 4-7), DO (8), CHECK (9), ACT (10).
3. Los beneficios del SGSI incluyen visibilidad para el mando, responsabilidades claras para el personal técnico, continuidad de misión, y respuesta a incidentes más efectiva.
4. **Compliance** es cumplir con los requisitos documentados; **security posture** es el nivel de protección real. El SGSI que funciona bien hace que ambos converjan.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Ciclo PDCA — Wikipedia:** https://es.wikipedia.org/wiki/Ciclo_de_Deming — Historia del modelo y sus aplicaciones en gestión de calidad y seguridad.
- **ISO 27001 y PDCA — ISO.org:** https://www.iso.org/isoiec-27001-information-security.html — Descripción oficial del estándar con referencia al ciclo de mejora continua.
- **Compliance vs. Security Posture (SANS):** https://www.sans.org/blog/compliance-vs-security/ — Artículo técnico en inglés que profundiza en la distinción con casos de estudio.

---

*Siguiente: [ISO 27001 — Requisitos (Parte 1)](iso27001-requisitos-p1.md)*
