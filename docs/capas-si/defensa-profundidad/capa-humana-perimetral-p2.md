---
# Horas asignadas: 4 hrs
# Tipo: Teoría
---

# Defensa en Profundidad — Capa Perimetral (CONT-22)

> Esta clase cubre **Capa 2: Perimetral** del modelo de defensa en profundidad establecido en [Parte 1](capa-humana-perimetral-p1.md).

> **Duración:** 4 horas | **Asignatura:** Capas SI | **Unidad:** Defensa en Profundidad

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar la función del firewall perimetral como primera barrera tecnológica de la red.
- Describir el concepto y diseño de una DMZ y los servicios que típicamente aloja.
- Distinguir los diferentes tipos de segmentación de red y su aplicación defensiva.
- Explicar el rol de las VLANs para aislar zonas de confianza dentro de la red.
- Aplicar los conceptos perimetrales a escenarios de redes militares tácticas.

---

> **Recapitulación:** En la Parte 1 estudiamos el modelo de 7 capas de defensa en profundidad y la Capa 1 Humana — ingeniería social, políticas de uso aceptable y formación. Esta clase cubre la Capa 2: Perimetral, el primer nivel de defensa tecnológica.

---

## El Firewall Perimetral: Primera Barrera Tecnológica

El firewall perimetral es el componente más conocido de la seguridad de red, pero también el más malinterpretado. No es una solución mágica — es un inspector de tráfico que aplica reglas. Solo es efectivo si las reglas están bien diseñadas y si se considera como una capa dentro de un modelo más amplio, no como la única defensa.

!!! note "Definición"
    Un **firewall perimetral** es un dispositivo o software que controla el flujo de tráfico de red entre dos o más zonas de confianza diferente, permitiendo o bloqueando paquetes según reglas predefinidas basadas en criterios como dirección IP origen/destino, puerto, protocolo y estado de la conexión.

### Tipos de Firewall por Generación

| Tipo | Qué inspecciona | Limitación principal |
|------|-----------------|---------------------|
| **Packet filter** (1ª gen) | Cabecera IP/TCP — IP origen, destino, puerto, protocolo | No entiende el contexto de la conexión; fácil de evadir con fragmentación |
| **Stateful inspection** (2ª gen) | Estado de la conexión — rastrea si el paquete pertenece a una sesión establecida | No inspecciona el contenido de la aplicación |
| **Application-layer / NGFW** (3ª gen) | Contenido de la aplicación — identifica el protocolo real (ej: HTTP en puerto no estándar), usuarios, aplicaciones | Mayor coste computacional; requiere mantenimiento de reglas más complejas |

Para redes militares de comunicaciones, el **stateful inspection** es el mínimo recomendado. El NGFW (Next-Generation Firewall) es el estándar actual en redes que manejan información clasificada, porque puede distinguir tráfico C2 legítimo de malware que "imita" tráfico web.

### Principio de Mínimo Privilegio en Reglas de Firewall

El principio que gobierna el diseño de reglas de firewall es: **denegar todo por defecto, permitir solo lo necesario**. Una regla que dice "permitir todo el tráfico saliente" es tan útil como no tener firewall — el malware simplemente establece sus conexiones de comando y control hacia fuera, sin ser bloqueado.

!!! warning "Regla 'Allow All Outbound' — la trampa más común"
    Muchos administradores permiten todo el tráfico saliente por comodidad. Esta configuración permite que cualquier malware en la red interna establezca conexiones hacia servidores externos sin restricción. El ransomware moderno exfiltra datos hacia el exterior antes de cifrarlos — y lo hace por el puerto 443 (HTTPS) precisamente porque la mayoría de firewalls lo dejan pasar.

!!! example "Aplicación en entorno castrense"
    La red del puesto de mando avanzado permite salida solo en los puertos requeridos para las misiones activas: 443 (HTTPS cifrado para reportes), 53 (DNS solo hacia el servidor DNS interno), y los puertos específicos del sistema C2 operacional. Todo lo demás está bloqueado por defecto. Cuando un ordenador infectado intenta contactar un servidor de comando externo en el puerto 8080, el firewall perimetral bloquea el intento y genera una alerta. Sin esta regla de salida restrictiva, el incident no habría sido detectado durante días.

---

## La DMZ: Zona Desmilitarizada de Red

El término "zona desmilitarizada" viene directamente del lenguaje militar — la zona neutral entre dos posiciones enfrentadas donde ninguna parte tiene control exclusivo. En redes, la DMZ cumple exactamente ese rol: es una zona de red que está expuesta al exterior pero separada de la red interna.

!!! note "Definición"
    Una **DMZ** (Demilitarized Zone) es un segmento de red que actúa como zona tampón entre Internet (o red externa no confiable) y la red interna corporativa. Los servidores en la DMZ son accesibles desde el exterior, pero están aislados de los sistemas internos más críticos.

### Por qué existe la DMZ

