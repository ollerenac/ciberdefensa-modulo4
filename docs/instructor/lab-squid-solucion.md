---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab Proxy Squid — Control de Navegación (LAB-03)

!!! danger "Solo para instructores"
    Este documento contiene las configuraciones esperadas, salidas de comandos y
    criterios de calificación. No mostrarlo en el proyector durante el lab. Está
    excluido del nav público de MkDocs pero es visible en el repositorio GitHub.

---

## Preparación del aula — verificar ANTES del lab

1. **La VM Ubuntu de los labs de iptables existe y arranca**, con NAT + Host-only.
   El snapshot `base-limpia` disponible como plan B.
2. **Firewall de la VM limpio**: `sudo iptables -L -n` — políticas ACCEPT, sin
   reglas. Si quedó persistido el escenario del lab iptables P2 (limpieza
   incompleta), la política DROP bloqueará el 3128 y el proxy "no funcionará" sin
   razón aparente. Remedio: reset seguro (políticas ACCEPT → flush) +
   `netfilter-persistent save`.
3. **Internet en la VM vía NAT**: `curl -4 -I http://example.com` responde. La
   instalación usa `apt` (paquete `squid`, componente main, ~unos MB con
   dependencias). Si el ancho de banda del aula es pobre, pre-descargar en cada
   VM antes de la clase: `sudo apt install --download-only -y squid`.
4. **IP Host-only del host Windows**: normalmente `192.168.56.1` (interfaz
   "VirtualBox Host-Only Network"). Es la IP que aparecerá como cliente en el
   `access.log`.

---

## Salidas esperadas — Paso 1 (instalación)

```
$ squid -v | head -1
Squid Cache: Version 6.x        (la 6.x exacta depende del parche de noble)

$ systemctl status squid --no-pager
● squid.service - Squid Web Proxy Server
     Loaded: loaded (/usr/lib/systemd/system/squid.service; enabled; ...)
     Active: active (running) ...

$ sudo ss -lntp | grep 3128
LISTEN 0 4096  *:3128  *:*  users:(("squid",pid=XXXX,fd=XX))
```

Puntos de dictado: `enabled` = arranca con la VM (un control de seguridad no puede
depender de que alguien lo lance a mano); el patrón `ss` puerto→proceso→PID es el
mismo de los labs de iptables — reforzarlo.

---

## squid.conf esperado por ejercicio

La lección hace **reescribir el archivo completo** en cada ejercicio (no anexar
bloques) precisamente para evitar reglas duplicadas y orden ambiguo. Config final
del Ejercicio 2 (acumulativo sobre el 1):

```text
http_port 3128
acl red_lab src 192.168.56.0/24
acl sitios_bloqueados dstdomain .facebook.com .instagram.com .twitter.com
acl palabras_bloqueadas url_regex -i juegos gaming streaming
http_access deny sitios_bloqueados
http_access deny palabras_bloqueadas
http_access allow red_lab
http_access deny all
```

Config del Ejercicio 3 (whitelist — **sustituye** las reglas de bloqueo):

```text
http_port 3128
acl red_lab src 192.168.56.0/24
acl sitios_permitidos dstdomain .example.com .wikipedia.org .ubuntu.com
http_access deny !sitios_permitidos
http_access allow red_lab
http_access deny all
```

!!! note "Nota para el instructor"
    El orden `deny` antes de `allow red_lab` es el punto conceptual del lab:
    primera coincidencia gana, igual que en iptables (`-I` vs `-A`). Si un alumno
    pone el deny después del allow, el bloqueo es inerte — dejar que lo descubra
    con la prueba y el log antes de corregirlo: es la mejor lección del día.

---

## Salidas esperadas del access.log

**Petición permitida (Paso 3, momento clave — la IP del cliente es el Windows):**
```text
1718500200.123   142 192.168.56.1 TCP_MISS/200 4521 GET http://example.com/ - DIRECT/93.184.216.34 text/html
```

**Petición bloqueada por ACL (Ejercicios 1-3):**
```text
1718500210.456     0 192.168.56.1 TCP_DENIED/403 3856 GET http://www.facebook.com/ - HIER_NONE/- text/html
```

**Túnel HTTPS permitido (aparecerá al navegar a sitios https de la whitelist):**
```text
1718500220.789  5142 192.168.56.1 TCP_TUNNEL/200 8412 CONNECT wikipedia.org:443 - DIRECT/... -
```

Comentar el `CONNECT dominio:443`: es la evidencia visual de que con HTTPS el
proxy solo ve el dominio — enlaza con la advertencia del Ejercicio 2 y con SSL
Bump de la P1. El `curl.exe -x` del Paso 3 debe devolver un encabezado con
`Via: 1.1 <hostname> (squid/6.x)` — la firma del proxy.

Convertir timestamps: `date -d @1718500200` en la VM.

---

## Verificación de cada ejercicio

### Ejercicio 1 — Lista negra

**Verificación:** `facebook.com` desde Firefox muestra la página de error de Squid
("The requested URL could not be retrieved" / Access Denied). Log: `TCP_DENIED/403`.

**Si no funciona:**

- ¿Recargó Squid? `sudo systemctl reload squid` tras cada edición — causa #1.
- ¿`squid -k parse` limpio? Un typo (p. ej. `http_acces`) genera `FATAL` y el
  reload deja el servicio con la config anterior.
