---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Defensa en Profundidad — Capa de Activos Críticos (CONT-27)

> Esta clase cubre **Capa 7: Activos Críticos** del modelo de defensa en profundidad establecido en [Parte 1](capa-humana-perimetral-p1.md).

> **Duración:** 2 horas | **Asignatura:** Capas SI | **Unidad:** Defensa en Profundidad

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Identificar los activos críticos característicos de un entorno militar de comunicaciones.
- Aplicar criterios de criticidad operacional para priorizar la protección de activos.
- Describir los conceptos básicos de continuidad de operaciones (BCP/DRP) y su función en la resiliencia de misión.

---

> **Recapitulación:** La Parte 1 (CONT-25) y Parte 2 (CONT-26) cubrieron las Capas 5 y 6 — seguridad de aplicaciones (auth, OWASP) y seguridad de datos (clasificación, BitLocker, DLP). Esta clase llega al núcleo del modelo: la Capa 7, los activos críticos que todas las capas anteriores están diseñadas para proteger.

---

## ¿Qué es un Activo Crítico?

Las 6 capas anteriores existen porque hay algo que proteger en el centro. La Capa 7 es ese núcleo — los activos cuya pérdida, compromiso o destrucción comprometería la misión o causaría daño irreparable.

!!! note "Definición"
    Un **activo crítico** es cualquier sistema, activo de información o recurso cuyo fallo, compromiso o destrucción tendría un impacto inaceptable en la capacidad operacional de la organización, incluyendo la imposibilidad de cumplir la misión.

No todos los activos son críticos. Un ordenador de escritorio para tareas administrativas no es crítico — si falla, el trabajo se hace en otro equipo. El servidor de directorio activo que autentica a todos los usuarios de la red, sí es crítico — si falla, nadie puede acceder a nada.

La distinción importa porque los controles más costosos y restrictivos (acceso físico con doble llave, backups en múltiples ubicaciones, sistemas redundantes) se aplican solo a activos críticos. Aplicar esos controles a todo sería económicamente inviable.

---

## Activos Críticos en Entornos Militares de Comunicaciones

Los técnicos de comunicaciones trabajan con activos que tienen características particulares: muchos son físicos (no solo software), operan en condiciones adversas (campo, temperatura, interferencias), y su fallo puede tener consecuencias inmediatas en operaciones activas.

| Activo | Por qué es crítico | Impacto de compromiso |
|--------|-------------------|-----------------------|
| **Sistemas C2 (Command and Control)** | Coordinan el mando y control de las operaciones; sin ellos, las unidades operan de forma descoordinada | Pérdida de coordinación táctica; riesgo de fuego amigo; misión comprometida |
| **Radios tácticas y enlaces de comunicaciones** | Canal primario de comunicación en operaciones — voz y datos entre unidades | Pérdida de comunicación operacional; unidades aisladas |
| **SCIF (Sensitive Compartmented Information Facility)** | Instalación de procesamiento de información clasificada — controles físicos y de red especiales | Exposición de información de inteligencia; comprometer fuentes y métodos |
| **Equipos de puesto de mando avanzado** | Comunicaciones en campo; frecuencias, claves de cifrado, órdenes de operación | Captura de frecuencias y claves; interrupción del mando avanzado |
| **Servidor de directorio / autenticación** | Autentica a todos los usuarios de la red; sin él, nadie accede a ningún sistema | Bloqueo completo de la red; o compromiso si el atacante obtiene control del directorio |
| **Sistemas de backup y archivos** | Contienen la copia de recuperación de datos críticos | Si el backup está comprometido, la recuperación post-incidente es imposible |

!!! example "Aplicación en entorno castrense"
    Durante un ejercicio conjunto, el servidor de directorio activo del batallón (que autentica a los 120 usuarios de la red) es cifrado por un ransomware. Nadie puede iniciar sesión en ningún sistema. El oficial de operaciones no puede acceder al sistema de planificación de misiones. Los técnicos no pueden acceder a las consolas de gestión de radios. El batallón queda efectivamente sin capacidad de gestión de red durante el tiempo que tarda la recuperación. El servidor de directorio es la definición exacta de activo crítico: cuando falla, todo lo demás falla con él.

---

## Criterios de Criticidad: ¿Cómo Priorizar?

Cuando los recursos son limitados, no todos los activos pueden tener el máximo nivel de protección. Se necesita un criterio sistemático para priorizar.

Los cuatro criterios principales:

**1. Criticidad operacional:** ¿Qué pasa si este activo falla? ¿La misión continúa, se degrada o se interrumpe completamente?

**2. Tiempo de recuperación:** ¿Cuánto tiempo tarda en recuperarse si falla? Un activo que tarda 4 horas en recuperarse es más crítico que uno que se recupera en 15 minutos.

**3. Dependencias:** ¿Cuántos otros sistemas dependen de este activo? El servidor de directorio es crítico en parte porque docenas de sistemas dependen de él para autenticar.

