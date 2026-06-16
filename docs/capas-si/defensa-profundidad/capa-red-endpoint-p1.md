---
# Horas asignadas: 4 hrs
# Tipo: Teoría
---

# Defensa en Profundidad — Capa de Red Interna (CONT-23)

> Esta clase cubre **Capa 3: Red Interna** del modelo de defensa en profundidad establecido en [Parte 1](capa-humana-perimetral-p1.md).

> **Duración:** 4 horas | **Asignatura:** Capas SI | **Unidad:** Defensa en Profundidad

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar por qué el cifrado en tránsito (TLS) es necesario incluso dentro de una red interna.
- Describir los tipos de VPN y elegir el más adecuado para distintos escenarios de conectividad militar.
- Explicar el concepto de NAC y cómo controla el acceso basado en la postura del dispositivo.
- Aplicar los conceptos de red interna a escenarios de redes tácticas y de puesto de mando.

---

> **Recapitulación:** En la Parte 1 construimos el modelo de 7 capas y estudiamos la Capa 1 Humana. En CONT-22 cubrimos la Capa 2 Perimetral: firewall, DMZ y segmentación VLAN. Esta clase profundiza en la Capa 3 — la red interna una vez que el tráfico ha cruzado el perímetro.

---

## ¿Por qué la red interna necesita sus propios controles?

Un error conceptual frecuente es asumir que la red interna es "de confianza". La lógica es: si el firewall perimetral solo deja entrar tráfico autorizado, todo lo que está dentro es seguro. Este modelo es exactamente el que atacantes modernos explotan.

Piénsalo en términos militares: asegurar el perímetro de una base no garantiza que todos los que están dentro son amigos. Un infiltrado que cruza el perímetro con credenciales robadas, un empleado que actúa maliciosamente, o malware instalado antes de que el equipo entrara a la red interna — todos están "dentro" y ninguno debería tener acceso irrestricto a todo lo demás.

La Capa 3 aplica controles dentro de la red interna: cifra el tráfico para que no sea legible aunque alguien lo intercepte, verifica la identidad de los equipos que se conectan a la red, y crea canales seguros para comunicaciones entre ubicaciones separadas.

---

## Cifrado en Tránsito: TLS

TLS (Transport Layer Security) es el protocolo que cifra la comunicación entre dos puntos en una red. Cuando accedes a un sitio web con `https://`, estás usando TLS. Pero TLS no es solo para Internet — cualquier comunicación interna entre sistemas puede y debe cifrarse.

!!! note "Definición"
    **TLS** (Transport Layer Security) es un protocolo criptográfico que proporciona autenticación, integridad y confidencialidad en comunicaciones de red. Opera entre la capa de transporte (TCP) y la capa de aplicación, siendo transparente para las aplicaciones que lo usan.

### ¿Por qué cifrar el tráfico interno?

El argumento "ya estamos dentro de la red, no hace falta cifrar" falla por varias razones:

1. **Amenazas internas:** Un empleado descontento o un dispositivo comprometido puede escuchar el tráfico de la red local con herramientas de captura de paquetes (sniffer). Sin TLS, puede leer credenciales, correos y documentos en texto claro.

2. **Movimiento lateral del atacante:** Un atacante que ya penetró la red interna usa técnicas de sniffing y ARP poisoning para interceptar el tráfico entre otros equipos. TLS neutraliza este vector.

3. **Integridad de datos:** TLS no solo cifra — también garantiza que los datos no fueron modificados en tránsito. Un atacante de "man-in-the-middle" no puede alterar el contenido sin que la conexión falle.

### Cómo funciona TLS (Concepto, no código)

TLS establece una conexión segura mediante un proceso de negociación llamado **TLS handshake**:

```
Cliente                              Servidor
  │                                     │
  │──── ClientHello ───────────────────▶│  1. "Quiero comunicarme. Aquí mis cipher suites soportadas."
  │                                     │
  │◀─── ServerHello + Certificado ──────│  2. "Acepto. Aquí está mi certificado (identidad + clave pública)."
  │                                     │
  │  [Cliente verifica el certificado:  │
  │   ¿firma de CA de confianza?        │
  │   ¿nombre coincide con destino?]    │
  │                                     │
  │──── Clave de sesión (cifrada) ─────▶│  3. Cliente genera clave de sesión, la cifra con la clave pública del servidor.
  │                                     │
  │◀───────────── ACK ──────────────────│  4. Servidor descifra la clave de sesión. Canal seguro establecido.
  │                                     │
  │═══════════ Datos cifrados ══════════│  5. Todo el tráfico posterior usa la clave de sesión simétrica.
```