El problema que resuelve la DMZ es simple: algunos servicios necesitan ser accesibles desde el exterior (correo electrónico, servidor web, portal de acceso remoto) pero si esos servidores están directamente en la red interna, cualquier atacante que los comprometa tiene acceso directo a todo lo demás.

La DMZ introduce una separación: los servidores "públicos" van a la DMZ; la red interna mantiene todo lo demás. Si un atacante compromete un servidor en la DMZ, solo está en la DMZ — tiene que superar una segunda barrera para llegar a la red interna.

### Diseño Clásico de DMZ con Doble Firewall

```
Internet
    │
    ▼
[Firewall Externo]  ← reglas: permite solo tráfico a DMZ
    │
    ▼
┌───────────────────┐
│       DMZ          │  ← Servidores publicados: web, correo, VPN gateway
│  [Servidor Web]   │
│  [Proxy inverso]  │
│  [Mail relay]     │
└───────────────────┘
    │
    ▼
[Firewall Interno]  ← reglas: más estrictas; solo tráfico necesario a red interna
    │
    ▼
Red Interna         ← Sistemas críticos: bases de datos, directorio, C2
```

En este diseño, un atacante que compromete el servidor web en la DMZ todavía enfrenta el firewall interno antes de llegar a los sistemas críticos. El firewall interno tiene reglas aún más estrictas — típicamente solo permite comunicaciones específicas y necesarias entre la DMZ y la red interna.

| Zona | Nivel de confianza | Ejemplos de servicios |
|------|-------------------|----------------------|
| **Internet** | No confiable (0) | Usuarios externos, atacantes potenciales |
| **DMZ** | Baja confianza (1) | Servidor web, proxy inverso, relé de correo, portal VPN |
| **Red interna** | Media confianza (2) | Estaciones de trabajo, servidores de aplicaciones internos |
| **Zona de activos críticos** | Alta confianza (3) | Bases de datos, directorio activo, sistemas C2, SCIF |

!!! example "Aplicación en entorno castrense"
    La Jefatura de Comunicaciones del Batallón N°3 necesita ofrecer acceso VPN a oficiales que trabajan desde ubicaciones remotas. El servidor VPN (concentrador VPN) se coloca en la DMZ — accesible desde Internet para recibir las conexiones entrantes, pero aislado de la red interna. Cuando un oficial remoto se conecta, el firewall interno valida que su tráfico solo llega a los servidores de aplicaciones autorizados para su rol. Si el concentrador VPN fuera comprometido, el atacante estaría atrapado en la DMZ, sin acceso directo a los sistemas C2 del batallón.

### Proxy Inverso en la DMZ

El proxy inverso es un componente que actúa en nombre de los servidores internos. Cuando un usuario externo accede a una aplicación, el proxy inverso recibe la solicitud, la valida, y la reenvía al servidor interno. El servidor interno nunca está expuesto directamente a Internet.

Los beneficios del proxy inverso van más allá de la seguridad: también permite terminar el cifrado TLS en la DMZ (aliviar carga del servidor interno), balanceo de carga y caché de contenido estático.

---

## Segmentación de Red

La segmentación de red es la práctica de dividir una red grande en subredes más pequeñas con controles de acceso entre ellas. El objetivo es contener el movimiento lateral de un atacante — si compromete un segmento, no puede moverse libremente por toda la red.

!!! note "Definición"
    La **segmentación de red** es la división de una red en segmentos lógicos o físicos separados, con controles de acceso entre ellos, de forma que el tráfico entre segmentos requiere autorización explícita.

### Comparativa: Sin Segmentación vs. Con Segmentación

| Escenario | Sin segmentación | Con segmentación |
|-----------|-----------------|-----------------|
| **Un equipo comprometido** | El atacante puede escanear y atacar todos los equipos de la red — todo está directamente alcanzable | El atacante está confinado al segmento del equipo comprometido; necesita superar controles para moverse a otros segmentos |
| **Ransomware** | Se propaga automáticamente a todos los equipos y servidores accesibles | Solo cifra los equipos en el mismo segmento que el equipo inicial; la propagación se detiene en los límites de segmento |
| **Brecha de datos** | El atacante accede a todas las bases de datos desde cualquier equipo de la red | Solo los equipos del segmento autorizado pueden acceder a bases de datos sensibles |
| **Visibilidad** | El tráfico interno es invisible — no hay punto donde monitorizarlo | El tráfico entre segmentos pasa por puntos de control monitorizados |

### Modelos de Segmentación

**Por función:**
Agrupa los sistemas por su función — todos los servidores en un segmento, todos los equipos de trabajo en otro, los sistemas de IoT/industrial en un tercero. El tráfico entre funciones pasa por un firewall interno.

**Por nivel de sensibilidad:**
Agrupa los sistemas por la sensibilidad de la información que manejan. Los sistemas que procesan información clasificada van en un segmento con controles más estrictos que los que solo manejan información pública.

**Por zona geográfica:**
En redes distribuidas (varios edificios o instalaciones), cada ubicación tiene su propio segmento con enlace controlado hacia las demás ubicaciones.

