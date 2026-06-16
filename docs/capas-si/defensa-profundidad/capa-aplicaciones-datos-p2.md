---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Defensa en Profundidad — Capa de Datos (CONT-26)

> Esta clase cubre **Capa 6: Datos** del modelo de defensa en profundidad establecido en [Parte 1](capa-humana-perimetral-p1.md).

> **Duración:** 2 horas | **Asignatura:** Capas SI | **Unidad:** Defensa en Profundidad

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Aplicar un esquema de clasificación de información en niveles al contexto militar.
- Explicar el cifrado en reposo y su aplicación práctica con BitLocker.
- Describir los controles DLP y cómo previenen la exfiltración de datos.

---

> **Recapitulación:** La Parte 1 (CONT-25) cubrió la Capa 5 Aplicaciones: autenticación/autorización, validación de entrada y las 10 categorías del OWASP Top 10. Esta clase cubre la Capa 6: los datos mismos — su clasificación, su protección en reposo y la prevención de su salida no autorizada.

---

## Clasificación de la Información

No toda la información tiene el mismo valor ni el mismo nivel de sensibilidad. Tratar todos los datos con el mismo nivel de protección es ineficiente (proteger en exceso datos públicos es un desperdicio) y peligroso (proteger insuficientemente datos críticos es una vulnerabilidad).

La clasificación de la información es el proceso de asignar a cada dato un nivel de sensibilidad que determina qué controles de seguridad se aplican.

!!! note "Definición"
    La **clasificación de la información** es el proceso de categorizar los activos de información según su nivel de sensibilidad y el impacto que tendría su divulgación no autorizada, para aplicar controles de seguridad proporcionales a ese nivel.

### Niveles de Clasificación en Contexto Militar

Los sistemas militares utilizan esquemas de clasificación formales. En el contexto de esta asignatura, los niveles de referencia son:

| Nivel | Descripción | Impacto de divulgación | Ejemplo de contenido |
|-------|-------------|----------------------|----------------------|
| **Público** | Información que puede divulgarse libremente sin impacto | Ninguno | Comunicados de prensa, información de reclutamiento general |
| **Interno** | Información operacional no sensible; uso interno de la organización | Bajo — afecta operaciones pero no misiones críticas | Calendarios internos, procedimientos administrativos, directorio interno |
| **Confidencial** | Información sensible que de divulgarse comprometería operaciones | Medio — comprometería operaciones o personal | Planes de operación en fase de preparación, información de personal, datos de inteligencia táctica |
| **Secreto** | Información que de divulgarse causaría daño grave a la seguridad nacional o la misión | Alto — compromete la misión, expone personal o activos | Órdenes de operaciones activas, información de fuentes de inteligencia, frecuencias y códigos operacionales |

**Reglas de manejo por nivel:**

| Nivel | Almacenamiento | Transmisión | Destrucción |
|-------|---------------|-------------|------------|
| Público | Sin restricciones | Sin restricciones | Sin requisitos especiales |
| Interno | Equipos corporativos; sin USBs personales | Red interna o VPN | Eliminar con vaciado de papelera |
| Confidencial | Equipos corporativos cifrados; acceso con credenciales | Solo en canales cifrados (TLS/VPN) | Borrado seguro (sobreescritura múltiple) |
| Secreto | Sistemas homologados para clasificación; separados de sistemas de red general | Solo en redes clasificadas dedicadas | Destrucción física certificada |

!!! example "Aplicación en entorno castrense"
    Un técnico de comunicaciones recibe un archivo de Excel con los turnos de guardia de la semana siguiente. Ese archivo es "Interno" — nadie fuera del batallón necesita verlo, pero su divulgación no compromete ninguna misión activa. Lo almacena en la carpeta de red interna sin cifrado adicional y lo transmite por correo interno. Por contraste, el plan de operaciones para el ejercicio del próximo mes es "Confidencial" — solo el personal con habilitación específica puede acceder, solo se almacena en equipos con disco cifrado, y solo se transmite por la VPN con TLS.

---

## Cifrado en Reposo: BitLocker

El cifrado en tránsito (TLS, VPN — Capa 3) protege los datos mientras se mueven por la red. El cifrado en reposo protege los datos cuando están almacenados — en el disco duro de un portátil, en un servidor, en un USB.

El escenario que el cifrado en reposo resuelve: un portátil corporativo es robado o perdido. Sin cifrado en reposo, el ladrón saca el disco, lo conecta a otro equipo y lee todos los archivos. Con cifrado en reposo, los datos en disco son ininteligibles sin la clave de descifrado.

!!! note "Definición"
    El **cifrado en reposo** (*encryption at rest*) es la protección de datos almacenados mediante cifrado, de forma que el acceso físico al medio de almacenamiento (disco, USB, servidor) no otorga acceso a los datos sin la clave de descifrado.

### BitLocker: Cifrado de Disco en Windows

BitLocker es la solución de cifrado de disco incluida en Windows Pro y Enterprise. Cifra el disco completo (o particiones seleccionadas) y requiere autenticación antes del arranque.

**Funcionamiento conceptual:**