**4. Irreemplazabilidad:** ¿Puede ser sustituido temporalmente? Un router de borde puede ser reemplazado por uno de respaldo; las claves de cifrado de un sistema C2 que no tienen copia de seguridad no pueden.

### Tabla de Criticidad

| Activo | Criticidad operacional | Tiempo recuperación | Dependencias | Clasificación |
|--------|----------------------|--------------------|-----------|--------------------|
| Servidor C2 | Alta — sin él no hay coordinación | Horas | Alta — todas las unidades | **Crítico** |
| Radios tácticas (set principal) | Alta | Minutos (set de repuesto) | Media | **Crítico** |
| Servidor de directorio | Alta — sin él nadie se autentica | Horas | Muy alta | **Crítico** |
| Servidor de archivos operacionales | Media — trabajo continúa sin acceso temporal | Horas | Media | **Importante** |
| Equipos de trabajo administrativos | Baja — trabajo se redistribuye | Minutos | Baja | **Estándar** |

!!! tip "Regla práctica de priorización"
    Un activo es crítico si su indisponibilidad durante más de 2 horas comprometería la misión activa o impediría el mando y control. Este umbral es orientativo — cada organización debe definir el propio basado en su misión específica.

---

## Continuidad de Operaciones: BCP y DRP

Los activos críticos pueden fallar — por un incidente de seguridad, por un fallo hardware, por un desastre natural o por error humano. La planificación de continuidad define qué hacer cuando eso ocurre.

!!! note "Definición"
    El **BCP** (Business Continuity Plan / Plan de Continuidad de Negocio) es el plan que define cómo una organización continúa operando durante y después de un incidente significativo. El **DRP** (Disaster Recovery Plan / Plan de Recuperación ante Desastres) es el componente técnico del BCP que define los procedimientos para recuperar sistemas y datos tras un fallo.

### Conceptos Clave de Continuidad

| Concepto | Qué significa | Ejemplo |
|----------|--------------|---------|
| **RTO** (Recovery Time Objective) | Tiempo máximo aceptable para recuperar un sistema después de un fallo | "El servidor C2 debe estar operativo en máximo 4 horas tras un fallo" |
| **RPO** (Recovery Point Objective) | Cantidad máxima de datos que la organización puede perder — equivalente a "¿cuántas horas de trabajo atrás puede irse el backup?" | "El backup más antiguo aceptable tiene 1 hora de antigüedad" |
| **Failover** | Conmutación automática a un sistema de respaldo cuando el primario falla | Switch secundario que asume el control cuando el switch primario falla |
| **Backup offsite** | Copia de seguridad almacenada en una ubicación física diferente al sistema principal | Backup diario replicado a otra instalación — si la instalación principal es destruida, los datos están en otro lugar |

### Estrategias de Recuperación por Nivel de Criticidad

| Estrategia | Descripción | Costo | Para activos |
|-----------|-------------|-------|-------------|
| **Hot standby** | Sistema de respaldo listo, encendido y sincronizado — failover en segundos | Alto | Activos críticos con RTO < 1 hora |
| **Warm standby** | Sistema de respaldo parcialmente configurado — failover en minutos con intervención mínima | Medio | Activos críticos con RTO < 4 horas |
| **Cold standby** | Hardware de repuesto almacenado sin configurar — recuperación en horas con intervención técnica | Bajo | Activos importantes con RTO < 24 horas |
| **Backup y restauración** | Solo backups; recuperación desde cero — horas a días | Muy bajo | Activos estándar |

!!! example "Aplicación en entorno castrense"
    El batallón define que el servidor C2 tiene un RTO de 2 horas (tolerancia máxima de 2 horas de indisponibilidad) y un RPO de 30 minutos (máximo 30 minutos de datos perdidos). Para cumplir estos objetivos, el servidor C2 tiene un warm standby en la sala de servidores secundaria con snapshots cada 30 minutos. Si el servidor primario falla, el técnico activa el standby en aproximadamente 15 minutos. Los datos del C2 tienen como máximo 30 minutos de antigüedad, cumpliendo el RPO. Las comunicaciones C2 se restauran dentro del RTO de 2 horas.

---

## Resumen

1. Los activos críticos son aquellos cuyo fallo comprometería la misión; en entornos militares incluyen sistemas C2, radios tácticas, SCIF y servidor de directorio.
2. La criticidad se evalúa por impacto operacional, tiempo de recuperación, dependencias e irreemplazabilidad.
3. El BCP (continuidad operacional) y el DRP (recuperación técnica) definen qué hacer cuando falla un activo crítico; los conceptos clave son RTO y RPO.
4. Las estrategias de recuperación (hot/warm/cold standby, backup) se eligen según el RTO requerido para cada activo.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **NIST SP 800-34 Rev. 1 — Contingency Planning Guide for Federal Information Systems:** https://csrc.nist.gov/publications/detail/sp/800-34/rev-1/final

---

*Siguiente: [Capa de Activos Críticos (Parte 2) — Respuesta a Incidentes NIST SP 800-61 y Ejercicio Tabletop](capa-activos-criticos-p2.md)*
