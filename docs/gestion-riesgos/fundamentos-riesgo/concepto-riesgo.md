---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Concepto de Riesgo: Amenazas, Vulnerabilidades e Impacto

> **Duración:** 2 horas | **Asignatura:** Gestión de Riesgos y Vulnerabilidades | **Unidad:** Fundamentos de Gestión de Riesgos

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Diferenciar riesgo, amenaza, vulnerabilidad e impacto en contexto operacional.
- Construir una fila de matriz de riesgo para un activo militar dado.
- Seleccionar la opción de tratamiento apropiada para un riesgo de nivel Alto.

---

## Puente con la Asignatura Anterior

En la Asignatura 2 estudiaron el proceso de gestión del riesgo según ISO 27005:2022 — el ciclo completo, el risk register, las cuatro opciones de tratamiento (mitigar, aceptar, transferir, evitar), y la diferencia entre apetito y tolerancia al riesgo. Conocen el marco teórico.

En esta asignatura aplican esos conceptos de forma más directa y operacional, con ejemplos concretos del entorno militar y con énfasis en la acción, no en el proceso. No se vuelve a explicar el ciclo ISO 27005 paso a paso — se da por entendido. Lo que se agrega es el criterio operacional: **cómo razona un Técnico de comunicaciones cuando está frente a un riesgo real.**

---

## Los Cuatro Conceptos Fundamentales

Antes de llenar una matriz, el Técnico debe poder separar cuatro conceptos que en la práctica aparecen entremezclados. La analogía militar primero, la definición formal después.

**Analogía:** Una radio táctica HF que no ha recibido actualización de firmware en 14 meses. Hay un actor hostil que sabe que ese modelo de radio tiene una falla conocida y puede explotarla para interrumpir comunicaciones durante una operación.

!!! note "Riesgo"
    La **probabilidad de que una amenaza explote una vulnerabilidad y cause un impacto negativo** en la misión, el personal o los sistemas. Riesgo = Probabilidad × Impacto. No es el atacante, no es la falla — es la combinación de ambos en un contexto dado.

    En el ejemplo: el riesgo es que las comunicaciones tácticas fallen durante la operación porque un actor hostil explote la vulnerabilidad de firmware antes de que se aplique el parche.

!!! note "Amenaza"
    Un **agente o evento con capacidad y posible intención de causar daño** a un activo de información. La amenaza existe independientemente de que exista una vulnerabilidad — pero sin vulnerabilidad, no puede materializarse.

    En el ejemplo: el actor hostil o el malware que explota vulnerabilidades en firmware de radios HF es la amenaza.

!!! note "Vulnerabilidad"
    Una **debilidad en un activo o control** que puede ser explotada por una amenaza. La vulnerabilidad sola no genera riesgo — se convierte en riesgo cuando hay una amenaza capaz de aprovecharla.

    En el ejemplo: el firmware sin actualizar desde hace 14 meses es la vulnerabilidad.

!!! note "Impacto"
    La **consecuencia negativa sobre la misión** si el riesgo se materializa. En entornos militares, el impacto se mide principalmente en disponibilidad operacional, integridad de la información clasificada, y seguridad del personal.

    En el ejemplo: la pérdida de comunicación táctica durante la operación, con posible exposición de posiciones propias.

### La Relación entre los Cuatro Conceptos

```
Amenaza + Vulnerabilidad → RIESGO → Impacto
```

La fórmula **Riesgo = Probabilidad × Impacto** que estudiaron en la Asignatura 2 sigue siendo válida. Lo que se añade aquí es el criterio militar para estimar cada factor cuando no hay datos históricos precisos.

---

## La Matriz de Riesgo en la Práctica

Ya conocen qué es una matriz de riesgo. Lo que se practica ahora es llenarla con razonamiento visible — no solo asignar categorías, sino justificar cada celda.

La siguiente matriz usa activos reales de una unidad de comunicaciones. La escala: **Alta (A) / Media (M) / Baja (B)** para probabilidad e impacto.

| # | Activo | Amenaza | Vulnerabilidad | Prob. | Impacto | Nivel de Riesgo | Razonamiento |
|---|--------|---------|----------------|-------|---------|-----------------|--------------|
| 1 | Sistema C2 del puesto de mando | Malware / ransomware | Sin antivirus actualizado; puertos USB habilitados | Alta | Alta | **Alto** | El C2 es crítico para la misión. Un malware que lo inhabilite detiene el mando. Probabilidad alta porque el vector USB es frecuente en unidades sin política de medios removibles. |
| 2 | Servidor de archivos de la unidad | Acceso no autorizado | Contraseñas compartidas entre turnos | Media | Media | **Medio** | Almacena información sensible pero no es el nodo central de mando. Probabilidad media: el acceso compartido es un riesgo latente, no un ataque activo en curso. |
| 3 | Laptops de campo del puesto de mando avanzado | Robo o extravío | Sin cifrado de disco; sin bloqueo automático | Media | Alta | **Alto** | Las laptops de campo salen del perímetro controlado. Sin cifrado, cualquier persona con acceso físico puede leer los archivos. Impacto alto si contienen planes de operaciones. |

### Regla para Calcular el Nivel de Riesgo

