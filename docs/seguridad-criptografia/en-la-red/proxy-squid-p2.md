---
# Horas asignadas: 3 hrs
# Tipo: Teoría + Laboratorio (versión alumno)
---

# Proxy Squid: Configuración y Práctica (Parte 2)

> **Duración:** 3 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red | **Continuación de Parte 1 — incluye laboratorio integrado**

## Recap de la Parte 1

En la Parte 1 aprendimos que un proxy forward actúa como intermediario entre los
clientes internos e internet, evaluando ACLs para permitir o denegar peticiones
HTTP/HTTPS y registrando toda la actividad en el `access.log`. También vimos que el
proxy debe estar acompañado de una regla de firewall para evitar que los usuarios lo
bypaseen. En esta sesión convertimos la **VM Ubuntu en el servidor proxy de la red**
y el **host Windows en el cliente** que navega a través de él — la misma topología
de dos máquinas de los laboratorios de iptables, con los papeles invertidos: ahora
la VM es el control y Windows es el usuario.

---

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Instalar Squid en la VM Ubuntu y verificar el servicio y su puerto de escucha
- Escribir una configuración mínima de `squid.conf` y validar cada cambio antes de aplicarlo
- Configurar el navegador del host Windows para navegar a través del proxy de la VM
- Aplicar ACLs para bloquear dominios, palabras clave y construir una whitelist
- Leer y analizar el `access.log` identificando cliente, URL, resultado y bytes
- Demostrar que el proxy sin regla de firewall complementaria puede ser bypasseado

---

## Entorno de laboratorio

```
Host Windows 11 (cliente)                VM Ubuntu 24.04 (servidor proxy)
   Firefox / PowerShell                        Squid en puerto 3128
        │                                            │
        └────── red Host-only (192.168.56.x) ────────┤
                                                     │ Adaptador NAT
                                                     ▼
                                                 Internet
```

El navegador del Windows ya no conecta con los servidores web: entrega cada
petición a Squid (en la VM), Squid la evalúa contra sus ACLs, la realiza en nombre
del cliente a través de su adaptador NAT y devuelve la respuesta.

**Prerrequisitos:**

| Requisito | Verificación |
|-----------|-------------|
| VM Ubuntu 24.04 con NAT + Host-only (labs iptables) | `ip -4 addr` muestra ambas interfaces |
| Firewall de la VM limpio (limpieza del lab iptables P2) | `sudo iptables -L -n` — políticas ACCEPT, sin reglas |
| Conectividad host ↔ VM | `ping <IP-de-la-VM>` desde PowerShell responde |
| Internet en la VM (vía NAT) | `curl -4 -I http://example.com` desde la VM responde |

---

## Paso 1 — Instalar Squid en la VM

**[VM Ubuntu — Bash]**

```bash
# Instalar Squid desde los repositorios oficiales (componente main)
sudo apt update && sudo apt install -y squid

# Verificación 1: el servicio quedó activo y habilitado
systemctl status squid --no-pager

# Verificación 2: ¿qué proceso escucha en el 3128? (patrón del lab iptables)
sudo ss -lntp | grep 3128
```

!!! question "Verificación"
    `systemctl status` muestra `active (running)` y `enabled`. `ss` muestra el
    puerto `3128` con `users:(("squid",pid=...))` — el mismo método
    puerto→proceso→PID de los labs anteriores. Anotar la versión con
    `squid -v | head -1` (Ubuntu 24.04 instala Squid 6.x).

!!! note "Por qué un servicio y no un programa"
    Squid se instala como **servicio de systemd**: arranca solo con la VM y corre
    en segundo plano permanentemente. Un proxy que hay que lanzar a mano no sirve
    como control de seguridad — el control tiene que estar siempre de pie.

---

## Paso 2 — Configuración mínima de squid.conf

El archivo de configuración es `/etc/squid/squid.conf`. El que instala el paquete
tiene miles de líneas de documentación; para entender qué hace cada pieza, lo
respaldamos y escribimos uno mínimo desde cero:

**[VM Ubuntu — Bash]**

```bash
# Respaldar la configuración original (patrón: capturar antes de modificar)
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.original

# Escribir la configuración mínima del laboratorio
sudo nano /etc/squid/squid.conf
```

Borrar todo el contenido y dejar exactamente esto:

```squid
# Puerto en que escucha Squid (3128 es el estándar)
http_port 3128

# ACL que define la red del laboratorio (la subred Host-only)
acl red_lab src 192.168.56.0/24

# Permitir el acceso de la red del laboratorio
http_access allow red_lab

# Denegar todo lo demás (postura por defecto)
http_access deny all
```

**Qué hace cada línea y por qué:**

| Línea | Por qué existe |
|-------|----------------|
| `http_port 3128` | El puerto donde los clientes entregan sus peticiones |
| `acl red_lab src ...` | Define **quién** es la red del lab — solo define, no decide |
| `http_access allow red_lab` | Decide: los clientes de esa subred pueden usar el proxy |
| `http_access deny all` | La red de seguridad: lo que no coincidió arriba, se niega |

!!! note "Primera coincidencia gana — otra vez"
    Las reglas `http_access` se evalúan **en orden, de arriba a abajo, y la primera
    que coincide decide**. Es exactamente la misma lógica que las cadenas de
    iptables (la lección de `-I` vs `-A`): el `deny all` final es el equivalente de
    la política DROP — todo lo no permitido explícitamente, muere.

Aplicar el cambio — **siempre validar antes de recargar**:

```bash
# 1. Validar la sintaxis SIN aplicar (un error de tipeo tumbaría el servicio)
sudo squid -k parse

# 2. Si no hay líneas FATAL: recargar el servicio para que lea el archivo nuevo
sudo systemctl reload squid
```

!!! warning "El ciclo de todo cambio en squid.conf"
    Squid lee su configuración **solo al arrancar o recargar**. Editar el archivo
    no cambia nada por sí solo. El ciclo completo, para cada ejercicio de hoy:
    **editar → `squid -k parse` → `systemctl reload squid` → probar**.
    Saltarse el reload es la causa #1 de "mi regla no funciona".

---

## Paso 3 — Conectar el cliente Windows

Primero una prueba **sin navegador** — así, si algo falla, sabemos que el problema
es el proxy y no la configuración de Firefox:

**[Windows — PowerShell]**

```powershell
# Pedir example.com A TRAVÉS del proxy de la VM (-x = usar este proxy)
curl.exe -x http://<IP-de-la-VM>:3128 -I http://example.com
```

Debe responder un encabezado HTTP con una línea `Via: ... (squid/6.x)` — la firma
de que la respuesta pasó por el proxy.

Ahora el navegador. En **Firefox** del host Windows:

1. Ajustes → General → (al final) Configuración de red → **Configuración...**
2. Seleccionar **Configuración manual del proxy**
3. Proxy HTTP: `<IP-de-la-VM>` — Puerto: `3128`
4. Marcar **"Usar también este proxy para HTTPS"**
5. Aceptar, y navegar a `http://example.com`

Mientras tanto, en la VM, observar el log en tiempo real:

**[VM Ubuntu — Bash]**

```bash
# El log pertenece al servicio — se lee con sudo
sudo tail -f /var/log/squid/access.log
```

!!! question "Verificación — el momento clave del laboratorio"
    En el log aparece la petición de `example.com` con la **IP del host Windows**
    (`192.168.56.1`) como cliente. Estás viendo navegar **a otra máquina** desde el
    servidor proxy: esto es el "control centralizado" de la Parte 1 hecho realidad.
    Una sola configuración en este servidor gobernará ahora todo lo que ese cliente
    puede ver.

---

## Los ejercicios: imponer política con ACLs

El ciclo es el mismo en los tres ejercicios: **editar `squid.conf` en la VM →
validar → recargar → probar desde Windows → leer el log**. En cada ejercicio se
muestra el archivo **completo** — editar el existente, no añadir bloques duplicados.

### Ejercicio 1 — Lista negra de dominios

**[VM Ubuntu — Bash]** — `sudo nano /etc/squid/squid.conf`:

```squid
http_port 3128

acl red_lab src 192.168.56.0/24

# ACL: dominios prohibidos (el punto inicial cubre también subdominios)
acl sitios_bloqueados dstdomain .facebook.com .instagram.com .twitter.com

# La denegación va ANTES del allow — primera coincidencia gana
http_access deny sitios_bloqueados
http_access allow red_lab
http_access deny all
```

```bash
sudo squid -k parse && sudo systemctl reload squid
```

**[Windows]** — Navegar a `facebook.com`: Firefox muestra la **página de error de
Squid** ("The requested URL could not be retrieved / Access Denied") — no un error
del navegador. La petición murió en el proxy; nunca salió a internet.

