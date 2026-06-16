---
# Horas asignadas: 1 hr
# Tipo: Examen
---

# Examen Final — Unidad: En la Red

> **Duración:** 1 hora | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red

!!! warning "Instrucciones"
    - Este examen cubre los temas de la Unidad "En la Red": Proxy Squid (Partes 1 y 2), IDS e IPS con Snort (Partes 1 y 2), y Honeypots (Partes 1 y 2).
    - Responder individualmente y sin consultar apuntes salvo que el instructor lo indique.
    - Duración total: 60 minutos.
    - Sección A vale 12 puntos (1 punto por pregunta). Sección B vale 4 puntos (1 punto por pregunta). Sección C vale 4 puntos (1 punto por pregunta). Total: 20 puntos.

---

## Sección A: Opción Múltiple (Preguntas 1-12)

*Seleccionar la única respuesta correcta para cada pregunta.*

---

**P1.** ¿Cuál es la diferencia funcional principal entre un proxy forward y un firewall?

A) El proxy analiza el contenido de las peticiones HTTP; el firewall analiza solo puertos e IPs

B) El firewall bloquea puertos; el proxy redirige todo el tráfico a una IP diferente

C) El proxy funciona en capa 7 (aplicación) controlando URLs y contenido; el firewall funciona en capa 3/4 controlando IPs y puertos

D) No hay diferencia práctica — ambos controlan el mismo tráfico de la misma manera

---

**P2.** ¿Qué columna del `access.log` de Squid indica si el proxy entregó el contenido desde su caché local o lo descargó del servidor destino?

A) La columna de bytes transferidos

B) El código Squid (TCP_HIT para contenido de caché, TCP_MISS para descarga directa del servidor)

C) El método HTTP (GET vs POST)

D) La URL completa de la petición

---

**P3.** Un alumno configura Squid para bloquear `facebook.com` con una ACL correcta, pero puede seguir accediendo a ese dominio cuando desactiva el proxy en su navegador. ¿Por qué?

A) El `squid.conf` tiene un error de sintaxis que hace inefectiva la ACL

B) El navegador tiene caché de DNS y resuelve la IP directamente sin consultar el proxy

C) Squid solo controla el tráfico que pasa a través de él — sin una regla de firewall que fuerce el uso del proxy, el navegador puede conectarse directamente a internet

D) `facebook.com` usa HTTPS y Squid no puede bloquear tráfico HTTPS sin SSL Bump

---

**P4.** ¿Cuál es la diferencia entre un IDS y un IPS en términos de impacto operacional?

A) El IDS es más caro y complejo de configurar que el IPS

B) El IPS puede interrumpir tráfico legítimo si genera falsos positivos; el IDS solo alerta sin afectar el flujo de tráfico

C) El IDS solo funciona en redes WiFi; el IPS funciona en redes cableadas

D) El IPS requiere una licencia de pago; el IDS siempre es gratuito y de código abierto

---

**P5.** ¿Qué significa un "falso positivo" en el contexto de un IDS?

A) Una alerta que no se generó cuando debería haberse generado ante tráfico malicioso real

B) Una alerta generada por tráfico malicioso real que el IDS detectó correctamente

C) Una alerta generada por tráfico legítimo que coincidió erróneamente con una firma de ataque conocida

D) Un error en el archivo de configuración del IDS que produce comportamiento inesperado

---

**P6.** Analice la siguiente regla Snort:

```
alert tcp any any -> $HOME_NET 23 (msg:"Telnet"; sid:1000001; rev:1;)
```

¿Qué tráfico detecta esta regla?

A) Todo el tráfico TCP que sale de la red interna hacia cualquier destino externo en el puerto 23

B) Todo el tráfico TCP desde cualquier origen hacia cualquier equipo de la red interna ($HOME_NET) en el puerto 23 (destino Telnet)

C) Solo el tráfico TCP desde IPs externas hacia el servidor de seguridad de la red en el puerto 23

