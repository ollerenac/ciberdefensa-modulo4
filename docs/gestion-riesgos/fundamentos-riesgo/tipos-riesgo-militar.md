---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Tipos de Riesgo en Sistemas e Importancia en Entornos Militares

> **Duración:** 2 horas | **Asignatura:** Gestión de Riesgos y Vulnerabilidades | **Unidad:** Fundamentos de Gestión de Riesgos

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Clasificar un riesgo dado en una de las cuatro categorías de la taxonomía castrense (operacional, TI, físico, humano).
- Identificar dos o más factores específicos del entorno militar que amplifican cada categoría de riesgo.
- Reconocer el impacto de cada categoría en la continuidad de la misión.

---

## Por Qué el Contexto Militar Es Diferente

Los marcos de gestión de riesgos civiles —ISO 27005, NIST— identifican riesgos genéricos válidos para cualquier organización. En el entorno castrense, hay tres factores estructurales que amplifican ciertos tipos de riesgo de forma significativa:

**1. Cadena de mando rígida.** En una organización civil, si el gerente de TI no está disponible, otro directivo toma decisiones de seguridad. En el Ejército, la cadena de mando es la vía de decisión. Un solo punto de falla humana —el Oficial de guardia que no escala, el Técnico que no reporta— puede paralizar la respuesta a un incidente por horas.

**2. Sigilo y clasificación.** Las organizaciones militares manejan más información sensible y clasificada por unidad que la mayoría de empresas privadas. Las consecuencias de una filtración no son económicas — son operacionales y de seguridad nacional. Un documento de planificación comprometido puede costar vidas.

**3. Continuidad de misión como prioridad absoluta.** En un banco, un sistema caído implica pérdida económica. En una unidad de comunicaciones durante una operación, un sistema caído puede implicar la imposibilidad de coordinar fuerzas, recibir órdenes o solicitar evacuación médica. La **disponibilidad** puede ser más crítica que la confidencialidad en escenarios de misión activa.

Estos tres factores determinan cómo se clasifican y priorizan los riesgos en el entorno castrense.

---

## Categoría 1 — Riesgos Operacionales

**Definición:** Riesgos que afectan directamente la ejecución de la misión por falla de procesos, sistemas de mando y control (C2), o comunicaciones tácticas.

| Ejemplo | Descripción |
|---------|-------------|
| Falla del sistema C2 durante operaciones nocturnas | El sistema de mando y control pierde conectividad — el puesto avanzado no puede recibir actualizaciones del mapa de situación ni transmitir posición. |
| Pérdida de la red de radio táctica en área de operaciones | Todas las radios del batallón pierden sincronización — las unidades de maniobra quedan sin canal de voz seguro. |
| Corte de comunicaciones entre puesto avanzado y base principal | El Oficial de Operaciones en base no puede confirmar el estado de la misión ni autorizar cambios de plan. |

**Factor amplificador:** En operaciones activas, no hay tiempo para un diagnóstico técnico largo. La ventana para remediar un fallo operacional es minutos, no horas. Los riesgos operacionales tienen la mayor urgencia de escalada.

!!! warning "Señal de alerta"
    Un riesgo operacional que no se escala en los primeros minutos de detectado puede pasar de "incidente técnico" a "fallo de misión". El Técnico de guardia es el primer respondedor — su primera acción es reportar, no diagnosticar.

---

## Categoría 2 — Riesgos TI

**Definición:** Vulnerabilidades técnicas en software, hardware o configuración de los sistemas de información de la unidad que pueden ser explotadas por un atacante o causar fallo por degradación.

| Ejemplo | Descripción |
|---------|-------------|
| Software sin parchear en terminales C2 | El sistema operativo de los puestos de mando tiene vulnerabilidades conocidas (CVE publicados) sin resolver. |
| Contraseñas genéricas compartidas | Varios operadores usan la misma contraseña para el sistema de turno — imposible auditar quién accedió a qué. |
| Puertos USB habilitados en equipos sensibles | Cualquier medio removible puede introducir malware o extraer información sin control. |
| Firmware desactualizado en radios tácticas | Las radios corren versiones de firmware con fallas conocidas que pueden comprometer la criptografía integrada. |

