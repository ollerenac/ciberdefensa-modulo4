---
# Horas asignadas: 4 hrs
# Tipo: Teoría
---

# ISO 27005 — Gestión del Riesgo (Parte 1)

> **Duración:** 4 horas | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Describir el proceso completo de gestión del riesgo según ISO 27005:2022.
- Construir un inventario básico de activos de información con clasificación de valor.
- Identificar amenazas y vulnerabilidades asociadas a activos de comunicaciones militares.
- Explicar cómo se relacionan activo, amenaza y vulnerabilidad para formar un riesgo.

---

## Por qué Existe ISO 27005

ISO 27001 exige que las organizaciones evalúen y traten sus riesgos de seguridad de la información. Pero ISO 27001 no especifica cómo hacerlo — solo dice que debe hacerse de forma sistemática. ISO 27005 llena ese vacío: es el estándar que describe el proceso completo de gestión del riesgo de seguridad de la información.

!!! note "Definición"
    **ISO/IEC 27005:2022** es el estándar internacional que proporciona directrices para la gestión del riesgo de seguridad de la información. Describe el proceso completo — desde el establecimiento del contexto hasta el monitoreo continuo — y se puede usar con cualquier metodología de evaluación de riesgos.

La gestión del riesgo no es una actividad de seguridad exclusiva del área de TI. Es un proceso de toma de decisiones: qué riesgos son aceptables, cuáles deben reducirse, y qué recursos se destinan a esa reducción. Esas decisiones afectan la misión y deben involucrar al mando.

---

## El Proceso de Gestión del Riesgo

ISO 27005 describe el proceso de gestión del riesgo como un ciclo con seis etapas interconectadas:

```
Establecimiento del contexto
         │
         ▼
Identificación del riesgo
(activos → amenazas → vulnerabilidades → impacto)
         │
         ▼
Análisis del riesgo
(probabilidad × impacto = nivel de riesgo)
         │
         ▼
Evaluación del riesgo
(¿el riesgo es aceptable o requiere tratamiento?)
         │
         ▼
Tratamiento del riesgo
(mitigar / aceptar / transferir / evitar)
         │
         ▼
Monitoreo y revisión
(¿los controles siguen funcionando? ¿cambiaron los riesgos?)
         │
         ▲──────────────────────────── (ciclo continuo)
```

Cada etapa produce información que alimenta a las siguientes. El proceso no termina — se repite cuando cambia el contexto, se incorporan nuevos activos, o surgen nuevas amenazas.

### Etapa 1: Establecimiento del contexto

Antes de evaluar riesgos, la organización debe establecer el marco en el que trabajará: qué criterios usará para evaluar riesgos, cuál es el apetito de riesgo de la organización (cuánto riesgo está dispuesta a aceptar), y cuáles son los límites del proceso de gestión del riesgo.

**En una unidad militar:** El contexto incluye la misión actual de la unidad, los sistemas en operación, las restricciones operacionales (qué sistemas son críticos para la misión y cuya interrupción no es aceptable), y el horizonte temporal de la evaluación (¿para esta operación específica o para el ciclo anual de planificación?).

---

## Inventario de Activos de Información

La gestión del riesgo comienza con saber qué hay que proteger. Sin un inventario de activos, cualquier evaluación de riesgos es incompleta.

!!! note "Definición"
    Un **activo de información** es cualquier elemento que tenga valor para la organización y que necesite protección. Incluye datos, sistemas, personas, instalaciones, hardware, software y procesos.

### Tipos de activos

Los activos de información se clasifican en varias categorías:

| Tipo | Descripción | Ejemplos en comunicaciones militares |
|------|-------------|-------------------------------------|
| **Datos / información** | La información en sí misma, en cualquier forma | Planes de comunicaciones, frecuencias de operación, material criptográfico, reportes de inteligencia |
| **Hardware** | Equipos físicos | Radios tácticas, terminales C2, servidores de enlace, equipos de puesto de mando |
| **Software** | Aplicaciones y sistemas operativos | Sistemas de gestión de redes de comunicaciones, firmwares de equipos, aplicaciones de mando y control |
| **Personas** | Personal con conocimiento y acceso | Operadores de radio habilitados, Técnicos de sistemas C2, personal con acceso al SCIF |
| **Instalaciones** | Espacios físicos | Sala de comunicaciones, SCIF, puesto de mando avanzado, vehículos de comunicaciones |
| **Servicios** | Servicios de comunicaciones o procesamiento | Enlace satelital, servicio de telecomunicaciones de la fuerza, red de datos táctica |

### Valoración de activos

No todos los activos tienen el mismo valor para la misión. La valoración establece qué activos son más críticos y por tanto requieren mayor protección.

Los criterios de valoración más comunes son:

| Criterio | Pregunta | Ejemplo |
|----------|----------|---------|
| **Confidencialidad** | ¿Qué tan grave sería que este activo fuera conocido por el adversario? | Material criptográfico: gravísimo. Lista de horarios del comedor: insignificante. |
| **Integridad** | ¿Qué tan grave sería que este activo fuera modificado sin autorización? | Planes de coordinación de fuego: crítico — un error puede causar fuego amigo. Directorio telefónico: bajo impacto. |
| **Disponibilidad** | ¿Qué tan grave sería que este activo no estuviera disponible cuando se necesita? | Red de comunicaciones tácticas durante una operación: misión-crítico. Archivo de actas del año anterior: impacto nulo. |

La suma de los tres criterios (Confidencialidad + Integridad + Disponibilidad) da el **valor del activo** — que puede expresarse como un número o como una categoría (Alto / Medio / Bajo).

**Ejemplo de inventario simplificado:**

| Activo | Tipo | Confidencialidad | Integridad | Disponibilidad | Valor global |
|--------|------|-----------------|------------|----------------|--------------|
| Material criptográfico (claves vigentes) | Datos | Alto | Alto | Alto | **Crítico** |
| Radios tácticas (10 unidades) | Hardware | Medio | Alto | Alto | **Alto** |
| Red de datos táctica | Servicio | Alto | Alto | Alto | **Crítico** |
| Registro de mantenimiento de equipos | Datos | Bajo | Medio | Bajo | **Bajo** |
| Terminal de sistema C2 (principal) | Hardware | Alto | Alto | Alto | **Crítico** |

!!! warning "El inventario debe mantenerse actualizado"
    Un inventario que se elabora una vez y no se revisa pierde valor rápidamente. Cada vez que se incorpora un nuevo sistema, se retira un equipo o cambia la misión de la unidad, el inventario debe actualizarse. El inventario obsoleto es casi tan malo como no tener inventario.

---

## Catálogo de Amenazas

Una **amenaza** es un evento o acción que puede causar daño a un activo de información. Las amenazas no son hipotéticas — son situaciones que han ocurrido o pueden ocurrir en contextos similares.

### Categorías de amenazas

Las amenazas se agrupan en tres grandes categorías:

**1. Amenazas deliberadas (intencionales):**
Acciones que alguien realiza con el propósito de causar daño — un adversario, un insider malicioso, o un atacante externo.

**2. Amenazas accidentales:**
Errores humanos no intencionales que causan daño — configurar mal un sistema, borrar archivos por equivocación, olvidar aplicar un parche.

**3. Amenazas ambientales:**
Eventos físicos o naturales que pueden dañar activos — fallas de energía, inundaciones, temperatura extrema, fallos de hardware.

### Catálogo de amenazas para comunicaciones militares

| Amenaza | Categoría | Activos afectados | Consecuencia si se materializa |
|---------|-----------|------------------|---------------------------------|
| Interceptación de comunicaciones por el adversario | Deliberada | Transmisiones de radio, enlace de datos | Pérdida de confidencialidad de la información operacional |
| Interferencia electrónica (jamming) | Deliberada | Radios tácticas, enlace satelital | Pérdida de disponibilidad de comunicaciones durante la operación |
| Captura de equipo con material criptográfico | Deliberada | Radios tácticas, terminales C2 | Pérdida de confidencialidad del material criptográfico vigente |
| Error de configuración por operador | Accidental | Sistemas C2, configuración de red | Pérdida de integridad o disponibilidad de comunicaciones |
| Uso incorrecto de canal no cifrado | Accidental | Transmisiones de radio | Pérdida de confidencialidad de información sensible |
| Fallo de energía en el puesto de mando | Ambiental | Todos los sistemas del puesto de mando | Pérdida de disponibilidad de toda comunicación |
| Daño físico por condiciones de campo | Ambiental | Radios tácticas, equipos de puesto de mando | Pérdida de disponibilidad de equipos críticos |
| Acceso físico no autorizado a sala de comunicaciones | Deliberada | Equipos, documentación clasificada | Pérdida de confidencialidad e integridad |
| Infección por malware vía USB | Deliberada / Accidental | Terminales C2 con sistema operativo | Pérdida de integridad y disponibilidad de sistemas |

---

## Identificación de Vulnerabilidades

Una **vulnerabilidad** es una debilidad en un activo o en los controles que lo protegen, que puede ser explotada por una amenaza para causar daño. La combinación de amenaza + vulnerabilidad crea el riesgo.

!!! note "Definición"
    **Vulnerabilidad** = Debilidad en un activo o control. **Amenaza** = Evento que puede explotar esa debilidad. **Riesgo** = Probabilidad de que la amenaza explote la vulnerabilidad × Impacto en la organización.