!!! question "Verificación"
    En el `access.log` de la VM aparece la línea con `TCP_DENIED/403`, la IP del
    Windows y el dominio bloqueado. ¿Por qué la regla deny va antes del allow?
    Si estuviera después, `allow red_lab` coincidiría primero y el bloqueo sería
    inerte — el mismo error que la regla `-A` del Paso 10 del lab iptables.

### Ejercicio 2 — Bloqueo por palabras clave

Añadir al archivo (queda mostrado completo):

```squid
http_port 3128

acl red_lab src 192.168.56.0/24

acl sitios_bloqueados dstdomain .facebook.com .instagram.com .twitter.com

# ACL: palabras prohibidas en la URL (-i = ignorar mayúsculas/minúsculas)
acl palabras_bloqueadas url_regex -i juegos gaming streaming

http_access deny sitios_bloqueados
http_access deny palabras_bloqueadas
http_access allow red_lab
http_access deny all
```

```bash
sudo squid -k parse && sudo systemctl reload squid
```

**[Windows]** — Probar `http://www.juegos.com` → bloqueado (`TCP_DENIED`).

!!! warning "La letra pequeña del regex con HTTPS"
    En una petición **HTTPS**, el navegador solo le dice al proxy el **dominio**
    (`CONNECT dominio:443`) — la ruta y el resto de la URL viajan cifrados y el
    regex **no puede verlos**. `juegos.com` se bloquea porque la palabra está en el
    dominio; una URL como `https://sitio.com/juegos` pasaría sin bloquear. Es la
    limitación que la Parte 1 explicó en la sección de SSL Bump: sin romper el
    cifrado, el proxy solo ve el dominio. El bloqueo por palabra clave es efectivo
    para HTTP y para palabras en el dominio — no para rutas HTTPS.

### Ejercicio 3 — Whitelist: solo lo autorizado

Invertir la lógica: en vez de prohibir lo malo conocido, **permitir solo lo bueno
autorizado**. Sustituir las reglas de bloqueo (archivo completo):

```squid
http_port 3128

acl red_lab src 192.168.56.0/24

# ACL: los únicos dominios autorizados
acl sitios_permitidos dstdomain .example.com .wikipedia.org .ubuntu.com

# Denegar todo lo que NO esté en la lista (! = negación)
http_access deny !sitios_permitidos
http_access allow red_lab
http_access deny all
```

```bash
sudo squid -k parse && sudo systemctl reload squid
```

**[Windows]** — `example.com` y `wikipedia.org` cargan; **cualquier otro dominio**
es denegado.

!!! note "Whitelist = el 'default deny' de capa 7"
    Es la misma filosofía que la política `INPUT DROP` de iptables, un nivel más
    arriba: por defecto nada pasa, y cada permiso es una decisión explícita del
    administrador. Más seguro, más mantenimiento — el enfoque correcto para redes
    con internet muy restringido.

---

## Lectura del access.log

Cada petición procesada deja una línea. Ejemplo real de este laboratorio:

```
1718500200.123   142 192.168.56.1 TCP_MISS/200 4521 GET http://example.com/ - DIRECT/93.184.216.34 text/html
```

| Campo | Valor en el ejemplo | Descripción |
|-------|--------------------|----|
| Timestamp Unix | `1718500200.123` | Fecha y hora en formato Unix epoch |
| Tiempo de respuesta | `142` | Milisegundos que tardó Squid en responder |
| IP del cliente | `192.168.56.1` | El host Windows — quién pidió |
| Código Squid/HTTP | `TCP_MISS/200` | Qué hizo Squid / qué respondió el servidor |
| Bytes transferidos | `4521` | Tamaño de la respuesta |
| Método HTTP | `GET` | Tipo de petición (GET, POST, CONNECT...) |
| URL | `http://example.com/` | Lo que se pidió |
| Usuario | `-` | Sin autenticación configurada |
| Tipo de conexión | `DIRECT` | Squid fue directo al servidor de origen |
| IP del servidor destino | `93.184.216.34` | Quién respondió realmente |
| Tipo de contenido | `text/html` | Tipo MIME de la respuesta |

**Códigos Squid más comunes:**

| Código | Significado |
|--------|------------|
| `TCP_MISS` | Petición enviada al servidor (no estaba en caché) |
| `TCP_HIT` | Servida desde la caché de Squid |
| `TCP_DENIED` | Bloqueada por una ACL |
| `TCP_TUNNEL` | Túnel HTTPS (método CONNECT) — Squid solo ve el dominio |