**Factor amplificador:** Los Técnicos de comunicaciones son a la vez usuarios y administradores de los sistemas de la unidad. La superficie de ataque coincide con la responsabilidad de custodia. Un Técnico que conecta un USB personal al servidor de archivos es simultáneamente el riesgo y el responsable de prevenir ese riesgo.

!!! note "Distinción importante"
    Los riesgos TI no se limitan a ataques externos. La mayoría de incidentes TI en unidades militares tienen origen interno: una mala configuración, un software desactualizado, una política incumplida. El atacante aprovecha lo que ya existe.

---

## Categoría 3 — Riesgos Físicos

**Definición:** Riesgos derivados del entorno físico o del acceso no autorizado a equipos, instalaciones o medios de almacenamiento.

| Ejemplo | Descripción |
|---------|-------------|
| Robo o extravío de laptop de campo sin cifrado | Una laptop con documentos de planificación operacional queda expuesta — cualquier persona con acceso físico puede leer los archivos. |
| Acceso de personal no autorizado a la sala de comunicaciones | Una persona sin autorización ingresa al cuarto de comunicaciones durante un cambio de guardia con control deficiente. |
| Daño a equipos por condiciones ambientales | Polvo, humedad o temperatura extrema en zonas operativas deterioran equipos que no están en racks cerrados o con protección adecuada. |
| Medios de almacenamiento sin destrucción certificada | Un disco duro dado de baja pero no destruido correctamente puede ser recuperado por terceros. |

**Factor amplificador:** Las unidades de comunicaciones operan en entornos no controlados — bases de campaña, vehículos tácticos, zonas de operaciones en campo. A diferencia de una oficina corporativa, el perímetro físico cambia con la operación. Los controles físicos que funcionan en una instalación permanente no siempre son aplicables en campo.

---

## Categoría 4 — Riesgos Humanos

**Definición:** Riesgos originados en el comportamiento del personal, ya sea por error, negligencia o acción malintencionada. Es la categoría más difícil de controlar técnicamente.

Esta categoría tiene tres subcategorías:

### 4a. Insider Threat (Amenaza Interna)

Personal con acceso legítimo a sistemas o información que los usa de forma no autorizada — ya sea para filtrar información, sabotear sistemas o facilitar el acceso a terceros. El insider no necesita explotar una vulnerabilidad técnica: ya tiene las credenciales.

### 4b. Negligencia

Comportamiento descuidado sin intención maliciosa que crea vulnerabilidades. Ejemplos frecuentes:

- Usar medios personales (USB, correo personal, WhatsApp) para transferir información de trabajo.
- Conectar a redes Wi-Fi no autorizadas desde equipos de la unidad.
- Compartir contraseñas con compañeros "para mayor comodidad".
- Dejar sesiones abiertas al ausentarse del puesto.

### 4c. Exposición OSINT Involuntaria

El Técnico publica en redes sociales fotos, videos o comentarios que revelan información de valor de inteligencia: ubicación de la unidad, equipos en uso, horarios de operación, nombres de personal. Sin saberlo, está proporcionando información al adversario.

**Factor amplificador:** En organizaciones con alta cohesión de grupo —como el Ejército— la sospecha interna es culturalmente difícil de gestionar. Nadie quiere creer que un compañero puede ser un riesgo. Por eso el control técnico (principio de mínimo privilegio, auditoría de accesos, separación de funciones) es más efectivo que el control social.

!!! note "Principio de mínimo privilegio"
    Cada usuario debe tener acceso solo a los recursos que necesita para su función — ni uno más. Un Técnico de mantenimiento de radios no necesita acceso a la base de datos de personal de la unidad. Implementar este principio reduce el daño potencial de cualquier riesgo humano, tanto de negligencia como de amenaza interna.

En la práctica, los riesgos no aparecen en categorías limpias. Un solo escenario puede involucrar múltiples categorías simultáneamente. Por ejemplo: un Técnico que conecta un USB personal al servidor de archivos activa un **Riesgo Humano** (negligencia al violar la política de medios removibles), un **Riesgo TI** (el USB puede introducir malware), y un **Riesgo Operacional** potencial (si el malware afecta el servidor, los archivos de planificación pueden quedar comprometidos). La clasificación por categorías sirve para priorizar la respuesta — el Técnico primero aísla el vector TI y luego escala el aspecto humano a la cadena de mando.