---

## VLANs: Segmentación Lógica en la Red Local

Las VLANs (Virtual Local Area Networks) permiten implementar segmentación de red sin necesidad de hardware físico separado para cada segmento. Un solo switch gestionable puede alojar múltiples VLANs completamente separadas entre sí.

!!! note "Definición"
    Una **VLAN** es una red local virtual que agrupa puertos de uno o más switches en un dominio de broadcast lógicamente separado de otros grupos, independientemente de la ubicación física de los dispositivos.

### ¿Por qué VLANs y no subredes físicas separadas?

En una instalación militar con múltiples tipos de sistemas (sistemas C2, radios tácticas IP, equipos administrativos, sistemas de videoconferencia clasificada), usar cables físicos separados para cada tipo sería costoso, inflexible y difícil de gestionar. Las VLANs permiten separar el tráfico lógicamente usando la misma infraestructura física de switches y cables.

### Asignación de VLANs por Zona

| VLAN | Nombre | Contenido | Acceso desde otras VLANs |
|------|--------|-----------|--------------------------|
| VLAN 10 | Gestión | Interfaces de gestión de switches, routers, firewall | Solo VLAN Administradores |
| VLAN 20 | Servidores | Servidores de archivos, directorio activo, correo interno | Autorizado desde VLAN Usuarios; bloqueado desde VLAN IoT |
| VLAN 30 | Usuarios | Equipos de trabajo del personal | Acceso limitado a VLAN Servidores |
| VLAN 40 | Sistemas tácticos | Radios tácticas IP, terminales C2 | Aislada — sin acceso a otras VLANs salvo router C2 |
| VLAN 50 | IoT/Periféricos | Impresoras, cámaras IP, control de acceso | Acceso mínimo; sin acceso a VLAN Servidores ni Usuarios |

!!! warning "VLAN hopping — el ataque de evasión de segmentación"
    Un atacante avanzado puede intentar explotar errores de configuración para saltar de una VLAN a otra, técnica conocida como VLAN hopping. La mitigación principal es deshabilitar los puertos trunk innecesarios, cambiar la VLAN nativa del trunk a una VLAN sin dispositivos, y aplicar VLAN access maps en switches gestionados.

!!! example "Aplicación en entorno castrense"
    El batallón tiene un único rack de switches gestionables para toda la instalación. Los sistemas de radio táctica IP van en VLAN 40 (Sistemas Tácticos) completamente aislada — no tienen comunicación posible con la VLAN de usuarios ni con la VLAN de servidores administrativos. Los equipos de trabajo del personal administrativo van en VLAN 30. Si uno de esos equipos administrativos es comprometido por un atacante, el atacante no puede alcanzar los sistemas de radio tácticos ni los terminales C2 en VLAN 40 — la segmentación por VLAN lo confina. El movimiento lateral está estructuralmente bloqueado.

---

## Integración: El Perímetro Como Sistema

La Capa 2 no es un solo dispositivo — es un sistema de componentes que trabajan juntos:

```
Internet
    │
[Firewall perimetral] ← primera barrera; bloquea tráfico no autorizado
    │
[DMZ] ← servicios publicados; aislados de la red interna
    │
[Proxy inverso] ← valida y reenvía tráfico hacia servidores internos
    │
[Firewall interno] ← segunda barrera; controla tráfico DMZ ↔ red interna
    │
[Red interna segmentada en VLANs]
    ├── VLAN Usuarios
    ├── VLAN Servidores
    ├── VLAN Sistemas Tácticos (aislada)
    └── VLAN Gestión (solo administradores)
```

Cada componente agrega una capa de control. Si el firewall perimetral falla o es evadido, la DMZ limita el alcance. Si la DMZ es comprometida, el firewall interno bloquea el acceso a la red interna. Si se llega a la red interna, la segmentación VLAN limita el movimiento lateral.

---

## Resumen

1. El firewall perimetral es la primera barrera tecnológica: controla el tráfico por reglas y debe seguir el principio de denegar por defecto.
2. La DMZ aloja servicios expuestos al exterior aislándolos de la red interna con una segunda barrera (firewall interno).
3. La segmentación de red confina a los atacantes a un segmento y detiene la propagación lateral del malware.
4. Las VLANs implementan segmentación lógica sobre la misma infraestructura física, permitiendo aislar zonas de confianza diferente.
5. El perímetro efectivo es un sistema integrado de firewall + DMZ + proxy inverso + segmentación interna, no un dispositivo único.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **NIST SP 800-41 Rev. 1 — Guidelines on Firewalls and Firewall Policy:** https://csrc.nist.gov/publications/detail/sp/800-41/rev-1/final
- **CIS Critical Security Controls — Control 12: Network Infrastructure Management:** https://www.cisecurity.org/controls/network-infrastructure-management

---

*Siguiente: [Capa de Red y Endpoint (Parte 1) — TLS, VPN y NAC](capa-red-endpoint-p1.md)*
