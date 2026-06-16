---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Cortafuegos: Protección Perimetral Local (Parte 1)

> **Duración:** 2 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En el Ordenador

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar la diferencia entre un host firewall (cortafuegos de host) y un firewall perimetral de red.
- Describir el filtrado stateful vs. stateless y por qué Windows Defender Firewall es stateful.
- Navegar Windows Defender Firewall with Advanced Security (WFAS) e interpretar las reglas existentes.
- Crear reglas de entrada y salida básicas por puerto, protocolo y aplicación.
- Explicar los tres perfiles de red (Domain, Private, Public) y cuándo aplica cada uno.

---

## ¿Qué es un Cortafuegos?

Un cortafuegos (firewall) es un sistema que inspecciona el tráfico de red y decide si lo permite o lo bloquea, basándose en un conjunto de reglas predefinidas. La analogía más precisa es la del portero de un edificio: decide quién puede entrar, pero también controla a dónde pueden ir los que ya están adentro, y quién puede salir.

Hay dos tipos de cortafuegos que necesitamos entender como distintos — no son excluyentes, sino complementarios:

**Host firewall (cortafuegos de host):** Corre en el propio ordenador que protege. Windows Defender Firewall es un host firewall. Protege ese equipo específico del tráfico de red que llega a él o sale de él. Es el que estudiamos en esta clase.

**Firewall perimetral de red:** Corre en un dispositivo dedicado — un router, un appliance de seguridad, o una UTM (Unified Threat Management appliance) — situado entre la red interna de la unidad y el exterior (internet u otras redes). Protege toda la red, no un equipo individual.

**¿Por qué necesitamos ambos?** El firewall perimetral es la primera línea de defensa: protege contra amenazas externas que intentan entrar a la red. Pero si un atacante ya está dentro de la red — porque comprometió un equipo, porque se conectó a la WiFi sin autorización, o porque un dispositivo infectado entró físicamente — el firewall perimetral ya no lo ve. Es entonces cuando el host firewall de cada equipo importa: previene el movimiento lateral, es decir, que el atacante se mueva de un equipo comprometido a otro dentro de la misma red.

En operaciones militares esto es especialmente relevante: las redes de unidad pueden tener múltiples dispositivos conectados, no todos controlados directamente, y la amenaza interna (un equipo comprometido o un dispositivo no autorizado) es tan real como la amenaza externa.

---

## Filtrado Stateful vs. Stateless

La diferencia entre filtrado stateful y stateless es fundamental para entender cómo funciona cualquier firewall moderno.

**Filtrado stateless (sin estado):** El firewall examina cada paquete de red de forma completamente independiente. Solo considera la dirección IP de origen, la dirección IP de destino, el puerto y el protocolo. No recuerda nada de paquetes anteriores. Cada paquete se evalúa solo, sin contexto de conversación.

**Filtrado stateful (con estado):** El firewall mantiene una tabla de conexiones activas — recuerda qué conexiones se han iniciado, desde dónde, hacia dónde, y en qué estado están. Si el ordenador inició una conexión TCP hacia un servidor web (el ordenador envió la petición), el firewall stateful permite automáticamente que los paquetes de respuesta del servidor entren — porque recuerda que esa conversación fue iniciada por el equipo local.

| Escenario | Stateless | Stateful |
|-----------|-----------|---------|
| El ordenador solicita una página web (puerto 80 saliente) | Debe haber una regla explícita de ENTRADA que permita las respuestas del servidor | Permite automáticamente las respuestas porque recuerda que la petición fue iniciada localmente |
| Un atacante externo envía paquetes al puerto 80 del ordenador | Se evalúa igual que cualquier otro paquete — si hay regla de entrada que permita el puerto 80, entra | Se evalúa como nueva conexión iniciada externamente — si no hay regla de entrada explícita, se bloquea |
| Escaneo de puertos externo | Puede pasar algunos paquetes si los puertos tienen reglas de permiso | Detecta el patrón de múltiples conexiones a diferentes puertos y puede bloquearlo |

