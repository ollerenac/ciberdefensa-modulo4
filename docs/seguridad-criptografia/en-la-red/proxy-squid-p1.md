---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# Seguridad en la Red: Proxy Squid (Parte 1)

> **Duración:** 1 hora | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar qué es un proxy forward y cómo difiere de un firewall en su función y capa de operación
- Describir el flujo de tráfico web a través de un proxy, desde la petición del cliente hasta la respuesta
- Identificar los beneficios del proxy en redes corporativas y militares: control, registro, caché y políticas centralizadas
- Introducir el concepto de SSL Bump para inspección de HTTPS (conceptual, sin laboratorio)

---

## ¿Qué es un Proxy Forward?

Un proxy forward es un servidor intermediario entre los clientes de una red interna y los servidores de internet. Cuando un cliente desea acceder a una página web, en lugar de conectarse directamente al servidor destino, envía su petición al proxy. El proxy evalúa si esa petición está permitida, la realiza en nombre del cliente y devuelve la respuesta.

!!! note "Definición"
    Un **proxy forward** es un servidor que actúa como intermediario entre clientes internos e internet, interceptando peticiones HTTP/HTTPS salientes para aplicar políticas de acceso, registrar actividad y opcionalmente almacenar contenido en caché.

**Analogía:** Es como un correo militar clasificado: el mensajero (proxy) lleva y trae paquetes entre el cuartel y el mundo exterior, pero registra todo lo que transporta, verifica que cada paquete esté autorizado y puede rechazar los que no cumplan las normas. El destinatario exterior nunca conoce directamente la dirección interna del remitente.

---

## Proxy Forward vs. Proxy Inverso

Los alumnos confunden con frecuencia estas dos variantes. La diferencia es fundamental:

| Tipo | Quién protege | Posición en la red | Ejemplo de uso |
|------|--------------|-------------------|----------------|
| **Proxy Forward** | Los clientes internos | Delante de los clientes | Squid en una red corporativa o militar — controla lo que accede el personal |
| **Proxy Inverso** | Los servidores propios | Delante de los servidores | nginx como reverse proxy — recibe peticiones de internet y las distribuye a servidores internos |

!!! warning "Confusión habitual"
    El proxy **forward** controla lo que los usuarios internos acceden en internet.
    El proxy **inverso** controla lo que llega desde internet hacia los servidores propios.
    Son herramientas distintas para amenazas distintas. Squid es un proxy forward.

---

## Flujo de Tráfico Web a Través de un Proxy

Cuando el navegador está configurado para usar un proxy, el flujo de una petición es:

```
Cliente (navegador)
   │ 1. Petición HTTP al proxy (no al servidor destino)
   ▼
Proxy Squid (localhost:3128)
   │ 2. Proxy evalúa ACLs (¿está permitido este dominio?)
   │ 3. Si está permitido: proxy hace la petición al servidor destino
   ▼
Servidor destino (example.com)
   │ 4. Servidor responde al proxy
   ▼
Proxy Squid
   │ 5. Proxy registra en access.log y devuelve la respuesta al cliente
   ▼
Cliente (recibe la respuesta)
```

El paso 2 es donde reside el poder del proxy: las **ACLs (Access Control Lists)** son listas de reglas que determinan qué dominios, palabras clave o patrones de URL están permitidos o bloqueados. Si la ACL deniega la petición, el cliente recibe un mensaje de error de Squid y la petición nunca llega al servidor destino.

---

## Beneficios en Redes Corporativas y Militares

Un proxy bien configurado proporciona cuatro ventajas operacionales concretas:

1. **Control de acceso:** Bloquear dominios no autorizados — redes sociales, entretenimiento, sitios de descarga, dominios conocidos como maliciosos. El control es centralizado: una sola regla en el servidor proxy afecta a todos los equipos de la red.

2. **Registro de actividad:** El `access.log` de Squid muestra quién (IP del cliente) accedió a qué URL y cuándo. Este registro es fundamental para auditoría de seguridad y respuesta a incidentes: permite reconstruir qué equipos se comunicaron con un dominio malicioso y qué datos se transfirieron.

