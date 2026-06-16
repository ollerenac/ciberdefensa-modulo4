---
# Horas asignadas: 3 hrs
# Tipo: Teoría
---

# Defensa en Profundidad — Capa de Aplicaciones (CONT-25)

> Esta clase cubre **Capa 5: Aplicaciones** del modelo de defensa en profundidad establecido en [Parte 1](capa-humana-perimetral-p1.md).

> **Duración:** 3 horas | **Asignatura:** Capas SI | **Unidad:** Defensa en Profundidad

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar la diferencia entre autenticación y autorización y por qué ambas son necesarias.
- Describir los controles de validación de entrada y por qué son la primera defensa a nivel de aplicación.
- Identificar las categorías de riesgo del OWASP Top 10 en términos conceptuales, sin necesidad de conocimiento de programación.
- Relacionar las vulnerabilidades de aplicación con el impacto en entornos militares.

---

> **Recapitulación:** La Parte 1 (CONT-23) cubrió la Capa 3 Red Interna (TLS, VPN, NAC) y la Parte 2 (CONT-24) cubrió la Capa 4 Endpoint (EDR, parches, USB). Esta clase baja un nivel más: la seguridad dentro de las aplicaciones mismas.

---

## Autenticación y Autorización: Dos Controles Distintos

La confusión entre autenticación y autorización es uno de los errores conceptuales más comunes en seguridad. Son dos preguntas completamente diferentes:

- **Autenticación:** ¿Quién eres? — Verificar la identidad de quien intenta acceder.
- **Autorización:** ¿Qué puedes hacer? — Verificar qué operaciones está permitido realizar a la identidad ya verificada.

!!! note "Definición"
    **Autenticación** es el proceso de verificar que una entidad es quien dice ser, mediante uno o más factores verificables. **Autorización** es el proceso de determinar qué recursos y operaciones puede acceder y ejecutar una entidad autenticada.

### Factores de Autenticación

Los factores de autenticación se clasifican en tres categorías. La autenticación multi-factor (MFA) combina dos o más categorías:

| Factor | Qué es | Ejemplo | Debilidad principal |
|--------|--------|---------|---------------------|
| **Algo que sabes** (conocimiento) | Información secreta | Contraseña, PIN, pregunta de seguridad | Puede ser robado (phishing), adivinado (fuerza bruta) o filtrado |
| **Algo que tienes** (posesión) | Objeto físico | Token OTP (One-Time Password), tarjeta inteligente, teléfono | Puede ser robado físicamente |
| **Algo que eres** (inherencia) | Característica biométrica | Huella dactilar, reconocimiento facial, iris | No puede cambiarse si es comprometido; problemas de privacidad |

**Autenticación Multi-Factor (MFA):** Combinar dos factores de categorías diferentes aumenta exponencialmente la dificultad de comprometer la autenticación. Si el atacante roba la contraseña (algo que sabes), todavía necesita el token físico (algo que tienes). Comprometer ambos simultáneamente requiere un nivel de acceso y sofisticación mucho mayor.

!!! example "Aplicación en entorno castrense"
    El sistema de correo clasificado del batallón requiere dos factores: la contraseña del usuario (algo que sabe) y la tarjeta inteligente de identificación militar introducida en el lector del equipo (algo que tiene). Si un oficial pierde su contraseña por un phishing, el atacante solo tiene el primer factor — sin la tarjeta física, no puede acceder. Si pierde la tarjeta, el atacante solo tiene el segundo factor — sin la contraseña, tampoco puede acceder.

### Autorización: Principio de Mínimo Privilegio

El principio de mínimo privilegio aplicado a autorización establece que cada usuario, proceso o sistema debe tener acceso **únicamente** a los recursos que necesita para su función específica — nada más.

Los modelos de autorización más comunes:

- **DAC (Discretionary Access Control):** El propietario del recurso decide quién puede acceder. Flexible pero inconsistente — depende de cada propietario tomar decisiones correctas.
- **MAC (Mandatory Access Control):** Un administrador central define las políticas de acceso basadas en etiquetas de clasificación. El usuario no puede cambiar los permisos. Típico en sistemas de información clasificada (SECRET, TOP SECRET).
- **RBAC (Role-Based Access Control):** Los permisos se asignan a roles (Administrador, Técnico, Oficial, Lector) y los usuarios se asignan a roles. Más manejable que asignar permisos individualmente a cada usuario.

!!! warning "Privilege creep — acumulación de privilegios"
    Con el tiempo, los usuarios acumulan permisos que ya no necesitan — un técnico que fue promovido a oficial y conserva sus permisos técnicos, o un empleado que cubrió una baja temporal en otro departamento y nunca le retiraron el acceso. Esta acumulación (privilege creep) viola el principio de mínimo privilegio y amplía la superficie de ataque. Las revisiones periódicas de accesos (access reviews) son el control para detectar y corregir este problema.

---

## Validación de Entrada: La Primera Defensa en la Aplicación

Las aplicaciones reciben datos de los usuarios: formularios, archivos, parámetros en URLs, mensajes de API. Si la aplicación confía ciegamente en esos datos y los procesa sin validarlos, un atacante puede enviar datos maliciosos que la aplicación ejecuta como si fueran instrucciones legítimas.

!!! note "Definición"
    **Validación de entrada** es el proceso de verificar que los datos recibidos por una aplicación cumplen el formato, tipo, rango y contenido esperados antes de procesarlos, rechazando cualquier dato que no cumpla esos criterios.

El principio fundamental es: **nunca confíes en datos que vienen del exterior de la aplicación**. Esto incluye:

- Formularios web enviados por usuarios
- Parámetros en URLs
- Archivos subidos al sistema
- Datos recibidos de otras aplicaciones o APIs
- Parámetros en cookies

La validación de entrada no es solo una medida de seguridad — también es calidad de datos. Una aplicación que valida correctamente sus entradas funciona de manera más predecible y robusta.

---

## OWASP Top 10: Las Vulnerabilidades Más Críticas en Aplicaciones

OWASP (Open Web Application Security Project) es una fundación sin fines de lucro que publica, entre otros recursos, el **OWASP Top 10** — una lista de las 10 categorías de vulnerabilidades más críticas y prevalentes en aplicaciones web. No es una lista de bugs específicos, sino de **categorías de riesgo** que sirven como referencia para el sector.

Esta lista no es para programadores: un oficial o técnico de comunicaciones que la conoce puede identificar si un sistema tiene vulnerabilidades de alto riesgo y priorizar qué preguntas hacer al proveedor o al equipo de desarrollo.

### OWASP Top 10 (2021) — Visión Conceptual