| Concepto | Qué es | Por qué importa |
|----------|--------|-----------------|
| **Certificado digital** | Documento electrónico firmado por una CA que vincula una clave pública a una identidad | Permite al cliente verificar que está hablando con el servidor correcto, no con un impostor |
| **Autoridad Certificadora (CA)** | Entidad de confianza que firma certificados | Si la CA es confiable, sus certificados son confiables; una CA comprometida pone en riesgo toda la cadena |
| **Cipher suite** | Combinación de algoritmos para intercambio de clave, cifrado y autenticación | TLS 1.3 elimina las suites débiles del pasado; usar TLS 1.2+ con suites modernas |
| **Perfect Forward Secrecy (PFS)** | Propiedad por la que cada sesión usa claves de sesión únicas derivadas efímeramente | Aunque roben la clave privada del servidor mañana, no pueden descifrar el tráfico de hoy |

!!! warning "TLS 1.0 y 1.1 — versiones obsoletas"
    TLS 1.0 y 1.1 tienen vulnerabilidades conocidas (POODLE, BEAST). Las versiones actuales del estándar son TLS 1.2 y TLS 1.3. Cualquier sistema que todavía negocie TLS 1.0 o 1.1 debe ser actualizado. En entornos militares con equipos más antiguos, esto es especialmente relevante.

!!! example "Aplicación en entorno castrense"
    Los terminales del sistema C2 se comunican con el servidor central mediante un protocolo propietario que por defecto usa TCP sin cifrar. Un técnico de comunicaciones captura el tráfico con Wireshark y puede leer en texto claro los identificadores de misión y coordenadas. La solución es implementar TLS como wrapper sobre el protocolo propietario (o migrar a HTTPS/REST cifrado). Con TLS, aunque el técnico capture el tráfico, solo ve datos cifrados ilegibles. La confidencialidad de las comunicaciones C2 está protegida incluso dentro de la red interna del puesto de mando.

---

## VPN: Redes Privadas Virtuales

Una VPN (Virtual Private Network) crea un "túnel" cifrado sobre una red no confiable (generalmente Internet), permitiendo que dos extremos se comuniquen como si estuvieran en la misma red local.

!!! note "Definición"
    Una **VPN** es una tecnología que establece un canal de comunicación cifrado y autenticado sobre una infraestructura de red compartida o no confiable, creando una red privada virtual entre los extremos del túnel.

### Tipos de VPN

| Tipo | Escenario de uso | Descripción |
|------|-----------------|-------------|
| **Site-to-site** | Conectar dos instalaciones permanentes | Dos gateways VPN (uno en cada sede) establecen un túnel permanente; los usuarios de ambas sedes se ven en la misma red sin configurar nada en sus equipos |
| **Acceso remoto (client-to-site)** | Oficial o técnico trabajando desde ubicación remota | El usuario instala un cliente VPN en su equipo; se autentica y se conecta al gateway VPN de la organización; su tráfico viaja cifrado hasta la red central |
| **SSL/TLS VPN** | Acceso remoto sin cliente instalado | La VPN funciona a través del navegador web sobre HTTPS; no requiere instalar software adicional; adecuada para acceso ocasional |

### VPN Site-to-Site vs. Acceso Remoto: Comparativa

| Dimensión | Site-to-Site | Acceso Remoto |
|-----------|-------------|---------------|
| **Usuarios** | Toda la red del site B; transparente para los usuarios | Usuarios individuales; cada uno configura y usa el cliente |
| **Configuración** | En los gateways VPN; los usuarios no hacen nada | En cada dispositivo del usuario |
| **Persistencia** | Túnel siempre activo | El usuario activa/desactiva cuando lo necesita |
| **Protocolo típico** | IPSec (IKEv2) | IPSec (IKEv2), OpenVPN, WireGuard |
| **Caso militar típico** | Enlace permanente entre cuartel general y base avanzada | Oficial de enlace que trabaja desde ubicación temporal |

### Protocolos VPN Comunes

**IPSec (Internet Protocol Security):**
El protocolo estándar para VPN empresarial y gubernamental. Opera a nivel de capa de red (IP), cifrando cada paquete IP individualmente. Usa IKEv2 para la negociación de claves. Es el estándar en la mayoría de equipos de grado militar y gubernamental.

**OpenVPN:**
Protocolo de código abierto que usa TLS para el canal de control y cifrado de datos. Muy flexible y auditado públicamente. Habitual en soluciones comerciales y open-source.

**WireGuard:**
Protocolo moderno (2020) con código mucho más simple que IPSec u OpenVPN. Alto rendimiento, bajo consumo de recursos. Adoptado en varios sistemas operativos modernos (Linux kernel desde 5.6).

!!! example "Aplicación en entorno castrense"
    El puesto de mando avanzado está en el campo, conectado a la red central del cuartel general solo mediante un enlace de radio táctica con ancho de banda limitado. Se establece una VPN site-to-site con IPSec sobre ese enlace de radio — todo el tráfico entre el puesto avanzado y el cuartel general viaja cifrado. Si la señal de radio es interceptada por el adversario, solo captura tráfico IPSec cifrado, inútil sin las claves. Los operadores en el puesto avanzado acceden a los sistemas C2 del cuartel como si estuvieran en la misma red local, sin necesidad de configurar nada adicional en sus terminales.