| Probabilidad \ Impacto | Alta | Media | Baja |
|-------------------------|------|-------|------|
| **Alta** | Alto | Alto | Medio |
| **Media** | Alto | Medio | Bajo |
| **Baja** | Medio | Bajo | Bajo |

Cuando probabilidad e impacto apuntan a categorías distintas, el nivel de riesgo sube hacia el lado más conservador. En entornos militares, el costo de subestimar un riesgo es mayor que el de sobreestimarlo.

---

## Cuándo Actuar: Priorización Operacional

Las cuatro opciones de tratamiento (mitigar, aceptar, transferir, evitar) son conocidas de la Asignatura 2. Lo que se agrega aquí son los **plazos operacionales** — cuándo actúa el Técnico según el nivel de riesgo detectado:

| Nivel de Riesgo | Acción del Técnico | Plazo |
|-----------------|--------------------|-------|
| **Alto** | Escalar inmediatamente al Jefe de sección. No esperar al siguiente ciclo de revisión. Documentar el hallazgo con fecha y hora. | Dentro de las 24 horas siguientes a la detección |
| **Medio** | Elaborar un plan de mitigación con fecha de implementación. Informar al Jefe en el próximo reporte de turno. | Plan de mitigación en 7 días |
| **Bajo** | Documentar en el registro de riesgos de la unidad. Incluir en la revisión del próximo ciclo periódico. | Documentar y revisar en el ciclo regular de revisión |

!!! tip "Regla práctica"
    Un riesgo Alto no resuelto en 24 horas pasa a ser un riesgo aceptado por omisión — lo cual es diferente a un riesgo aceptado por decisión documentada. La diferencia importa si hay una auditoría posterior.

---

## Aplicación en Contexto Castrense

### Ejemplo 1: Laptops de campo con Windows sin parchear

Un Técnico de comunicaciones en el puesto de mando avanzado descubre que las 6 laptops de campo tienen Windows sin actualizar desde hace 90 días. El panel de Windows Update muestra 12 actualizaciones pendientes, incluyendo 3 marcadas como "Críticas".

**Razonamiento paso a paso:**

1. **Activo:** Laptops de campo — contienen documentos de planificación operacional.
2. **Amenaza:** Malware que explota vulnerabilidades de Windows sin parchear; posible acceso remoto no autorizado.
3. **Vulnerabilidad:** 12 parches pendientes, 3 críticos — cada uno es una falla conocida y publicada.
4. **Probabilidad:** Alta. Las vulnerabilidades críticas de Windows tienen exploits públicos disponibles días o semanas después de su publicación.
5. **Impacto:** Alto. Documentos de planificación operacional comprometidos = fuga de información clasificada, riesgo para el personal.
6. **Nivel de riesgo:** Alto (Probabilidad Alta × Impacto Alto).
7. **Acción:** Escalar al Jefe en las próximas horas. Si hay conectividad, aplicar actualizaciones de inmediato. Si no hay conectividad, aislar las laptops de la red de la unidad hasta que se parcheen. Documentar el hallazgo con fecha y hora.

### Ejemplo 2: Radio táctica con cifrado caducado

Una radio táctica VHF del batallón sigue en uso con material criptográfico (clave de cifrado) vencido hace 3 semanas. El reemplazo no ha llegado — el responsable de abastecimiento reporta un retraso logístico de 5 días adicionales.

**Análisis del riesgo:**

- Las comunicaciones se transmiten con cifrado vencido o predecible — el nivel de confidencialidad real es incierto.
- Las opciones disponibles: **mitigar** (reducir la información transmitida por esa radio al mínimo operacional, nada clasificado hasta que el cifrado sea renovado), **aceptar** (documentado y firmado por el Oficial de Seguridad, con conciencia explícita del riesgo), o **evitar** (usar un canal alternativo con cifrado vigente para comunicaciones sensibles).

**Lección clave:** El Técnico detecta, documenta y escala. No acepta ni transfiere riesgos por iniciativa propia. La decisión de aceptar un riesgo requiere autorización explícita del nivel de mando adecuado.

---

## Resumen

- **Riesgo = Probabilidad × Impacto.** Los cuatro conceptos base son riesgo, amenaza, vulnerabilidad e impacto — cada uno tiene un rol distinto en la ecuación.
- La **matriz de riesgo** no se llena con categorías abstractas: cada celda requiere razonamiento sobre el activo concreto, la amenaza real y el contexto operacional.
- Los **plazos operacionales** son: Alto → escalar en 24 hrs; Medio → plan en 7 días; Bajo → documentar y revisar en el ciclo regular.
- El **Técnico detecta y documenta** — no acepta ni transfiere riesgos por iniciativa propia. Esas decisiones pertenecen al nivel de mando.
- En entornos militares, el **impacto en la misión** es el criterio primario de priorización, no el costo económico.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- NIST SP 800-30 Rev.1 — Guide for Conducting Risk Assessments: [https://csrc.nist.gov/publications/detail/sp/800-30/rev-1/final](https://csrc.nist.gov/publications/detail/sp/800-30/rev-1/final) — Marco de evaluación de riesgos del gobierno de EE.UU., bien documentado y de acceso libre.

---
*Siguiente: [Tipos de Riesgo en Entornos Militares](tipos-riesgo-militar.md)*