**Windows Defender Firewall es stateful.** Esto significa que cuando configuras una regla de salida que permite tráfico, no necesitas crear también una regla de entrada que permita las respuestas — el estado de la conexión las maneja automáticamente. Esta es una ventaja práctica importante cuando se configuran reglas.

---

## Los Tres Perfiles de Red

Windows Defender Firewall no aplica el mismo conjunto de reglas en todos los entornos. Tiene tres perfiles, y el perfil activo determina qué conjunto de reglas se aplica. El sistema selecciona el perfil automáticamente basándose en el tipo de red detectado.

| Perfil | Cuándo aplica | Comportamiento por defecto | Relevancia militar |
|--------|--------------|--------------------------|-------------------|
| **Domain** | El equipo está conectado a una red con un controlador de dominio de Active Directory que Windows reconoce | Reglas gestionadas por la política de dominio; tráfico de administración permitido | Aplicable en redes de unidad con servidor de directorio activo |
| **Private** | El usuario marcó manualmente la red como "confiable" (red de hogar, red de oficina conocida) | Descubrimiento de red habilitado; se permite algo de tráfico entrante para compartición de recursos | Red de unidad marcada como privada por el Técnico responsable |
| **Public** | Red desconocida (WiFi de hotel, red de café, cualquier red no marcada como privada o dominio) | Descubrimiento de red deshabilitado; tráfico entrante bloqueado al máximo | Cualquier red no verificada en campo — debe ser siempre el perfil predeterminado en redes externas |

El perfil Public es la postura más restrictiva y debe aplicarse siempre en entornos no controlados.

!!! example "Aplicación en entorno castrense"
    Un Técnico conecta su laptop a la red WiFi del hotel durante una misión en otra ciudad. Windows detecta que es una red nueva, no marcada como privada o dominio. El perfil Public se activa automáticamente. Esto significa que: el descubrimiento de red está deshabilitado (otros usuarios de la WiFi no pueden ver el equipo), el tráfico entrante no solicitado está bloqueado, y se aplican las reglas más restrictivas disponibles.

    Error común: Windows puede preguntar "¿Esta es una red pública o privada?" cuando se conecta por primera vez. Si el usuario selecciona "Privada" en una red de hotel o en la WiFi de un cliente, el perfil se relaja innecesariamente. La regla en entornos militares: si hay duda, siempre marcar como Pública.

---

## Reglas de Entrada vs. Reglas de Salida

**Reglas de entrada (Inbound):** Controlan qué tráfico puede llegar al ordenador desde la red. Un paquete que llega desde fuera debe tener una regla de entrada que lo permita — de lo contrario, se bloquea silenciosamente.

**Reglas de salida (Outbound):** Controlan qué tráfico puede el ordenador enviar hacia la red. Por defecto, Windows Defender Firewall permite todo el tráfico saliente que no tenga una regla de bloqueo explícita.

Este comportamiento predeterminado — bloquear todo entrante, permitir todo saliente — es el apropiado para la mayoría de estaciones de trabajo, pero tiene una implicación importante: el tráfico saliente de malware no está bloqueado por defecto.

!!! warning "El outbound permisivo es explotado por el malware moderno"
    La mayoría de los administradores solo configura reglas de entrada — verifican que nadie pueda conectarse al equipo desde afuera. Pero el malware moderno abusa del outbound permisivo: una vez que un troyano se instala en el equipo, puede conectarse a sus servidores de Comando y Control (C2) en internet usando el puerto 443 (HTTPS) — un puerto que nadie bloquea saliente porque es el puerto del navegador web.

    Configurar reglas de salida que limiten qué aplicaciones pueden conectarse a internet es una medida de defensa en profundidad relevante para entornos de alta seguridad. En operaciones militares, los equipos de puesto de mando podrían tener reglas de salida que solo permiten a aplicaciones específicas (el cliente de correo, el browser autorizado) conectarse, y bloquean todo lo demás.

**Comportamiento predeterminado resumido:**

- **Inbound sin regla de permiso:** Bloqueado silenciosamente.
- **Outbound sin regla de bloqueo:** Permitido.
- **Regla de bloqueo vs. regla de permiso:** Las reglas de bloqueo tienen precedencia sobre las de permiso. Si existe una regla de bloqueo y una de permiso para el mismo tráfico, el bloqueo gana.

