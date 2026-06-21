---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# Controles, Buenas Prácticas y Reporte Técnico

> **Duración:** 1 hora | **Asignatura:** Gestión de Riesgos y Vulnerabilidades | **Unidad:** Mitigación y Control de Riesgos

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Clasificar un control de seguridad como preventivo, detectivo o correctivo.
- Completar la plantilla estándar de reporte de vulnerabilidad con un caso real.
- Describir la cadena de reporte de vulnerabilidades en una unidad militar.

---

## Los Tres Tipos de Controles

Un control de seguridad es cualquier medida —técnica, administrativa o física— que reduce un riesgo. Los controles se clasifican según su función en el tiempo:

| Tipo | Cuándo actúa | Definición | Ejemplo técnico | Ejemplo humano/proceso |
|------|-------------|------------|-----------------|------------------------|
| **Preventivo** | Antes del incidente | Evita que la amenaza se materialice o que la vulnerabilidad sea explotada | Firewall que bloquea el tráfico no autorizado; política de contraseñas fuertes; antivirus con protección en tiempo real | Capacitación de personal sobre OSINT y uso de redes sociales; política de no usar USB personales en equipos de la unidad |
| **Detectivo** | Durante o inmediatamente después | Alerta cuando un incidente está ocurriendo o ya ocurrió, para que se pueda responder | IDS que genera alerta cuando detecta tráfico inusual; logs de acceso al sistema; auditoría de cuentas con revisión periódica | Revisión diaria del log de accesos; reporte de anomalías en el comportamiento de la red por el Técnico de turno |
| **Correctivo** | Después del incidente | Restaura el estado normal y reduce el daño posterior al incidente | Aplicar parche de seguridad; restaurar backup limpio; revocar credenciales comprometidas | Activar procedimiento de respuesta a incidentes; notificar a la cadena de mando; documentar la acción correctiva tomada |

!!! note "Un control puede cumplir más de un rol"
    El antivirus es preventivo (bloquea la amenaza antes de ejecutarse) y detectivo (genera alertas cuando detecta malware). Los logs de acceso son detectivos (registran lo que ocurrió) pero también preventivos (el personal sabe que sus acciones quedan registradas, lo cual inhibe comportamientos inadecuados). Al clasificar un control, se identifica su función primaria — no es obligatorio encajarlo en una sola categoría.

---

## El Reporte de Vulnerabilidad: Plantilla y Cadena

El reporte de vulnerabilidad es el documento que el Técnico completa cuando detecta una falla de seguridad. Sin este reporte, la vulnerabilidad no existe formalmente para la cadena de mando — no se puede decidir sobre lo que no está documentado.

### Plantilla Estándar

| Campo | Contenido |
|-------|-----------|
| **ID de hallazgo** | VUL-2024-001 |
| **Activo afectado** | [Nombre del sistema o equipo] |
| **CVE (si aplica)** | CVE-XXXX-XXXX (o "No identificado" si no hay CVE conocido) |
| **Severidad** | Critical / High / Medium / Low — con score CVSS si está disponible |
| **Descripción** | Descripción clara de la vulnerabilidad detectada: qué falla, en qué componente, bajo qué condiciones puede ser explotada |
| **Recomendación** | Acción concreta para resolver la vulnerabilidad: aplicar parche, cambiar configuración, actualizar firmware |
| **Control compensatorio** | Si no es posible aplicar la solución definitiva: qué medida temporal reduce el riesgo (ej. aislar en VLAN, bloquear puerto, deshabilitar servicio) |
| **Fecha de detección** | [Fecha y hora de detección] |
| **Fecha límite de remediación** | [Calculada según severidad: Critical +24 hrs / High +7 días / Medium +30 días] |
| **Reportado por** | [Nombre, grado, unidad del Técnico que detectó la falla] |
| **Reportado a** | [Nombre, grado del Jefe inmediato o Oficial de Seguridad que recibe el reporte] |

### Ejemplo Completo: Caso JC-BC3

| Campo | Contenido |
|-------|-----------|
| **ID de hallazgo** | VUL-2024-003 |
| **Activo afectado** | Servidor de archivos — Jefatura de Comunicaciones BC3 (Windows Server 2016) |
| **CVE (si aplica)** | CVE-2024-A001 |
| **Severidad** | Critical (CVSS 9.8) |
| **Descripción** | El servidor ejecuta Windows Server 2016 con el servicio RDP habilitado y el parche MS24-XXX sin aplicar. La vulnerabilidad CVE-2024-A001 permite ejecución remota de código sin autenticación por un actor con acceso a la red de la unidad. |
| **Recomendación** | Aplicar parche MS24-XXX vía Windows Update antes del [fecha límite]. Verificar que el parche se instaló correctamente con reinicio del servidor y revisión de logs. |
| **Control compensatorio** | Si no es posible parchear en el plazo: deshabilitar el servicio RDP (solo activarlo cuando sea necesario para soporte remoto autorizado) y bloquear el puerto 3389 en el firewall de la red de la unidad. |
| **Fecha de detección** | 2024-03-15 14:30 |
| **Fecha límite de remediación** | 2024-03-16 14:30 (Critical: 24 horas) |
| **Reportado por** | TEC3 García Huanca, Jesús — JC-BC3 |
| **Reportado a** | TTE Rojas Sánchez, Carlos — Jefe de la Jefatura de Comunicaciones BC3 |

---

### La Cadena de Reporte Militar

El reporte de vulnerabilidad no termina cuando el Técnico completa el formulario. Sigue una cadena de decisión que determina qué acción se toma y con qué autoridad.