---

## Aplicación en Contexto Castrense

### Ejercicio de Clasificación (5 minutos en clase)

El instructor lee cada escenario. El alumno indica la categoría principal de riesgo y el factor amplificador más relevante.

| # | Escenario | Categoría principal | Respuesta sugerida |
|---|-----------|---------------------|-------------------|
| 1 | El sistema C2 del puesto de mando pierde conexión a la red táctica durante una maniobra nocturna. El equipo de comunicaciones no puede contactar a las unidades de flanco. | Riesgo Operacional | Falla de proceso/sistema que afecta directamente la ejecución de la misión. Factor amplificador: no hay tiempo de diagnóstico — el impacto es inmediato. |
| 2 | Un Técnico detecta que el antivirus de los terminales C2 no se ha actualizado en 45 días porque la licencia venció sin que nadie lo notara. | Riesgo TI | Vulnerabilidad técnica por configuración descuidada. Factor amplificador: los Técnicos son custodios y usuarios del mismo sistema. |
| 3 | La laptop del Oficial de Inteligencia fue olvidada dentro del vehículo táctico durante 2 horas. El vehículo estuvo desatendido en el estacionamiento de la base. | Riesgo Físico | Acceso no controlado a equipos fuera del perímetro seguro. Factor amplificador: las unidades operan en entornos no controlados donde el perímetro físico cambia. |
| 4 | Un miembro del equipo publica en Instagram una foto del interior de la sala de comunicaciones mostrando el equipo en uso y la ubicación de la antena. | Riesgo Humano (OSINT) | Exposición involuntaria de información de valor de inteligencia. Factor amplificador: la cohesión de grupo hace difícil señalar este comportamiento internamente. |
| 5 | El Técnico de guardia entrega su usuario y contraseña al relevo "para que no pierda tiempo al ingresar" y se va sin cerrar su sesión. | Riesgo Humano (negligencia) | Compartir credenciales destruye la trazabilidad de auditoría y expone el sistema. Factor amplificador: principio de mínimo privilegio requiere que cada usuario use sus propias credenciales. |
| 6 | El firmware de las radios HF del batallón tiene una versión que el fabricante marcó como "obsoleta y vulnerable" hace 6 meses. No se ha actualizado porque el proceso requiere enviar las radios a mantenimiento central. | Riesgo TI con componente Operacional | Vulnerabilidad técnica en firmware con riesgo potencial de explotación. Factor amplificador: la actualización requiere retirar equipo operacional — tension entre disponibilidad y seguridad. La decisión de aceptar el riesgo temporalmente requiere autorización del Oficial de Seguridad. |

---

## Resumen

- El entorno militar amplifica los riesgos por tres factores estructurales: cadena de mando rígida, alto volumen de información clasificada, y continuidad de misión como prioridad.
- La **taxonomía de cuatro categorías** — Operacional, TI, Físico, Humano — organiza los riesgos para priorizar la respuesta. No son mutuamente excluyentes: un incidente puede activar múltiples categorías.
- Los **Riesgos Operacionales** tienen la mayor urgencia de escalada: afectan directamente la ejecución de la misión en tiempo real.
- Los **Riesgos Humanos** son los más difíciles de controlar técnicamente — el principio de mínimo privilegio y la auditoría de accesos son las herramientas principales.
- Clasificar correctamente un riesgo determina qué control aplicar primero y a quién escalar.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- NIST SP 800-82 — Guide to Operational Technology (OT) Security: [https://csrc.nist.gov/publications/detail/sp/800-82/rev-3/final](https://csrc.nist.gov/publications/detail/sp/800-82/rev-3/final) — Riesgos específicos en sistemas de control e infraestructura crítica; relevante para sistemas de comunicaciones militares.

---
*Siguiente: [Métodos de Identificación y Priorización de Vulnerabilidades](../identificacion-vulnerabilidades/metodos-identificacion.md)*