### Tipos de vulnerabilidades

| Tipo | Descripción | Ejemplo en comunicaciones militares |
|------|-------------|-------------------------------------|
| **Técnica** | Debilidad en software, hardware o configuración | Firmware de radio táctico sin parche de seguridad publicado hace 6 meses |
| **Procedimental** | Falta de procedimiento o procedimiento incorrecto | No existe procedimiento documentado para reportar un radio táctico perdido |
| **Física** | Debilidad en la protección física | Sala de comunicaciones sin registro de acceso — imposible rastrear quién estuvo allí |
| **Humana** | Falta de formación o concienciación | Técnico no entrenado en el procedimiento de emergencia para borrar material criptográfico |

### La relación activo-amenaza-vulnerabilidad

Para cada activo, la identificación de riesgos combina amenazas y vulnerabilidades:

| Activo | Amenaza | Vulnerabilidad | Riesgo resultante |
|--------|---------|---------------|-------------------|
| Radio táctico con material criptográfico | Captura por adversario | No existe procedimiento de borrado de emergencia | Si el radio es capturado, el material criptográfico queda expuesto — compromiso de toda la red de comunicaciones cifradas |
| Terminal sistema C2 | Infección por malware | El terminal no tiene protección antimalware activa | Un USB infectado introducido en el terminal puede comprometer los datos operacionales del sistema C2 |
| Enlace satelital | Interferencia electrónica | No existe canal de comunicación redundante | Si el enlace satelital es interferido y no hay redundancia, la unidad queda sin comunicaciones estratégicas |

Esta tabla estructura el pensamiento de evaluación de riesgos — que se usará en la Parte 2 para calcular el nivel de riesgo y decidir el tratamiento.

---

## Aplicación en Contexto Castrense

**Ejemplo 1 — Inventario de activos en un puesto de mando avanzado:**
Antes de desplegar un puesto de mando avanzado, el Oficial de Comunicaciones realiza un inventario de los activos que se van a operar en ese despliegue. El inventario incluye cada radio táctico (número de serie, operador asignado, si tiene material criptográfico cargado), los terminales del sistema C2 (configuración, acceso habilitado para qué personal), y el enlace satelital (ventanas de comunicación disponibles, capacidad de backup). Este inventario no es burocracia — es la base para saber exactamente qué proteger durante el despliegue.

**Ejemplo 2 — Amenazas y vulnerabilidades en un SCIF de campaña:**
Un SCIF de campaña (instalación temporal para el procesamiento de información clasificada) enfrenta amenazas específicas del entorno operacional: espionaje de señales por equipos de inteligencia adversaria, acceso físico no autorizado por personal sin habilitación, y pérdida de documentación clasificada en caso de retirada de emergencia. Las vulnerabilidades pueden incluir: no todos los documentos físicos están inventariados (vulnerabilidad procedimental), el perímetro físico del SCIF de campaña es menos robusto que uno permanente (vulnerabilidad física), y el personal rotante no siempre recibe la instrucción de seguridad completa antes de entrar (vulnerabilidad humana). Cada par amenaza-vulnerabilidad define un riesgo que debe tratarse.

---

## Resumen

1. **ISO 27005:2022** describe el proceso completo de gestión del riesgo: Establecimiento de contexto → Identificación → Análisis → Evaluación → Tratamiento → Monitoreo.
2. La gestión del riesgo comienza con el **inventario de activos**: datos, hardware, software, personas, instalaciones y servicios. Cada activo se valora según Confidencialidad, Integridad y Disponibilidad.
3. El **catálogo de amenazas** organiza los eventos que pueden causar daño: deliberados (interceptación, captura, ciberataque), accidentales (errores humanos) y ambientales (fallas de energía, daño físico).
4. Una **vulnerabilidad** es una debilidad que una amenaza puede explotar. El riesgo resulta de la combinación: activo + amenaza + vulnerabilidad.
5. La Parte 2 cubrirá cómo calcular el nivel de riesgo y decidir el tratamiento adecuado para cada riesgo identificado.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **ISO 27005:2022 overview (ISO.org):** https://www.iso.org/standard/80585.html — Descripción oficial del estándar de gestión del riesgo.
- **Guía práctica de evaluación de riesgos (Advisera):** https://advisera.com/27001academy/knowledgebase/iso-27001-risk-assessment-treatment/ — Cómo hacer una evaluación de riesgos usando ISO 27005 como guía.
- **Catálogo de amenazas ENISA:** https://www.enisa.europa.eu/topics/threat-risk-management/threats-and-trend-analysis — Catálogo europeo de amenazas de ciberseguridad, actualizado anualmente, en inglés.

---

*Siguiente: [ISO 27005 — Gestión del Riesgo (Parte 2)](iso27005-riesgo-p2.md)*