- ¿El deny está ANTES de `http_access allow red_lab`?
- ¿Firefox realmente usa el proxy? Probar primero `http://example.com` — si no
  aparece en el log, el navegador no está enviando el tráfico al proxy.

### Ejercicio 2 — Palabras clave

**Verificación:** `http://www.juegos.com` bloqueado (`TCP_DENIED`).

**Si no funciona:** falta `-i` (el regex distingue mayúsculas por defecto). Si un
alumno prueba una URL HTTPS con la palabra en la **ruta** y no se bloquea: no es un
fallo — es la limitación CONNECT explicada en la advertencia del ejercicio;
convertirlo en pregunta al grupo.

### Ejercicio 3 — Whitelist

**Verificación:** `example.com` y `wikipedia.org` cargan (`TCP_MISS`/`TCP_TUNNEL`);
cualquier otro dominio → `TCP_DENIED`.

**Si no funciona:** verificar la negación `!sitios_permitidos` y su posición antes
del `allow red_lab`.

---

## Errores comunes y cómo manejarlos

| Error | Causa probable | Solución |
|-------|---------------|----------|
| `curl.exe -x` da "connection refused" | IP equivocada (usó la IP NAT `10.0.2.15` en vez de la Host-only) o Squid caído | La IP del proxy es la de `enp0s8` (192.168.56.x); `systemctl status squid` |
| `curl.exe -x` expira sin respuesta | Firewall de la VM con reglas del lab iptables aún activas | `sudo iptables -L -n` — si hay política DROP, reset seguro + `netfilter-persistent save` |
| "Mi regla no funciona" | Editó squid.conf sin recargar | El ciclo completo: editar → `squid -k parse` → `systemctl reload squid` → probar |
| El servicio no levanta tras un cambio | Error de sintaxis en squid.conf | `sudo squid -k parse` muestra la línea exacta del `FATAL`; corregir y `systemctl restart squid` |
| Todo da `TCP_DENIED`, incluso example.com | El `deny all` quedó antes del `allow red_lab`, o la ACL `red_lab` tiene una subred equivocada | Revisar orden y subred (debe ser la Host-only real, `ip -4 route` en la VM) |
| El log no muestra nada al navegar | Firefox sin el proxy configurado, o con excepción activa | Revisar Configuración manual del proxy; probar con `curl.exe -x` para aislar |
| `tail: cannot open access.log` | Falta sudo — el log pertenece al servicio | `sudo tail -f /var/log/squid/access.log` |
| Bloqueó facebook pero "sigue cargando" | Caché del navegador | Ctrl+Shift+R (recarga forzada) o modo privado |

---

## Notas de dictado

### Timing sugerido (~3 horas)

- Recap P1 + presentación de la topología (proxy en la VM, Windows cliente): ~10 min
- Paso 1 — instalación y verificación del servicio: ~15 min
- Paso 2 — squid.conf mínimo + ciclo parse/reload: ~25 min (el bloque conceptual denso)
- Paso 3 — conectar Windows: curl.exe primero, Firefox después, log en vivo: ~25 min
    - **El momento clave del lab:** el `tail -f` proyectado mientras un alumno
      navega desde Windows — la IP del cliente en el log hace tangible el "control
      centralizado" de la P1.
- Ejercicio 1 — lista negra: ~20 min (pausar en el porqué del orden deny/allow)
- Ejercicio 2 — regex + limitación HTTPS: ~20 min
- Ejercicio 3 — whitelist: ~20 min
- Lectura del access.log con anotación de campos: ~15 min
- Demo de bypass (con el log en silencio como evidencia): ~15 min
- Limpieza + entrega y cierre: ~15 min

### Momento pedagógico clave

El bypass: el alumno desactiva el proxy en Firefox, el dominio bloqueado carga, y
el `tail -f` proyectado **no imprime nada**. La ausencia de la línea es la
evidencia. Pregunta de cierre para conectar con la P1 y con el examen: "¿Qué
necesita una organización además de Squid para asegurar que todos los equipos usen
el proxy?" — Respuesta: la regla de firewall que bloquea la salida 80/443 a todo
proceso que no sea el proxy (o un proxy transparente).

---

## Rúbrica de calificación

| Criterio | Puntos | Cómo verificar |
|----------|--------|----------------|
| Squid activo y escuchando en el 3128 | 1 | Captura de `systemctl status` + `sudo ss -lntp \| grep 3128` |
| Cliente Windows navegando vía proxy | 1 | Línea del log con `192.168.56.1` como IP cliente (captura) |
| Dominio bloqueado (Ejercicio 1) con error de Squid | 2 | Captura de la página de error + línea `TCP_DENIED` del log |
| Whitelist funcionando (Ejercicio 3) | 2 | Dominio autorizado carga y uno no autorizado da `TCP_DENIED` (capturas) |
| Análisis del log: 5 líneas anotadas | 2 | Timestamp, IP cliente, código Squid/HTTP y URL identificados en cada línea |
| Bypass documentado con doble evidencia | 2 | Página cargando sin proxy **y** log sin líneas nuevas |

**Total: 10 puntos**

---

<!-- Solución instructor para: docs/seguridad-criptografia/en-la-red/proxy-squid-p2.md -->