#### Flujo de Reporte

1. **El Técnico detecta la vulnerabilidad** — completa el reporte de vulnerabilidad con la plantilla estándar y lo entrega al Jefe inmediato dentro del plazo según severidad (24 horas para Critical, 7 días para High).
2. **El Jefe inmediato evalúa** — determina si la remediación está dentro de su autoridad y recursos. Si puede actuar, autoriza la acción y la documenta. Si la severidad es Critical o High, o si los recursos no son suficientes, escala al Oficial de Seguridad.
3. **El Oficial de Seguridad decide** — determina la acción formal: mitigar con los controles disponibles, aceptar formalmente el riesgo residual, o escalar al mando superior. Emite la resolución por escrito con fecha y firma. Si se acepta el riesgo, queda documentado quién tomó la decisión y con qué criterio.
4. **El Técnico implementa y cierra** — aplica la remediación o el control compensatorio autorizado, documenta el resultado (qué se hizo, cuándo, qué se verificó) y cierra el reporte con la fecha de resolución.

#### Roles y Responsabilidades

| Rol | Recibe | Decide | Plazo de respuesta |
|-----|--------|--------|--------------------|
| Técnico | Hallazgo en el sistema | Reportar — no acepta ni transfiere riesgos por cuenta propia | Inmediato: dentro del plazo de la severidad |
| Jefe inmediato | Reporte del Técnico | Autorizar acción o escalar | Mismo día de recibir el reporte |
| Oficial de Seguridad | Reporte escalado | Mitigar / aceptar formalmente / escalar a mando superior | Dentro del plazo de la severidad |

**¿Qué hace el Técnico si el Jefe no responde?**

Si el Jefe inmediato no responde dentro del plazo esperado (especialmente en vulnerabilidades Critical o High), el Técnico documenta el intento de reporte y escala directamente al Oficial de Seguridad, indicando en el reporte que se intentó escalar al Jefe sin respuesta. Este escalado directo debe ser la excepción, no la regla — pero en casos de riesgo Critical, el Técnico no puede esperar indefinidamente.

---

## Aplicación en Contexto Castrense

### Escenario: Firmware de radios HF con vulnerabilidad sin parche disponible

El Técnico de la JC-BC3 identifica que el firmware de las radios HF del batallón tiene un CVE con score 7.5 (High) publicado hace 3 semanas. El fabricante no ha publicado un parche aún — la única comunicación oficial es "estamos trabajando en una actualización".

**¿Qué control compensatorio aplica?**

No hay parche disponible, así que la remediación definitiva no es posible. El Técnico evalúa controles compensatorios:

- **Reducir la superficie de exposición:** Las radios HF solo se usan para frecuencias autorizadas. Si la vulnerabilidad requiere que un actor envíe un paquete especialmente crafteado al receptor, reducir el tiempo de recepción en frecuencias no operacionales limita la ventana de ataque.
- **Monitoreo aumentado:** Aumentar la frecuencia de revisión de las radios — verificar después de cada sesión que el firmware no ha sido modificado (comparando el hash de versión con el valor esperado según el manual del fabricante).
- **Plan de contingencia:** Identificar radios de respaldo con firmware en versión anterior sin la vulnerabilidad, o protocolo de comunicación alternativo si una radio HF queda comprometida.

**¿Cómo llena el campo "Control compensatorio" en la plantilla?**

!!! example "Ejemplo de control compensatorio para el campo 'Control compensatorio'"
    Control compensatorio: Hasta la publicación del parche por el fabricante, aplicar:
    (1) Restricción de uso a frecuencias operacionales autorizadas únicamente.
    (2) Verificación de integridad de firmware post-sesión según manual técnico.
    (3) Radio de respaldo designada como contingencia si se detecta comportamiento anómalo.
    Referencia: incidente reportado a Oficial de Seguridad, reporte VUL-2024-007, 2024-03-15.

**¿A quién reporta y en qué plazo?**

High = 7 días de plazo de remediación. Sin parche disponible, el Técnico reporta el hallazgo al Jefe inmediato el mismo día de detectarlo. El Oficial de Seguridad debe recibir el reporte escalado y emitir una resolución formal (mitigar con controles compensatorios + monitoreo hasta que el parche esté disponible). La aceptación del riesgo residual debe quedar firmada por el Oficial de Seguridad.

---

## Resumen

- Los controles de seguridad se clasifican como **preventivos** (evitan el incidente), **detectivos** (alertan cuando ocurre) o **correctivos** (restauran el estado normal). Un control puede cumplir más de un rol.
- La **plantilla de reporte de vulnerabilidad** es el instrumento formal que convierte un hallazgo técnico en una decisión documentada. Sin reporte, la vulnerabilidad no existe para la cadena de mando.
- La **cadena de reporte** es: Técnico detecta → Jefe inmediato evalúa y actúa o escala → Oficial de Seguridad decide formalmente → Técnico implementa y cierra.
- El **Técnico no acepta ni transfiere riesgos** por cuenta propia — su rol es detectar, documentar y escalar.
- Cuando no hay parche disponible, el **control compensatorio** es la respuesta operacional: reduce el riesgo residual mientras se espera la solución definitiva.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- NIST SP 800-53 Rev.5 — Security and Privacy Controls for Information Systems: [https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) — Catálogo completo de controles de seguridad con categorías preventivas, detectivas y correctivas.

---
*Siguiente: [Examen — Gestión de Riesgos y Vulnerabilidades](examen.md)*
