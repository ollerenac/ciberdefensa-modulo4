---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# ISO 27005 — Gestión del Riesgo (Parte 2)

> **Duración:** 1 hora | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Calcular el nivel de riesgo usando la fórmula Probabilidad × Impacto en una matriz 3×3.
- Seleccionar la opción de tratamiento adecuada para un riesgo dado.
- Construir una fila básica de plan de tratamiento de riesgos.

---

> **Recapitulación:** En la clase anterior (Parte 1) cubrimos el proceso completo de gestión del riesgo de ISO 27005, el inventario de activos con criterios CIA, el catálogo de amenazas, y la relación activo-amenaza-vulnerabilidad. Esta clase completa el proceso: cómo calcular el nivel de riesgo y qué hacer con él.

---

## Evaluación del Riesgo: Cuantitativa vs. Cualitativa

Una vez identificados los riesgos (activo + amenaza + vulnerabilidad), la siguiente etapa es evaluar su magnitud — qué tan grave es cada riesgo. Existen dos enfoques:

**Evaluación cuantitativa:**
Expresa el riesgo en valores numéricos concretos, generalmente monetarios. "La probabilidad de que el enlace satelital falle es del 15% anual; el impacto estimado es una interrupción de 4 horas con un costo operacional de X miles de dólares." Este enfoque es preciso pero requiere datos históricos detallados y es difícil de aplicar en entornos militares donde el costo de un incidente no se mide solo en dinero.

**Evaluación cualitativa:**
Expresa el riesgo en categorías (Alto / Medio / Bajo) usando escalas descriptivas. Es menos precisa que la cuantitativa, pero es práctica, rápida, y funciona bien cuando no hay datos históricos suficientes. Este es el enfoque más común en implementaciones iniciales de gestión de riesgos — incluyendo en organizaciones militares.

!!! tip "Para organizaciones que comienzan con la gestión de riesgos"
    La evaluación cualitativa con una matriz 3×3 o 5×5 es el punto de entrada recomendado. Una vez que la organización tiene experiencia y datos históricos, puede evolucionar hacia enfoques más cuantitativos para los riesgos más críticos.

En esta clase usamos una **matriz de riesgo 3×3** con escala cualitativa.

---

## Las 4 Opciones de Tratamiento del Riesgo

Después de evaluar cada riesgo, la organización debe decidir qué hacer con él. ISO 27005 define cuatro opciones:

| Opción | Cuándo se usa | Ejemplo militar |
|--------|--------------|-----------------|
| **Mitigar (Modificar)** | El riesgo es inaceptable y se pueden implementar controles para reducir su probabilidad o impacto | Riesgo: captura de radio con material criptográfico → mitigación: implementar procedimiento de borrado de emergencia |
| **Aceptar (Retener)** | El riesgo está dentro del apetito de riesgo de la organización, o el costo de mitigarlo supera el beneficio | Riesgo: fallo de una radio de respaldo durante operación → aceptado: la radio principal tiene redundancia suficiente para la misión |
| **Transferir (Compartir)** | El riesgo puede trasladarse a un tercero que lo gestione mejor — seguros, contratos, acuerdos | Riesgo: fallo del enlace satelital comercial arrendado → transferido: el contrato incluye SLA de disponibilidad y compensación por fallos |
| **Evitar (Eliminar)** | La actividad que crea el riesgo no es necesaria y puede eliminarse | Riesgo: acceso a internet desde terminales del sistema C2 → evitado: los terminales se configuran sin conectividad a internet |

!!! note ""
    En la práctica, la mayoría de los riesgos significativos se tratan con **Mitigar** — implementar controles para reducirlos. La aceptación se usa para riesgos residuales bajos. La transferencia es menos común en entornos militares. La eliminación solo aplica cuando la fuente del riesgo puede suprimirse completamente sin afectar la misión.

---

## Ejercicio en Clase: Construyendo una Matriz de Riesgo

El instructor asignará un escenario con un activo y una amenaza. Cada alumno construye su propia evaluación de riesgo en papel siguiendo este procedimiento:

**Paso 1:** Identificar el activo y la amenaza asignados por el instructor.

**Paso 2:** Identificar una vulnerabilidad del activo frente a esa amenaza.

**Paso 3:** Estimar la **Probabilidad** de que la amenaza se materialice:
- 1 = Baja (ocurre raramente en entornos similares)
- 2 = Media (ocurre ocasionalmente — ha ocurrido en otras unidades)
- 3 = Alta (es probable que ocurra durante la operación)

**Paso 4:** Estimar el **Impacto** si la amenaza se materializa:
- 1 = Bajo (efecto menor, misión continúa sin degradación significativa)
- 2 = Medio (degradación notable de la capacidad operacional)
- 3 = Alto (impacto grave — misión comprometida o imposible de continuar)

**Paso 5:** Calcular el **Nivel de Riesgo** = Probabilidad × Impacto.

**Paso 6:** Determinar la opción de tratamiento (Mitigar / Aceptar / Transferir / Evitar) y proponer un control concreto.

### Matriz de referencia

| Probabilidad ↓ \ Impacto → | Bajo (1) | Medio (2) | Alto (3) |
|-----------------------------|----------|-----------|----------|
| **Alta (3)**                | 3 — MEDIO | 6 — ALTO | 9 — CRÍTICO |
| **Media (2)**               | 2 — BAJO  | 4 — MEDIO | 6 — ALTO |
| **Baja (1)**                | 1 — BAJO  | 2 — BAJO  | 3 — MEDIO |

