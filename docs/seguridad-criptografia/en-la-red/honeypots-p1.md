---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# Honeypots: Tecnología de Engaño (Parte 1)

> **Duración:** 1 hora | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Definir qué es un honeypot y explicar por qué genera alertas de alta confianza
- Distinguir honeypots de baja interacción de honeypots de alta interacción en términos de seguridad y datos recopilados
- Explicar las consideraciones legales y éticas que rigen el despliegue de honeypots
- Identificar casos de uso concretos en entornos militares, incluyendo la detección de insider threats

---

## ¿Qué es un Honeypot?

Un honeypot es un recurso de red o sistema diseñado deliberadamente para ser encontrado y atacado, sin tener ningún uso operacional legítimo. Por definición, ningún usuario legítimo debería intentar conectarse a él en circunstancias normales. Por tanto, **cualquier acceso a un honeypot es sospechoso por definición**.

!!! note "Definición"
    Un **honeypot** es un sistema o recurso de red falso, desplegado intencionalmente para atraer a atacantes, con el objetivo de detectar su presencia, estudiar sus técnicas y recopilar inteligencia de amenazas. No tiene valor productivo: su único valor es como trampa y sensor de detección.

**Analogía:** Es como una trampa para ratones colocada en un almacén militar: no tiene valor operacional, por lo que su activación merece atención inmediata. Sin embargo, antes de atribuirla a un atacante se debe descartar un escaneo autorizado, una herramienta de monitoreo o una configuración equivocada.

**Honeynet:** Una red compuesta por múltiples honeypots que simulan una infraestructura completa (servidor web, servidor de archivos, servidor de base de datos, estaciones de trabajo). Concepto más avanzado — en esta clase trabajamos con un honeypot individual.

---

## Tipos de Honeypot

| Tipo | Seguridad | Datos recopilados | Ejemplo de herramienta |
|------|-----------|-------------------|----------------------|
| **Baja interacción** | Mayor — el atacante interactúa con una emulación limitada | Conexiones, IPs, puertos intentados, timing | Sensor TCP simple, emulador de banners |
| **Alta interacción** | Menor — el atacante interactúa con un sistema real | Todo lo anterior + comandos ejecutados, técnicas usadas, herramientas descargadas | Sistema Linux real con servicios deliberadamente vulnerables |

**Baja interacción:** El honeypot emula la apariencia de un servicio (responde a conexiones TCP, simula el banner de SSH o HTTP) pero no entrega un sistema operativo completo. El atacante habla con un proceso de emulación. Esto reduce el riesgo, pero no lo elimina: el software del honeypot también debe mantenerse contenido y actualizado.

**Alta interacción:** El honeypot ejecuta servicios reales en un sistema completo. El atacante puede comprometer el sistema "de verdad", ejecutar comandos, instalar herramientas. Esto permite recopilar inteligencia mucho más rica sobre las técnicas del atacante, pero introduce el riesgo de que el atacante escape del honeypot y comprometa otros sistemas de la red.

!!! warning "Alta interacción en entornos de clase"
    Los honeypots de alta interacción son inapropiados para laboratorios de clase porque un atacante que los compromete puede usar el sistema como plataforma para atacar otros equipos de la red del aula. En el laboratorio de esta asignatura, usamos exclusivamente honeypots de baja interacción operando en localhost.

---

## Valor Defensivo

Los honeypots aportan cuatro ventajas operacionales como complemento de otras herramientas defensivas:

1. **Detección temprana de alta confianza:** Un honeypot bien inventariado no recibe tráfico operacional normal. Por eso, una conexión justifica una investigación, aunque todavía se debe verificar si provino de una actividad autorizada o de un error de configuración.

2. **Inteligencia de amenazas:** ¿Qué técnicas usa el atacante? ¿Qué servicios busca? ¿Qué herramientas intenta usar? Los honeypots responden estas preguntas con datos reales capturados del atacante real, no de simulaciones.

3. **Detección de insider threats:** Si un empleado o personal interno escanea la red buscando recursos no autorizados, sus herramientas de escaneo contactarán el honeypot. El log del honeypot lo registra. Los atacantes internos son particularmente difíciles de detectar con herramientas perimetrales (firewall, IDS de perímetro) — el honeypot interno los captura.

4. **Deception (engaño activo):** El atacante invierte tiempo y esfuerzo en interactuar con recursos que no tienen valor real, mientras el equipo de seguridad tiene tiempo para detectar el ataque y preparar la respuesta. El tiempo que el atacante pierde en el honeypot es tiempo que el defensor gana.