---

## Navegando Windows Defender Firewall with Advanced Security

Para abrir la consola avanzada: `Win+R → wf.msc` (o buscar "Windows Defender Firewall with Advanced Security" en el menú Inicio).

La consola WFAS tiene cuatro secciones principales en el panel izquierdo:

- **Inbound Rules:** Lista de todas las reglas de entrada configuradas.
- **Outbound Rules:** Lista de todas las reglas de salida configuradas.
- **Connection Security Rules:** Reglas IPsec — fuera del alcance de esta clase.
- **Monitoring:** Vista del estado actual, conexiones activas y reglas activas por perfil.

**Leyendo una regla existente:** Cada regla en la lista de Inbound o Outbound Rules tiene columnas:

| Columna | Qué muestra |
|---------|------------|
| Name | Nombre descriptivo de la regla |
| Group | Agrupación lógica (por ejemplo, "Remote Desktop") |
| Profile | A qué perfil(es) aplica (Domain, Private, Public o combinaciones) |
| Enabled | Yes/No — si la regla está activa |
| Action | Allow o Block |
| Override | Si la regla puede ser anulada por otra regla |
| Program | Ejecutable específico al que aplica (si aplica) |
| Local Address | IP o rango local |
| Remote Address | IP o rango remoto |
| Protocol | TCP, UDP, Any |
| Local Port | Puerto local |
| Remote Port | Puerto remoto |

!!! tip "Filtrar reglas para ver solo las activas"
    La lista de Inbound Rules puede tener cientos de entradas. Para ver solo las que están activas: hacer clic en el encabezado de la columna "Enabled" para ordenar por estado, y las reglas habilitadas (Yes) se agrupan al principio. También se puede filtrar por perfil desde el menú Action → Filter by Profile.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    En una red de unidad, los ordenadores de los oficiales deben poder recibir conexiones de escritorio remoto (RDP, puerto TCP 3389) para que el Técnico de comunicaciones pueda administrarlos de forma remota desde el servidor de administración (IP: 192.168.10.5). Sin embargo, no se debe permitir RDP desde ningún otro equipo de la red — solo desde esa IP específica.

    El Técnico crea una regla en WFAS:

    - **Tipo:** Inbound Rule → New Rule → Port
    - **Protocol and Ports:** TCP, Specific local ports: 3389
    - **Action:** Allow the connection
    - **Profile:** Domain, Private (no Public)
    - **Remote IP (en Advanced → Remote IP address):** This IP address or subnet: 192.168.10.5
    - **Name:** "RDP desde servidor de administración únicamente"

    Y adicionalmente, crea una regla que bloquea RDP desde cualquier otra IP:

    - Misma configuración pero Action: Block the connection, Remote IP: Any IP address, con prioridad más alta.

    El resultado: solo la IP 192.168.10.5 puede iniciar una sesión RDP. Cualquier otro equipo de la red, incluido un atacante que haya comprometido otro ordenador, recibe un bloqueo silencioso al intentar conectarse al puerto 3389.

---

## Resumen

1. Un **host firewall** como Windows Defender Firewall protege el equipo individual; el **firewall perimetral de red** protege toda la red — se necesitan ambos porque el host firewall defiende contra movimiento lateral dentro de la red.
2. El filtrado **stateful** recuerda el estado de las conexiones y permite respuestas automáticamente; el **stateless** evalúa cada paquete de forma independiente — Windows Defender Firewall es stateful.
3. Los **tres perfiles** (Domain, Private, Public) definen qué tan restrictivo es el firewall según el entorno — en redes no verificadas siempre debe usarse el perfil Public.
4. Por defecto, todo el **tráfico entrante** sin regla de permiso se bloquea; todo el **tráfico saliente** sin regla de bloqueo se permite — las reglas de bloqueo siempre tienen precedencia sobre las de permiso.
5. WFAS se abre con `wf.msc` — las reglas se leen por columnas: Enabled, Action, Protocol, Local Port, Remote Port, Program.

---

*Siguiente: [Cortafuegos (Parte 2)](cortafuegos-p2.md)*