*Nivel de riesgo = Probabilidad × Impacto*

**Criterios de decisión sugeridos:**
- 1-2 (BAJO): Aceptar, con revisión en el próximo ciclo
- 3-4 (MEDIO): Mitigar o aceptar con documentación de la decisión
- 6 (ALTO): Mitigar — implementar controles en el corto plazo
- 9 (CRÍTICO): Mitigar o evitar — acción inmediata requerida

### Escenario de ejemplo (para práctica del instructor)

**Activo:** Radio táctico con material criptográfico vigente — asignado a patrulla de reconocimiento de largo alcance.

**Amenaza:** Captura del equipo por fuerzas adversarias durante la operación.

**Vulnerabilidad:** No existe procedimiento documentado de borrado de emergencia del material criptográfico; el operador no ha sido entrenado en el procedimiento.

*El alumno completa los Pasos 3-6 en papel:*

*Probabilidad: _____ (1/2/3) — Justificación: _____________________*

*Impacto: _____ (1/2/3) — Justificación: _____________________*

*Nivel de riesgo: _____ × _____ = _____ (___ BAJO / MEDIO / ALTO / CRÍTICO)*

*Tratamiento seleccionado: ☐ Mitigar  ☐ Aceptar  ☐ Transferir  ☐ Evitar*

*Control propuesto: ___________________________________________________*

---

## El Plan de Tratamiento de Riesgos

El resultado del ejercicio anterior — activo, amenaza, nivel de riesgo, tratamiento, control propuesto — es una fila del **Plan de Tratamiento de Riesgos**. El plan completo documenta todas las decisiones de tratamiento y es el documento que la organización usa para gestionar la reducción de sus riesgos en el tiempo.

Un plan de tratamiento básico tiene esta estructura:

| Riesgo | Nivel | Tratamiento | Control / Acción | Responsable | Fecha límite | Estado |
|--------|-------|-------------|-----------------|-------------|-------------|--------|
| Captura de radio con material criptográfico | CRÍTICO | Mitigar | Implementar procedimiento de borrado de emergencia y entrenar a todos los operadores | Oficial de Comunicaciones | Antes del próximo ejercicio de campo | En proceso |
| Fallo de alimentación eléctrica en puesto de mando | ALTO | Mitigar | Adquirir y verificar generador de respaldo para sala de comunicaciones | Sección de Logística | 30 días | Pendiente |

El Plan de Tratamiento de Riesgos se actualiza en la fase ACT del ciclo PDCA — cuando los controles se implementan, el estado cambia, y en la siguiente evaluación de riesgos se verifica que el nivel de riesgo disminuyó.

---

## Aplicación en Contexto Castrense

**Ejemplo — De la evaluación al tratamiento en el puesto de mando:**
La Jefatura de Comunicaciones del Batallón evalúa el riesgo de interferencia electrónica de su enlace de datos táctico. Probabilidad: Alta (3) — la zona de operaciones tiene antecedentes de uso de bloqueadores electrónicos por el adversario. Impacto: Alto (3) — sin el enlace de datos, el sistema C2 pierde la imagen operacional en tiempo real. Nivel de riesgo: 9 — CRÍTICO.

Opciones de tratamiento:
- **Evitar:** No es posible — el enlace de datos es esencial para la misión.
- **Transferir:** No aplica — no hay tercero que gestione este riesgo.
- **Aceptar:** Inaceptable dado el nivel CRÍTICO.
- **Mitigar:** La opción correcta. Control propuesto: instalar un canal de comunicación redundante (radio de voz HF cifrada como respaldo) que permita continuar las comunicaciones de mando aunque el enlace de datos sea bloqueado. Responsable: Oficial de Comunicaciones. Fecha: antes del despliegue.

Este es exactamente el proceso de la Parte 2 de ISO 27005 aplicado a un escenario real.

---

## Resumen

1. La evaluación cualitativa del riesgo usa una **matriz de probabilidad × impacto** — el nivel de riesgo es el producto de ambas dimensiones (escala 1-9 en matriz 3×3).
2. Las **4 opciones de tratamiento** son: Mitigar (implementar controles), Aceptar (dentro del apetito de riesgo), Transferir (a un tercero) y Evitar (eliminar la fuente del riesgo).
3. El **Plan de Tratamiento de Riesgos** documenta qué se va a hacer con cada riesgo inaceptable: qué control, quién es responsable, cuándo se implementa, y cómo se verifica.
4. El proceso de gestión del riesgo es cíclico — se repite cuando cambia el contexto, se incorporan nuevos activos o se detectan nuevas amenazas.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Matriz de riesgo ISO 27005 — plantilla (Advisera):** https://advisera.com/27001academy/knowledgebase/iso-27001-risk-assessment-template/ — Plantilla descargable de evaluación de riesgos en formato hoja de cálculo.
- **Las 4 opciones de tratamiento explicadas (ISO27001Security):** https://www.iso27001security.com/html/27005.html — Descripción detallada de cada opción con casos de uso.
- **ISO 27005:2022 — qué cambió respecto a 2018 (IT Governance):** https://www.itgovernance.co.uk/iso27005-2022 — Resumen de los cambios introducidos en la versión 2022, incluyendo el enfoque basado en escenarios.

---

*Continúa en: [Examen Parcial — Capas SI](examen-parcial.md)*