D) El tráfico UDP en el puerto 23 que intenta establecer una sesión Telnet cifrada

---

**P7.** ¿Por qué se recomienda usar la opción `threshold` en reglas Snort para eventos frecuentes como pings?

A) Porque el `threshold` mejora la velocidad de detección al reducir el procesamiento por paquete

B) Para evitar el "alert storm": un solo evento de ping repetido puede generar cientos de alertas idénticas en segundos, saturando la consola e impidiendo ver otros eventos más graves

C) Porque sin `threshold`, Snort no puede detectar paquetes ICMP en la interfaz de red

D) Para reducir el uso de CPU del servidor donde corre Snort en entornos de producción

---

**P8.** ¿Por qué cualquier conexión a un honeypot es inherentemente sospechosa?

A) Porque los honeypots usan protocolos obsoletos que los usuarios normales no utilizan en la red

B) Porque el honeypot bloquea automáticamente las IPs conocidas como seguras antes de registrar

C) Porque un honeypot no tiene ningún uso legítimo — ningún usuario normal debería conectarse a él en circunstancias normales

D) Porque los honeypots están configurados para rechazar todo el tráfico excepto el que proviene de IPs de atacantes conocidas

---

**P9.** ¿Cuál es la diferencia entre un honeypot de baja interacción y uno de alta interacción?

A) El de alta interacción solo funciona en redes WiFi; el de baja interacción funciona en cualquier tipo de red

B) El de baja interacción emula servicios sin ejecutar un sistema real (más seguro, menos datos recopilados); el de alta interacción ejecuta servicios reales y recopila más inteligencia pero presenta mayor riesgo si el atacante escapa

C) El de baja interacción solo puede detectar conexiones TCP; el de alta interacción también detecta conexiones UDP y ICMP

D) No hay diferencia técnica entre ambos — la distinción es únicamente en el precio del software utilizado

---

**P10.** Un honeypot en una red militar interna registra una conexión desde la IP 192.168.10.23, que pertenece al ordenador asignado a un oficial. ¿Cuál es la interpretación más adecuada?

A) Es una conexión de mantenimiento normal del equipo de administración de TI

B) El oficial probablemente hizo clic accidentalmente en un enlace que redirigió al honeypot

C) Puede indicar que el equipo del oficial está comprometido y realizando reconocimiento lateral, o que el oficial está explorando recursos de red no autorizados — ambos requieren investigación

D) El firewall perimetral está mal configurado y está redirigiendo tráfico normal al honeypot sin motivo

---

**P11.** ¿Qué ventaja tiene el control de navegación por proxy sobre el control por firewall de capa 3/4 para detectar posible exfiltración de datos?

A) El proxy puede bloquear HTTPS y el firewall de capa 3/4 no puede bloquear ese protocolo

B) El proxy registra las URLs completas (incluyendo paths y parámetros) y el tipo de contenido, lo que permite identificar qué datos se enviaron hacia qué destino específico, con una granularidad imposible para un firewall de capa 3/4

C) El firewall de capa 3/4 no puede bloquear el tráfico HTTP, pero el proxy sí puede hacerlo

D) El proxy es más rápido que el firewall porque trabaja directamente en capa 3 con direcciones IP

---

**P12.** En una regla Snort, ¿qué significa la opción `content:"cmd.exe"; nocase;`?

A) Ejecuta el archivo `cmd.exe` en el servidor Snort cuando se detecta la amenaza para analizar el malware

B) Busca la cadena literal `cmd.exe` en el payload del paquete, sin distinción entre mayúsculas y minúsculas (detecta `CMD.EXE`, `Cmd.Exe`, etc.)

C) Bloquea cualquier proceso del sistema operativo que tenga `cmd.exe` en su nombre de ejecutable

D) Es el identificador único numérico de la firma en la base de datos oficial de reglas de Snort

---

## Sección B: Identificación y Análisis (Preguntas 13-16)

*Analizar el escenario o dato presentado y responder con precisión. 1 punto por pregunta.*