---

## NAC: Control de Acceso a la Red

NAC (Network Access Control) es un conjunto de políticas y mecanismos que controlan qué dispositivos pueden conectarse a la red y qué acceso tienen, basándose en su identidad y su "postura" de seguridad.

!!! note "Definición"
    **NAC** (Network Access Control) es un enfoque de seguridad que verifica la identidad y el estado de seguridad de un dispositivo antes de permitirle acceder a la red, asignando el nivel de acceso adecuado según su postura.

### El Problema que Resuelve el NAC

Sin NAC, cualquier dispositivo que conecta un cable Ethernet o se une a la WiFi corporativa obtiene acceso a la red interna. Esto incluye:

- El portátil personal de un técnico visitante
- Un dispositivo IoT sin parches comprado por operaciones sin pasar por IT
- Un portátil corporativo que lleva semanas sin actualizarse y tiene malware

### Postura del Dispositivo

El concepto central del NAC es la "postura" — el estado de seguridad del dispositivo evaluado antes de darle acceso:

| Criterio de postura | Verificación | Acción si falla |
|--------------------|-------------|-----------------|
| **Antivirus activo y actualizado** | Agente NAC consulta el estado del AV | Cuarentena; acceso solo a repositorio de actualizaciones |
| **Sistema operativo con parches** | Se verifica el nivel de parcheo | Cuarentena con acceso a WSUS/SCCM para actualizar |
| **Certificado de dispositivo corporativo** | Autenticación 802.1X con certificado | Denegado si el dispositivo no tiene certificado válido |
| **Disco cifrado** | Agente NAC verifica BitLocker/FileVault | Advertencia; el dispositivo puede conectarse con acceso limitado |

### Tipos de NAC

**Pre-admission NAC:** El dispositivo es evaluado ANTES de obtener acceso a la red. Si falla, va a una VLAN de cuarentena donde solo puede remediar sus problemas (descargar actualizaciones, instalar antivirus). Solo cuando supera la evaluación obtiene acceso a la red de producción.

**Post-admission NAC:** El dispositivo obtiene acceso inicial, pero es monitoreado continuamente. Si su postura se degrada (desactiva el antivirus, se instala malware), el acceso se restringe automáticamente.

**802.1X — El Estándar de Autenticación de Red:**
El protocolo 802.1X es el mecanismo técnico principal del NAC. Opera en el nivel de switch/WiFi: cuando un dispositivo conecta un cable o intenta unirse a la WiFi, el switch no le da acceso hasta que el dispositivo presente credenciales válidas a un servidor de autenticación (RADIUS). Solo cuando el servidor RADIUS confirma la identidad y la postura, el switch activa el puerto y asigna la VLAN correspondiente.

```
Dispositivo (Supplicant)  ─── 802.1X ───▶  Switch (Authenticator)  ───▶  Servidor RADIUS
                          ◀─ VLAN asignada ─                        ◀─── Política de acceso
```

!!! example "Aplicación en entorno castrense"
    Un técnico trae su portátil personal para "revisar algo rápido" y lo conecta a un puerto Ethernet libre del pasillo. Sin NAC, el portátil obtiene una IP y accede a la red interna del batallón. Con 802.1X activo, el switch no le asigna IP hasta que el portátil presente un certificado corporativo válido. El portátil personal no tiene ese certificado — el switch lo pone en la VLAN de cuarentena donde solo hay acceso a Internet básico. La red interna del batallón permanece inaccesible para ese dispositivo no autorizado.

---

## Resumen

1. El tráfico en la red interna también debe cifrarse con TLS — la "confianza implícita" de la red interna es un modelo obsoleto que los atacantes explotan.
2. TLS establece un canal cifrado mediante un handshake que autentica el servidor, intercambia claves y cifra todos los datos posteriores.
3. VPN site-to-site conecta sedes permanentemente mediante un túnel cifrado transparente para los usuarios; VPN de acceso remoto permite a usuarios individuales conectarse desde fuera.
4. NAC controla qué dispositivos acceden a la red verificando su identidad y postura de seguridad antes o durante la conexión.
5. 802.1X es el protocolo técnico estándar para autenticación de dispositivos en la capa de red (switch/WiFi).

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **NIST SP 800-77 Rev. 1 — Guide to IPsec VPNs:** https://csrc.nist.gov/publications/detail/sp/800-77/rev-1/final
- **NIST SP 800-120 — Recommendation for EAP Methods Used in Wireless Network Access Authentication:** https://csrc.nist.gov/publications/detail/sp/800-120/final

---

*Siguiente: [Capa de Red y Endpoint (Parte 2) — EDR, Parches y Dispositivos Externos](capa-red-endpoint-p2.md)*