```
Sin BitLocker:
[Disco con datos] ─── acceso físico ───▶ [Cualquiera puede leer]

Con BitLocker:
[Disco cifrado AES-256] ─── acceso físico ───▶ [Datos cifrados; ilegibles]
                         ─── PIN + TPM ────▶ [Datos descifrados; accesibles solo al usuario autorizado]
```

**TPM (Trusted Platform Module):** Chip de seguridad en la placa base que almacena las claves de cifrado de BitLocker. El disco cifrado solo se puede desbloquear en el equipo donde se instaló BitLocker (el que tiene ese TPM específico) — incluso si el ladrón extrae el disco y lo conecta a otro equipo, no puede descifrarlo porque el TPM no está.

| Modo de autenticación BitLocker | Seguridad | Comodidad |
|---------------------------------|-----------|-----------|
| Solo TPM | Media — el disco se descifra automáticamente en el equipo original | Alta — usuario no nota nada; arranque transparente |
| TPM + PIN | Alta — se requiere PIN además del TPM | Media — usuario debe introducir PIN en cada arranque |
| TPM + PIN + USB de arranque | Muy alta — tres factores | Baja — proceso de arranque complejo |

Para equipos móviles (portátiles de trabajo de campo), el modo **TPM + PIN** es el recomendado — protege contra robo del equipo completo (el PIN no está en el disco) y es suficientemente cómodo para uso diario.

!!! tip "Clave de recuperación BitLocker"
    BitLocker genera una clave de recuperación de 48 dígitos al activarse. Esta clave permite desbloquear el disco si el TPM falla o si el equipo es sustituido. La clave de recuperación debe almacenarse de forma segura FUERA del equipo cifrado — en Active Directory (automático en entornos con dominio) o en impresión física guardada en un lugar seguro.

---

## DLP: Prevención de Pérdida de Datos

DLP (Data Loss Prevention) es un conjunto de tecnologías y políticas que detectan y previenen la transmisión no autorizada de información sensible fuera de la organización.

!!! note "Definición"
    **DLP** (Data Loss Prevention) es la estrategia y el conjunto de herramientas que identifican, monitorizan y bloquean el movimiento de datos sensibles fuera de los límites definidos por la organización, ya sea por error o de forma intencional.

### Canales que el DLP Monitorea

| Canal | Riesgo | Control DLP |
|-------|--------|-------------|
| **Correo electrónico** | Enviar documento confidencial a dirección externa por error o intencionalmente | Inspección del adjunto antes de enviar; alerta o bloqueo si contiene datos clasificados |
| **USB y dispositivos externos** | Copiar datos a USB personal para llevarse a casa | Agente DLP en el endpoint que detecta la copia y la bloquea o registra |
| **Subida a la nube** | Subir documentos a Google Drive, Dropbox personal | Proxy de red que inspecciona el tráfico HTTPS y bloquea subidas a servicios no autorizados |
| **Impresión** | Imprimir documentos confidenciales en impresoras no controladas | Control de impresoras: solo impresoras corporativas aprobadas; registro de qué se imprime |
| **Captura de pantalla** | Software de captura de pantalla instalado por el usuario | Agente DLP que detecta y bloquea aplicaciones de captura |

**DLP basado en contenido vs. DLP basado en etiqueta:**

- **Basado en contenido:** El DLP inspecciona el contenido del documento y detecta patrones — números de tarjeta de crédito, nombres de operaciones militares específicos, documentos que contienen palabras clave de clasificación.
- **Basado en etiqueta:** Los documentos llevan una etiqueta de clasificación (metadato) que indica su nivel de sensibilidad. El DLP aplica las políticas correspondientes a esa etiqueta automáticamente.

!!! example "Aplicación en entorno castrense"
    Un oficial de enlace envía por correo electrónico un informe de situación con el nivel "Confidencial" a una dirección de correo personal para seguir trabajando desde casa. El sistema DLP detecta la etiqueta de clasificación "Confidencial" en el adjunto, bloquea el envío y genera una alerta al oficial de seguridad. El oficial de enlace recibe un mensaje que indica que ese documento requiere el canal VPN para trabajar en remoto. La exfiltración accidental fue bloqueada antes de producirse.

---

## Resumen

1. La clasificación de la información asigna niveles de sensibilidad (Público / Interno / Confidencial / Secreto) que determinan los controles aplicables — proporcionalidad entre protección y criticidad.
2. El cifrado en reposo (BitLocker) protege los datos almacenados en disco — el acceso físico al equipo no otorga acceso a los datos sin la clave.
3. DLP detecta y bloquea la transmisión no autorizada de datos sensibles por correo, USB, nube e impresión, basándose en el contenido o la etiqueta de clasificación.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Microsoft BitLocker Documentation:** https://learn.microsoft.com/en-us/windows/security/operating-system-security/data-protection/bitlocker/
- **NIST SP 800-111 — Guide to Storage Encryption Technologies for End User Devices:** https://csrc.nist.gov/publications/detail/sp/800-111/final

---

*Siguiente: [Capa de Activos Críticos (Parte 1) — Activos Militares y Continuidad de Operaciones](capa-activos-criticos-p1.md)*