---

**P13.** Se muestra el siguiente extracto del `access.log` de Squid:

```
1718500200.123  850  192.168.1.55  TCP_MISS/200  128450  POST  http://upload.transferservice.net/data  -  DIRECT/203.0.113.10  application/octet-stream
```

¿Qué información revela esta línea y qué aspectos son potencialmente preocupantes desde el punto de vista de la seguridad?

---

**P14.** Se muestra la siguiente alerta generada por Snort:

```
[**] [1:1000012:1] HTTP con cadena sospechosa cmd.exe [**]
[Priority: 0]
06/16-02:17:43.112233 192.168.1.100:54321 -> 93.184.216.34:80
TCP TTL:128 TOS:0x0 ID:4321 IpLen:20 DgmLen:500
```

¿Qué indica esta alerta y cuál es el siguiente paso recomendado para el analista de seguridad?

---

**P15.** Se muestra el log de un honeypot con las siguientes entradas registradas en un período de 5 minutos:

```
[14:20:01] CONEXION desde 192.168.1.77:1024 - puerto honeypot 22 (SSH)
[14:20:01] CONEXION desde 192.168.1.77:1025 - puerto honeypot 23 (Telnet)
[14:20:02] CONEXION desde 192.168.1.77:1026 - puerto honeypot 80 (HTTP)
[14:20:02] CONEXION desde 192.168.1.77:1027 - puerto honeypot 443 (HTTPS)
[14:20:03] CONEXION desde 192.168.1.77:1028 - puerto honeypot 3389 (RDP)
```

¿Qué comportamiento se observa en este log y qué implicaciones tiene para la seguridad de la red?

---

**P16.** ¿En qué situación el proxy Squid **no puede** controlar el tráfico aunque el usuario lo tenga configurado correctamente como proxy en su navegador? Dar un ejemplo concreto.

---

## Sección C: Respuesta Corta (Preguntas 17-20)

*Responder en 3-5 oraciones concisas. 1 punto por pregunta.*

---

**P17.** Un técnico debe elegir entre desplegar primero un IDS o un IPS en la red de la unidad. El oficial de seguridad tiene poca experiencia con las firmas de detección y el tráfico normal de la red. ¿Cuál recomienda el técnico y por qué?

---

**P18.** Explique por qué un honeypot que registra exactamente 0 conexiones durante semanas no significa que "no funcionó" ni que fue una pérdida de tiempo. ¿Qué información útil proporciona esa situación al equipo de seguridad?

---

**P19.** ¿Cuál es la diferencia entre `acl badsite dstdomain .facebook.com` y `acl badsite dstdomain facebook.com` (sin el punto inicial) en `squid.conf`? ¿Qué implicación práctica tiene esta diferencia?

---

**P20.** Un NIDS genera 5.000 alertas en una sola noche. El analista no puede revisar manualmente todas. Describir tres estrategias concretas para priorizar cuáles alertas revisar primero.

---

---