!!! example "Aplicación en entorno castrense"
    Un honeypot configurado para parecer un servidor de archivos con el nombre `SERVIDOR-OPERACIONES-ALFA` y con recursos compartidos visibles en la red de la unidad. Ningún proceso operacional debería conectarse. Un intento genera una alerta de alta confianza: puede ser un atacante externo que comprometió un equipo, un script de ransomware, personal interno realizando reconocimiento no autorizado o un escaneo administrativo que no fue coordinado.

---

## Consideraciones Legales y Éticas

Dos aspectos legales y éticos que el alumno debe conocer antes de desplegar un honeypot:

**1. Autorización:** Desplegar un honeypot en una red requiere autorización explícita del responsable de la red y de la organización. El objetivo del honeypot es atraer actividad maliciosa: hacerlo en una red sin autorización puede crear responsabilidad legal para quien lo despliega, especialmente si el honeypot atrae o facilita ataques que afectan a terceros.

**2. Contención:** Si el honeypot está expuesto a internet (IP pública), atraerá atacantes reales externos. Si no está correctamente aislado, el atacante puede usar el honeypot como plataforma para atacar a terceros, creando responsabilidad legal para la organización que opera el honeypot. Los honeypots en producción deben estar en segmentos de red aislados, sin routing directo a la red interna ni a internet en ambas direcciones.

!!! warning "Honeypots en redes reales"
    En el contexto de este laboratorio, el honeypot opera exclusivamente en la red local del aula, en `localhost` o en la red de clase sin exposición a internet. **Nunca exponer el honeypot de clase a una IP pública.** La configuración correcta de contención es responsabilidad del instructor antes de iniciar la práctica.

---

## Honeytoken: La Versión Más Simple

Un **honeytoken** es un artefacto (archivo, credencial, entrada en base de datos, registro DNS) que dispara una alerta cuando alguien lo accede o lo usa. No requiere un servidor dedicado — es una trampa invisible integrada en la infraestructura existente.

**Ejemplos de honeytokens:**

- Una cuenta de usuario `honeyadmin` creada en el sistema con una contraseña registrada en una base de datos de credenciales — si alguien intenta autenticarse con esa cuenta, es alerta inmediata de que las credenciales fueron comprometidas
- Un archivo `CREDENCIALES_SISTEMA.xlsx` en un servidor de archivos, con el acceso auditado — cualquier apertura del archivo genera una alerta
- Una entrada DNS que apunta a un servidor de logging — si un equipo interno resuelve ese nombre, significa que alguien está escaneando el DNS interno

Los honeytokens son más simples de desplegar que un honeypot completo y pueden integrarse en infraestructuras existentes sin hardware adicional.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Durante el diseño de la red de una nueva instalación, el oficial de seguridad decide colocar tres honeytokens: un servidor de archivos con nombre "DATOS-CLASIFICADOS-TEMP" que solo registra accesos, un recurso compartido de red "BACKUP-OPS" visible pero vacío, y una cuenta de servicio "svc-maint-ops" en el directorio activo. Seis meses después, el sistema de monitoreo registra que la cuenta `svc-maint-ops` intentó autenticarse desde un equipo no autorizado a las 03:45. La investigación revela un ataque de movimiento lateral en curso: un adversario que comprometió un equipo perimetral estaba usando credenciales robadas para moverse por la red. Los honeytokens detectaron el ataque horas antes de que llegara a sistemas críticos reales.

---

## Resumen

- Un honeypot es un recurso sin uso operacional: cualquier conexión es sospechosa y produce una alerta de alta confianza que debe verificarse
- Los honeypots de baja interacción emulan servicios (más seguros, menos datos); los de alta interacción ejecutan servicios reales (más datos, mayor riesgo de escape del atacante)
- Los honeypots detectan insider threats de forma especialmente efectiva: los atacantes internos que escanean la red contactarán el honeypot igual que los externos
- El despliegue de honeypots requiere autorización explícita y correcta contención de red — un honeypot mal aislado puede convertirse en plataforma de ataques a terceros
- Los honeytokens son la versión más simple: artefactos o credenciales que generan alerta al ser accedidos, sin necesidad de infraestructura dedicada

---

*Siguiente: [Honeypots (Parte 2) — Despliegue y Análisis](honeypots-p2.md)*
