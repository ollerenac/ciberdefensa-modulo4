---
# Horas asignadas: 1 hr
# Tipo: Laboratorio (versión alumno)
---

# Lab: Filtrado de Paquetes con iptables en WSL2 (Parte 1)

> **Duración:** 1 hora | **Unidad:** En el Ordenador

!!! warning "Instrucciones generales"
    - Trabajar de forma individual salvo indicación del instructor.
    - Documentar cada paso con capturas de pantalla en su cuaderno.
    - Si un paso falla, **no avanzar** — notificar al instructor.

## Objetivos

Al finalizar este laboratorio, el alumno habrá:

- Verificado que WSL2 Ubuntu está operativo y que iptables está disponible
- Entendido el modelo de tablas y cadenas de iptables con un diagrama visual
- Aplicado una política por defecto DROP en INPUT y reglas selectivas de permiso
- Leído el ruleset con `iptables -L -v -n` e interpretado cada columna
- Verificado que las reglas funcionan con pruebas de conectividad

## Prerrequisitos

| Requisito | Verificación |
|-----------|-------------|
| WSL2 instalado con Ubuntu 22.04 | `wsl --list --verbose` muestra Ubuntu con estado Running |
| Ubuntu con permisos de root en WSL2 | `sudo -l` no pide contraseña adicional |
| Windows Terminal o PowerShell disponible | Abrir desde el menú Inicio |
| Acceso a internet desde WSL2 | `ping -c 3 8.8.8.8` desde dentro de Ubuntu responde |

!!! tip "Antes de empezar"
    Verificar cada prerrequisito antes de continuar. El laboratorio no funciona
    correctamente si falta alguno. Si WSL2 no está instalado, solicitar asistencia al
    instructor antes de intentar instalarlo durante el lab.

## Entorno de laboratorio

```
Windows 11 Host
└── WSL2 (Hyper-V VM interna)
    └── Ubuntu 22.04
        └── iptables (firewall de red del subsistema Linux)
            Interfaz: eth0 (IP interna WSL2, ej: 172.x.x.x)
```

!!! note ""
    WSL2 tiene su propia pila de red separada de Windows. Las reglas iptables que
    configuramos en Ubuntu **NO afectan el firewall de Windows** ni el tráfico del
    equipo anfitrión. Estamos operando dentro del subsistema Linux de forma aislada.

## Conceptos Clave: Tablas y Cadenas

Antes de ejecutar cualquier comando, es importante entender el modelo mental de iptables.
El tráfico de red pasa por **cadenas** (chains), y en cada cadena hay reglas que deciden
qué hacer con cada paquete.

| Cadena | Descripción | Uso típico en este lab |
|--------|-------------|------------------------|
| INPUT | Tráfico destinado a este equipo | Controlar quién puede conectarse |
| OUTPUT | Tráfico originado en este equipo | Controlar qué puede salir |
| FORWARD | Tráfico que pasa por el equipo como router | No usado en este lab |

**Flujo de paquetes (simplificado):**

```
Internet → [INPUT] → Proceso local
                          │
                          ▼
Internet ← [OUTPUT] ← Proceso local
```

!!! note "Tabla filter"
    En este lab usamos solo la tabla `filter` (la predeterminada de iptables). iptables
    tiene también las tablas `nat` y `mangle` — las mencionamos brevemente en la Parte 2.
    Cuando ejecutamos `iptables` sin especificar `-t tabla`, siempre operamos sobre `filter`.

## Pasos del Laboratorio

### Parte 1: Preparación del entorno

**Paso 1 — Abrir Ubuntu en WSL2 y verificar iptables**

En Windows Terminal, abrir una sesión Ubuntu. Verificar que tenemos acceso a iptables:

```bash
# Verificar que iptables está disponible
sudo iptables --version
# Salida esperada: iptables v1.8.x (nf_tables)
```

!!! question "Verificación"
    ¿Aparece la versión de iptables? Si el comando no existe, ejecutar
    `sudo apt-get install iptables -y` y reintentar.

**Paso 2 — Ver el estado inicial del firewall**

```bash
# Listar todas las reglas actuales con detalles
sudo iptables -L -v -n
```

!!! question "Verificación"
    El resultado debe mostrar tres cadenas (INPUT, FORWARD, OUTPUT) con política
    `ACCEPT` y 0 reglas. Este es el estado vacío — todo el tráfico pasa sin filtros.
    Las columnas `pkts` y `bytes` deben mostrar 0.

**Paso 3 — Limpiar cualquier regla existente**

```bash
# Eliminar todas las reglas existentes (flush)
sudo iptables -F
# Eliminar cadenas personalizadas si existen
sudo iptables -X
# Resetear contadores de paquetes y bytes
sudo iptables -Z
# Confirmar estado limpio
sudo iptables -L -v -n
```

!!! note ""
    Este paso asegura que partimos de un estado conocido. Si hay reglas anteriores
    de otra sesión, este flush las elimina. La política por defecto (ACCEPT o DROP)
    **no** se modifica con `-F`.

### Parte 2: Política por defecto DROP

**Paso 4 — Agregar regla de loopback primero (OBLIGATORIO)**

Antes de cambiar la política por defecto, siempre agregar la regla de loopback:

```bash
# Permitir todo el tráfico en la interfaz loopback (lo)
# Sin esta regla, muchos procesos locales dejan de funcionar
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
```

!!! warning "Atención: el orden importa"
    Si cambias la política a DROP sin agregar esta regla primero, comandos como
    `ping localhost` y procesos internos del sistema que usan la interfaz loopback
    dejarán de funcionar. **Siempre agregar la regla de loopback antes del DROP.**

**Paso 5 — Permitir conexiones establecidas**

```bash
# Permitir tráfico de respuesta a conexiones que el equipo inició
# Sin esta regla, el comando curl no puede recibir respuesta
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

!!! note "Filtrado stateful (seguimiento de conexiones)"
    Esta regla usa el módulo `state` (conntrack). Le dice a iptables: "si este paquete
    es parte de una conexión que yo inicié, déjalo pasar." Es la base del filtrado
    stateful — el kernel recuerda las conexiones activas y permite sus respuestas
    aunque la política general sea DROP.

**Paso 6 — Permitir puertos específicos**

```bash
# Permitir SSH entrante (puerto 22)
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
# Permitir HTTP entrante (puerto 80)
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

**Paso 7 — Aplicar política por defecto DROP**

```bash
# CAMBIAR política por defecto: bloquear todo lo que no tenga regla explícita
sudo iptables -P INPUT DROP
# OUTPUT lo dejamos en ACCEPT (podemos salir a cualquier destino)
sudo iptables -P OUTPUT ACCEPT
```

!!! warning "Principio de mínimo privilegio aplicado a la red"
    Este es el momento clave del laboratorio. A partir de ahora, **nada entra** a menos
    que lo hayamos permitido explícitamente con una regla. Todo lo que no coincida con
    una regla de ACCEPT cae a la política DROP y es silenciosamente descartado.

### Parte 3: Verificación y pruebas

**Paso 8 — Leer el ruleset completo**

```bash
# Ver todas las reglas con estadísticas de paquetes y bytes
sudo iptables -L -v -n
```

Anotar en el cuaderno el significado de cada columna:

| Columna | Significado |
|---------|-------------|
| pkts | Número de paquetes que coincidieron con esta regla |
| bytes | Bytes totales de los paquetes que coincidieron |
| target | Acción a tomar (ACCEPT, DROP, LOG, REJECT) |
| prot | Protocolo (tcp, udp, icmp, all) |
| in/out | Interfaz de entrada/salida (eth0, lo, etc.) |
| source | IP origen del paquete (0.0.0.0/0 = cualquiera) |
| destination | IP destino del paquete |

**Paso 9 — Probar conectividad**

```bash
# Prueba 1: loopback debe funcionar (regla de lo ACCEPT)
ping -c 3 127.0.0.1

# Prueba 2: acceso HTTP a internet
# Debe funcionar: OUTPUT es ACCEPT y ESTABLISHED permite la respuesta entrante
curl -I http://example.com

# Prueba 3: bloquear IP específica (añadir regla de bloqueo)
sudo iptables -A INPUT -s 8.8.8.8 -j DROP
# Intentar ping a esa IP (debe fallar: timeout sin respuesta)
ping -c 3 8.8.8.8
```

!!! question "Verificación"
    - ¿El ping a 127.0.0.1 responde con 3 paquetes recibidos?
    - ¿El curl a example.com devuelve `HTTP/1.1 200 OK`?
    - ¿El ping a 8.8.8.8 no recibe respuesta (timeout o sin contestación)?

## Validación final

Al terminar todas las partes, verificar:

- [ ] `sudo iptables --version` muestra la versión instalada (v1.8.x o similar)
- [ ] `sudo iptables -L -n` muestra política INPUT DROP con reglas de loopback y ESTABLISHED
- [ ] `curl -I http://example.com` devuelve código HTTP (no error de conexión)
- [ ] El ping a la IP bloqueada (8.8.8.8) no recibe respuesta (timeout)

## Complemento OFFen

!!! info "Ejercicio complementario (opcional)"
    Este laboratorio se complementa con cualquier desafío de reconocimiento y
    enumeración en la plataforma OFFen. Un escáner externo que intente conectarse
    a los puertos no listados en las reglas de ACCEPT descubrirá que no responden —
    esto es exactamente la perspectiva del atacante que los controles defensivos de
    este lab buscan neutralizar. El comportamiento del firewall desde afuera confirma
    que las reglas DROP funcionan como se diseñaron.

    Acceder desde: `http://[ip-offen-server]` con las credenciales del curso.

## Entrega

El alumno debe entregar:

1. Captura de pantalla de `sudo iptables -L -v -n` con las reglas del Paso 7 aplicadas
   (debe mostrar las 4 reglas: loopback ACCEPT, ESTABLISHED ACCEPT, tcp:22 ACCEPT, tcp:80 ACCEPT)
2. Resultado de la Prueba 1 (ping loopback — debe mostrar 3 paquetes recibidos)
3. Resultado de la Prueba 2 (curl example.com — debe mostrar HTTP 200)
4. Resultado de la Prueba 3 (ping 8.8.8.8 — debe mostrar timeout o sin respuesta)

---

<!-- Solución disponible para el instructor en: docs/instructor/lab-iptables-p1-solucion.md -->

*Continúa en: [Lab iptables (Parte 2)](lab-iptables-p2.md)*