!!! danger "Solo instructor — Clave de Respuestas"

    ## Clave de Respuestas

    ### Sección A — Respuestas correctas

    | Pregunta | Respuesta | Concepto evaluado |
    |----------|-----------|-------------------|
    | P1 | **C** | Proxy capa 7 vs firewall capa 3/4 |
    | P2 | **B** | Lectura de access.log: TCP_HIT vs TCP_MISS |
    | P3 | **C** | Bypass de proxy sin regla de firewall complementaria |
    | P4 | **B** | IDS pasivo (no afecta tráfico) vs IPS activo (puede interrumpir) |
    | P5 | **C** | Definición de falso positivo en detección |
    | P6 | **B** | Lectura de regla Snort: dirección `->` y `$HOME_NET` como destino |
    | P7 | **B** | Alert storm y la función del threshold |
    | P8 | **C** | Definición de honeypot: ningún uso legítimo = toda conexión es sospechosa |
    | P9 | **B** | Baja vs alta interacción: seguridad vs riqueza de datos |
    | P10 | **C** | Conexión interna a honeypot: equipo comprometido o insider threat |
    | P11 | **B** | Visibilidad URL completa del proxy vs visibilidad IP/puerto del firewall |
    | P12 | **B** | Opción `content` con `nocase` en regla Snort |

    ---

    ### Sección B — Respuestas modelo

    **P13 — Respuesta esperada:**
    La línea del access.log revela que el equipo con IP 192.168.1.55 realizó una petición HTTP `POST` (subida de datos) de 128.450 bytes (aproximadamente 125 KB) hacia el dominio `upload.transferservice.net`, que es un servicio de transferencia de archivos externo desconocido. El método `POST` combinado con el path `/data`, el tamaño considerable de la transferencia, el tipo de contenido `application/octet-stream` (datos binarios sin tipo específico) y el dominio desconocido que contiene "upload" son señales de posible exfiltración de datos. El analista debe: (1) identificar el equipo con IP 192.168.1.55 en el inventario, (2) investigar qué proceso realizó la petición, (3) bloquear `transferservice.net` en Squid de forma preventiva, (4) correlacionar con alertas de Snort desde esa IP.

    **P14 — Respuesta esperada:**
    La alerta indica que la regla Snort con sid 1000012 detectó la cadena `cmd.exe` en tráfico HTTP TCP desde la IP interna 192.168.1.100, puerto 54321, hacia la IP externa 93.184.216.34 (example.com) en el puerto 80. Encontrar `cmd.exe` en una petición HTTP saliente es un indicador de que el equipo 192.168.1.100 puede estar comunicándose con un servidor de comando y control (C2) de malware, enviando resultados de ejecución de comandos del sistema en el cuerpo de la petición HTTP. Esto es una técnica común de tunneling de C2 sobre HTTP para evadir firewalls. Siguiente paso: (1) aislar inmediatamente el equipo 192.168.1.100 de la red, (2) analizar procesos en ejecución y conexiones de red activas, (3) correlacionar con el access.log de Squid para ver el historial completo de peticiones, (4) escalar al oficial de seguridad como posible compromiso activo.

    **P15 — Respuesta esperada:**
    El log muestra un escaneo de puertos sistemático (port scan) ejecutado por la IP interna 192.168.1.77. En menos de 2 segundos, esa IP intentó conectarse a 5 puertos distintos del honeypot (22, 23, 80, 443, 3389) de forma secuencial — comportamiento característico de una herramienta automatizada de reconocimiento de red, imposible en acceso manual legítimo. Que la IP sea interna es especialmente relevante: indica que el equipo 192.168.1.77 puede estar comprometido y realizando reconocimiento lateral (búsqueda de servicios explotables en otros equipos de la misma red), o que es un insider realizando exploración no autorizada. Acción: aislar el equipo 192.168.1.77, analizar su memoria y conexiones activas, investigar qué herramienta realizó el scan.

    **P16 — Respuesta esperada:**
    El proxy Squid no puede controlar el tráfico de aplicaciones que no respetan la configuración de proxy del sistema operativo o del navegador. Ejemplo concreto: una aplicación de comunicaciones (cliente VoIP, aplicación de mensajería corporativa, agente de actualización de software) que establece conexiones TCP directas a internet sin consultar la configuración de proxy del sistema — no pasa por Squid aunque el navegador esté correctamente configurado. También: si el usuario usa una VPN que tunnela todo el tráfico fuera del proxy, o si una aplicación abre conexiones en puertos no estándar que el proxy no intercepta.

    ---

    ### Sección C — Criterios de evaluación

    **P17 — Mínimo que debe mencionar:**
    Recomendar comenzar con **IDS** (no IPS). Razón principal: sin experiencia previa con el tráfico normal de la red, configurar un IPS puede generar falsos positivos que bloqueen tráfico legítimo. El IDS permite observar el tráfico durante un período (semanas o meses), identificar qué alertas son reales y cuáles son ruido, y afinar las reglas sin riesgo de interrumpir operaciones. Solo cuando las reglas están validadas y la tasa de falsos positivos es baja, se pasa a modo IPS. Mencionar el concepto de "período de tune-up" es valioso.

    **P18 — Mínimo que debe mencionar:**
    El honeypot con 0 conexiones durante semanas indica que **ningún atacante intentó moverse lateralmente ni explorar la red**. Esta información es valiosa: confirma que en ese período no hubo reconocimiento interno, no hay herramientas de escaneo activas en la red, y no hay insider threats activos. El honeypot funciona como "centinela silencioso" — el hecho de que no registre nada es la evidencia de ausencia de actividad maliciosa, que es exactamente lo que queremos confirmar periódicamente. Además, si en el futuro aparece una alerta después de semanas de silencio, el contraste hace la anomalía más evidente.

    **P19 — Mínimo que debe mencionar:**
    Con **punto inicial** (`.facebook.com`): la ACL coincide con todos los subdominios de facebook.com — `www.facebook.com`, `m.facebook.com`, `static.facebook.com`, `api.facebook.com`. Esta es la forma correcta para bloquear un dominio completo.
    Sin **punto inicial** (`facebook.com`): la ACL solo coincide con el dominio exacto `facebook.com`. No bloquea `www.facebook.com` ni ningún subdominio — en la práctica, el navegador accede a través de `www.facebook.com`, por lo que la regla sin el punto no bloquea el acceso real.
    En resumen: siempre usar el punto inicial para bloquear dominios completos en Squid.

    **P20 — Tres estrategias válidas (cualquier combinación de las siguientes):**
    (1) **Priorizar por severidad/prioridad de la firma**: las reglas Snort tienen campo `priority`; revisar primero las alertas de prioridad 1 (alta) antes que las de prioridad 3 (baja).
    (2) **Agrupar alertas por IP de origen**: si 4.000 de las 5.000 alertas provienen de la misma IP, son un único evento repetido — tratarlo como un caso y revisarlo una vez.
    (3) **Filtrar por tipo de alert storm conocido**: si se sabe que la regla X genera ruido (ping de monitorización legítima), suprimir esas alertas del análisis del turno.
    (4) **Correlacionar con otras fuentes**: alertas que también aparecen en el log del proxy o del firewall tienen mayor probabilidad de ser reales — priorizar esas.
    (5) **Buscar primero alertas de IPs internas con comportamiento anómalo**: una alerta desde una IP interna es más grave que una desde internet — los ataques internos tienen más acceso a sistemas críticos.

    ---

    ## Notas de aplicación para el instructor

    **Timing sugerido:** 60 minutos totales. Los alumnos habitualmente necesitan 30-35 min para la Sección A, 15 min para la B y 15 min para la C. Si el tiempo escasea, la Sección C puede reducirse a 2 preguntas (P17 y P19 tienen mayor discriminación).

    **Cómo presentar los logs de P13, P14 y P15:** Proyectar cada extracto de log en la pantalla y dar 3 minutos antes de la siguiente pregunta para que los alumnos copien la información relevante en su hoja de respuestas. No es necesario que memoricen el log — sí que lo analicen.

    **Preguntas que suelen generar más discusión:**
    - **P3** (bypass del proxy): muchos alumnos responden D (HTTPS). Aprovechar para reforzar que Squid sí puede bloquear conexiones HTTPS por dominio (aunque no vea el contenido) — el problema de bypass es diferente.
    - **P19** (punto en squid.conf): habitualmente la respuesta más recordada del examen. Explicar en pizarra con un ejemplo: escribir `.facebook.com` y `www.facebook.com` y mostrar cuál ACL coincide con cuál. Esta distinción es la más práctica de toda la unidad.
    - **P8** (honeypot inherentemente sospechoso): algunos alumnos eligen A o D. Repasar la definición: "ningún uso legítimo" es la clave conceptual — no es sobre el protocolo ni sobre el filtrado de IPs.