| # | Categoría | Concepto simple | Ejemplo de riesgo |
|---|-----------|----------------|-------------------|
| **A01** | **Control de Acceso Roto** (Broken Access Control) | Un usuario puede hacer cosas que no debería poder hacer — acceder a datos de otro usuario, acceder a funciones de administrador | Técnico que modifica un parámetro en la URL y accede a registros de otro usuario |
| **A02** | **Fallos Criptográficos** (Cryptographic Failures) | Datos sensibles transmitidos o almacenados sin cifrar, o con cifrado débil | Contraseñas almacenadas en texto claro; comunicaciones sin TLS |
| **A03** | **Inyección** (Injection) | Datos de usuario que son interpretados como instrucciones — SQL injection, command injection | Atacante introduce código SQL en un campo de búsqueda; la aplicación ejecuta ese código en la base de datos |
| **A04** | **Diseño Inseguro** (Insecure Design) | Vulnerabilidades en la arquitectura del sistema, no en el código | Proceso de recuperación de contraseña que solo pregunta datos públicos fáciles de adivinar |
| **A05** | **Configuración de Seguridad Incorrecta** (Security Misconfiguration) | Software instalado con configuración por defecto insegura, funciones innecesarias activas, credenciales por defecto sin cambiar | Servidor web con panel de administración accesible con usuario "admin" / contraseña "admin" |
| **A06** | **Componentes Vulnerables y Desactualizados** (Vulnerable and Outdated Components) | Bibliotecas, frameworks o software base con vulnerabilidades conocidas que no se han actualizado | Aplicación web usando una versión de Apache con vulnerabilidad crítica conocida publicada hace 6 meses |
| **A07** | **Fallos de Identificación y Autenticación** (Identification and Authentication Failures) | Problemas en el proceso de verificar quién es el usuario — contraseñas débiles permitidas, sin MFA, sesiones que no expiran | Sistema que no bloquea tras múltiples intentos fallidos de contraseña; permite contraseñas de 4 caracteres |
| **A08** | **Fallos de Integridad de Software y Datos** (Software and Data Integrity Failures) | Actualizaciones de software descargadas sin verificar su integridad criptográfica | Actualización de firmware que no tiene firma digital verificada — cualquiera puede sustituirla |
| **A09** | **Fallos en el Registro y Monitoreo de Seguridad** (Security Logging and Monitoring Failures) | No registrar eventos de seguridad, o registrarlos pero no monitorizarlos | Sistema que no registra intentos fallidos de autenticación; incidente pasa desapercibido durante semanas |
| **A10** | **Falsificación de Solicitudes del Lado del Servidor** (Server-Side Request Forgery) | La aplicación recupera recursos de URLs externas sin validar — el atacante la usa para acceder a sistemas internos | Función "previsualizar URL" que el atacante usa para que el servidor acceda a recursos de la red interna |

!!! example "Aplicación en entorno castrense"
    El portal web de gestión de permisos del batallón tiene una función de búsqueda de personal por nombre. Un atacante introduce `'; DROP TABLE personal; --` en el campo de búsqueda. Si la aplicación no valida la entrada (A03 — Inyección), esta cadena se ejecuta como comando SQL en la base de datos, borrando la tabla de personal. En un escenario real de operaciones, perder la base de datos de personal con autorización de acceso puede comprometer toda la operación.

    La mitigación es la validación de entrada combinada con el uso de consultas parametrizadas (prepared statements) — la aplicación nunca interpreta el input del usuario como código SQL, sino solo como texto literal.

!!! example "Aplicación en entorno castrense"
    Un sistema de información de misión tiene un panel de administración que nunca fue explícitamente securizado porque "solo se accede internamente". El panel usa las credenciales por defecto del fabricante (`admin`/`admin`). Un atacante que llega a la red interna (quizás comprometiendo un endpoint en la Capa 4) accede al panel de administración, crea un nuevo usuario con privilegios de administrador, y tiene control total del sistema de información de misión. Este es A05 (Configuración de Seguridad Incorrecta) combinado con A07 (Fallos de Autenticación).

---

## Resumen

1. Autenticación (¿quién eres?) y autorización (¿qué puedes hacer?) son controles distintos; ambos son necesarios y el principio de mínimo privilegio rige la autorización.
2. La autenticación multi-factor (MFA) combina dos factores de categorías diferentes — robarlo ambos simultáneamente es exponencialmente más difícil.
3. La validación de entrada verifica que los datos externos cumplen el formato esperado antes de procesarlos — es la defensa primaria contra ataques de inyección.
4. El OWASP Top 10 define las 10 categorías de riesgo más críticas en aplicaciones; conocerlas permite identificar y priorizar vulnerabilidades sin ser programador.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **OWASP Top 10 (edición 2021):** https://owasp.org/Top10/
- **NIST SP 800-95 — Guide to Secure Web Services:** https://csrc.nist.gov/publications/detail/sp/800-95/final

---

*Siguiente: [Capa de Aplicaciones y Datos (Parte 2) — Clasificación, Cifrado en Reposo y DLP](capa-aplicaciones-datos-p2.md)*