Convertir el timestamp a fecha legible:

```bash
date -d @1718500200
```

---

## Demostración de bypass

**Objetivo:** comprobar empíricamente que el proxy solo controla el tráfico que
pasa a través de él.

1. Con la whitelist activa, confirmar desde Windows que `facebook.com` está
   bloqueado.
2. En la VM, dejar corriendo `sudo tail -f /var/log/squid/access.log`.
3. En Firefox: Configuración de red → **Sin proxy** → Aceptar.
4. Navegar a `facebook.com` — **carga normalmente**.
5. Mirar el log de la VM: **no apareció ninguna línea nueva**.

!!! question "Verificación — la ausencia como evidencia"
    La página cargó y el log quedó en silencio: el tráfico ya no atraviesa el
    proxy, así que el proxy ni bloquea ni registra. Esa línea que **no** está en el
    log es la prueba del bypass. Conclusión de la unidad: el proxy define la
    **política**, pero la **obligatoriedad** la pone el firewall (bloquear la
    salida 80/443 a todo lo que no sea el proxy) — las dos capas de la Parte 1.

---

## Limpieza final

**[VM Ubuntu — Bash]**

```bash
# Restaurar la configuración original de Squid
sudo cp /etc/squid/squid.conf.original /etc/squid/squid.conf
sudo squid -k parse && sudo systemctl reload squid
```

**[Windows]** — Firefox: Configuración de red → **Sin proxy** (dejar el navegador
como estaba).

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Un Técnico detecta en el `access.log` que un equipo de la unidad (IP
    192.168.1.47) realizó 200 peticiones en una hora a un dominio que contiene
    "upload" y "cloud" en la URL — patrón de posible exfiltración de datos. El
    método HTTP es `POST` y los bytes transferidos acumulan más de 50 MB. El
    Técnico reporta inmediatamente al oficial de seguridad con el extracto del log
    como evidencia y bloquea el dominio en `squid.conf` de forma preventiva. La
    investigación posterior confirma que el equipo tenía un troyano de
    exfiltración. Sin el proxy, esta actividad hubiera sido invisible: el firewall
    de capa 3 solo registra IP origen, IP destino y puerto — sin URL, sin método
    HTTP, sin tamaño de datos.

---

## Validación del laboratorio

Al terminar las prácticas, verificar que se cumple cada punto:

- [ ] `systemctl status squid` muestra `active (running)` y `sudo ss -lntp` muestra el proceso `squid` en el 3128
- [ ] `curl.exe -x` desde Windows devuelve encabezados con la firma `Via: ... squid`
- [ ] El `access.log` muestra las peticiones del navegador con la IP del host Windows como cliente
- [ ] El dominio bloqueado (Ejercicio 1) muestra la página de error de Squid, y el log registra `TCP_DENIED`
- [ ] La whitelist (Ejercicio 3) permite los dominios autorizados y deniega el resto
- [ ] El bypass está documentado: página cargando sin proxy **y** log sin líneas nuevas
- [ ] La configuración original de Squid quedó restaurada

---

## Resumen

- Squid se instala en la VM Ubuntu con `apt` y corre como servicio de systemd escuchando en el 3128; el host Windows navega a través de él por la red Host-only
- Todo cambio de configuración sigue el ciclo editar → `squid -k parse` → `systemctl reload` → probar
- Las ACLs se evalúan en orden y la primera coincidencia gana — la misma lógica de primera-coincidencia de iptables, y la whitelist es el "default deny" de capa 7
- El `access.log` registra quién pidió qué, cuándo, cuánto y con qué resultado — visibilidad de capa de aplicación que ningún firewall de capa 3/4 puede dar
- Con HTTPS el proxy solo ve el dominio (CONNECT); inspeccionar más requiere SSL Bump, con sus implicaciones legales
- El proxy sin firewall complementario es bypasseable: el proxy pone la política, el firewall pone la obligatoriedad

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- Referencia completa de `squid.conf`: `http://www.squid-cache.org/Doc/config/` — todos los parámetros con descripción
- Documentación oficial de Squid: `https://wiki.squid-cache.org/` — guías de configuración avanzada

<!-- Solución disponible para el instructor en: docs/instructor/lab-squid-solucion.md -->

---

*Siguiente: [IDS e IPS (Parte 1)](ids-ips-p1.md)*