3. **Caché de contenido:** Squid puede almacenar localmente respuestas frecuentes. Si 10 equipos descargan la misma actualización de software, Squid la descarga una vez y sirve las 9 copias restantes desde la caché local, reduciendo el consumo de ancho de banda.

4. **Aplicación de políticas centralizadas:** Una única configuración en el servidor proxy aplica las políticas de uso de internet a toda la red, sin necesidad de configurar cada equipo individualmente.

!!! example "Aplicación en entorno castrense"
    En una base militar con 50 equipos y acceso a internet por satélite (costoso y con ancho de banda limitado), Squid cumple dos misiones simultáneamente: primero, reduce el consumo de ancho de banda mediante la caché, lo que disminuye el coste de la conexión satelital. Segundo, el oficial de seguridad puede revisar el `access.log` al final del día y detectar si algún equipo accedió a dominios sospechosos o realizó transferencias de datos inusuales hacia el exterior.

---

## Limitación Importante: El Proxy no es Suficiente Solo

Un proxy forward solo controla el tráfico que pasa **a través de él**. Si un usuario configura su navegador para NO usar el proxy, o usa una aplicación que no respeta la configuración de proxy del sistema operativo, el tráfico sale directamente a internet sin pasar por Squid.

**Solución:** El proxy debe estar acompañado de una regla de firewall que bloquee el tráfico HTTP/HTTPS saliente que no provenga del proceso Squid. En Linux se usa iptables; en Windows, el Firewall de Windows con Seguridad Avanzada (WFAS). Esto convierte el proxy en un punto de paso obligatorio, no opcional.

```
Sin refuerzo de firewall — el usuario puede bypassear el proxy:
    Cliente → (sin proxy configurado) → Internet directamente

Con proxy + regla de firewall:
    Cliente → Proxy Squid (obligatorio) → Internet
    Cliente → Intento directo → BLOQUEADO por firewall
```

---

## Concepto de SSL Bump (Inspección HTTPS)

HTTPS cifra el contenido entre el navegador y el servidor. Con HTTPS, el proxy puede ver el dominio destino (en la cabecera `CONNECT`) pero no puede inspeccionar la URL completa ni el contenido de la petición.

**SSL Bump** es una técnica que rompe el cifrado HTTPS en el proxy para inspeccionar el contenido: el proxy genera un certificado firmado por la propia organización y lo presenta al cliente. Para que funcione, el certificado de la organización debe estar instalado en todos los clientes como autoridad de certificación de confianza.

!!! warning "Implicaciones legales y éticas del SSL Bump"
    Interceptar comunicaciones HTTPS puede incluir correo electrónico personal, banca en línea y otras comunicaciones privadas. En muchos países esto puede violar leyes de privacidad. En contextos militares, SSL Bump requiere **autorización explícita** de los responsables legales y debe estar documentado en las políticas de uso aceptable de la red. En este curso no se realizará laboratorio de SSL Bump.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Un Técnico configura Squid en el servidor de la unidad. Los 40 ordenadores del cuartel usan ese proxy como punto de salida a internet. El oficial de seguridad recibe una alerta indicando que un dominio de la lista de indicadores de compromiso fue contactado desde la red. El oficial revisa el `access.log` de Squid y en segundos identifica la IP interna del equipo, la hora exacta y la URL completa accedida. Sin el proxy, solo tendría el dato del firewall: "IP interna habló con IP externa", sin URL, sin tipo de tráfico, sin la capacidad de determinar si fue exfiltración o un clic accidental en un enlace malicioso.

---

## Resumen

- Un proxy forward actúa como intermediario entre clientes internos e internet, interceptando peticiones HTTP/HTTPS salientes para aplicar políticas de acceso
- El proxy evalúa ACLs para permitir o denegar cada petición — esto lo diferencia del firewall, que opera en capa 3/4 con IPs y puertos; el proxy opera en capa 7 con URLs y contenido
- El `access.log` de Squid registra toda la actividad web, proporcionando visibilidad detallada para auditoría y respuesta a incidentes
- Un proxy sin regla de firewall complementaria puede ser bypasseado — el control de acceso completo requiere ambas capas trabajando juntas

---

*Siguiente: [Proxy Squid (Parte 2) — Configuración y Laboratorio](proxy-squid-p2.md)*
