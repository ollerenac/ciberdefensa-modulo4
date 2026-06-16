---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab Proxy Squid — Control de Navegación (LAB-03)

!!! danger "Solo para instructores"
    Este documento contiene las configuraciones esperadas, salidas de comandos y criterios de calificación. No mostrarlo en el proyector durante el lab. Está excluido del nav público de MkDocs pero es visible en el repositorio GitHub.

---

## Prerrequisito de Instalación — Verificar ANTES del lab

La parte más crítica de este lab. Verificar estos puntos antes de la clase:

1. **Descargar el instalador de Squid para Windows** desde [squid.diladele.com](http://squid.diladele.com) (archivo `.MSI` de aproximadamente 15 MB). Guardar en USB para distribución offline si la conexión del aula es lenta.

2. **Verificar que Python no está instalado en los equipos del aula** — puede entrar en conflicto con el installer de Squid en algunos sistemas. Si Python está instalado, hacer la prueba de instalación de Squid en un equipo de prueba antes del lab.

3. **Verificar que el Puerto 3128 no está en uso**: abrir CMD como Administrador y ejecutar:
   ```cmd
   netstat -an | findstr 3128
   ```
   Si muestra algún resultado, hay un conflicto. Identificar el proceso con PID usando `netstat -ano | findstr 3128` y terminarlo desde el Administrador de Tareas antes de la clase.

4. **Tener la URL de un dominio de prueba** que sea claramente no laboral para el ejercicio de bloqueo. Usar algo como `facebook.com` o `instagram.com` — dominios conocidos por los alumnos maximizan el impacto visual del bloqueo.

5. **Instalar VC++ Redistributable 2019 x64** en los equipos del aula si la instalación de Squid falla por error de DLL. Descargable desde microsoft.com sin registro.

---

## Squid.conf Esperado al Final del Lab

El archivo de configuración que el alumno debe tener al terminar todos los ejercicios. Ubicación en Windows: `C:\Squid\etc\squid.conf`

```text
# Puerto de escucha
http_port 3128

# ACL: red local (cliente)
acl red_local src 127.0.0.1/32

# === EJERCICIO 1: Bloqueo de dominio ===
acl sitio_bloqueado dstdomain .facebook.com .instagram.com
http_access deny sitio_bloqueado

# === EJERCICIO 2: Palabras clave bloqueadas ===
acl palabras_bloqueadas url_regex -i juegos gaming entretenimiento
http_access deny palabras_bloqueadas

# === EJERCICIO 3 (opcional): Whitelist ===
# acl sitios_permitidos dstdomain .microsoft.com .windows.com .google.com
# http_access deny !sitios_permitidos

# Permitir acceso desde red local (siempre al final)
http_access allow red_local

# Denegar todo lo demás
http_access deny all
```

!!! note "Nota para el instructor"
    Los Ejercicios 1 y 2 son acumulativos — las ACLs se apilan. El Ejercicio 3 (whitelist) reemplaza los anteriores conceptualmente. Si el tiempo lo permite, hacer que el alumno comente los Ejercicios 1 y 2 y pruebe la whitelist por separado para ver la diferencia de comportamiento.

---

## Salidas Esperadas del access.log

Cómo se ve el log para cada tipo de petición. El instructor puede mostrar esto en el proyector cuando explica la sección "Lectura del access.log".

**Log de petición exitosa — TCP_MISS** (petición enviada al servidor de origen, respuesta no cacheada):
```text
1718500200.123   142 127.0.0.1 TCP_MISS/200 4521 GET http://example.com/ - DIRECT/93.184.216.34 text/html
```

**Log de petición bloqueada por ACL — TCP_DENIED** (bloqueada por una regla `http_access deny`):
```text
1718500210.456     0 127.0.0.1 TCP_DENIED/403 3856 GET http://facebook.com/ - NONE/- text/html
```

**Log de petición servida desde caché — TCP_HIT** (puede no ocurrir en el lab si solo se hace una petición por recurso):
```text
1718500220.789     2 127.0.0.1 TCP_HIT/200 4521 GET http://example.com/ - NONE/- text/html
```

**Comando para leer el log en tiempo real desde PowerShell:**
```powershell
Get-Content -Wait "C:\Squid\var\log\squid\access.log" | Select-String "127.0.0.1"
```

**Explicación de campos del log (mostrar en proyector):**

| Campo | Ejemplo | Significado |
|-------|---------|-------------|
| Timestamp Unix | `1718500200.123` | Segundos desde 1970-01-01 UTC + milisegundos |
| Tiempo de respuesta (ms) | `142` | Cuánto tardó Squid en responder al cliente |
| IP cliente | `127.0.0.1` | Quien hizo la petición |
| Resultado Squid/HTTP | `TCP_MISS/200` | Qué hizo Squid + código HTTP del servidor |
| Tamaño (bytes) | `4521` | Tamaño total de la respuesta |
| Método HTTP | `GET` | Verbo HTTP |
| URL | `http://example.com/` | Recurso solicitado |
| Servidor origen | `DIRECT/93.184.216.34` | IP del servidor destino (NONE si bloqueado) |
| Tipo MIME | `text/html` | Tipo de contenido |

---

## Verificación de Cada Ejercicio

### Ejercicio 1 — Bloquear dominio

**Verificación:** Navegar a `facebook.com` con el proxy configurado en el navegador debe mostrar la página de error de Squid ("ERR_ACCESS_DENIED" con fondo blanco y texto "The requested URL could not be retrieved"). En el `access.log` debe aparecer `TCP_DENIED/403`.

**Si no funciona:**

- Verificar que Squid fue reiniciado después del cambio al `squid.conf` → `services.msc → Squid → Reiniciar`, o desde CMD como Administrador: `net stop squid && net start squid`
- Verificar que la ACL de bloqueo está **ANTES** de la línea `http_access allow red_local` en el archivo
- Verificar que el navegador está configurado para usar el proxy (`localhost:3128`) — abrir `http://example.com` primero para confirmar que el proxy funciona antes de probar el bloqueo

### Ejercicio 2 — Palabras clave

**Verificación:** Intentar acceder a una URL que contenga la palabra "juegos" debe ser bloqueada. Por ejemplo: `http://www.juegos.com`. En el log debe aparecer `TCP_DENIED`.

**Si no funciona:** El regex de `url_regex` es case-sensitive por defecto sin `-i`. Verificar que la regla tiene la opción `-i` para ignorar mayúsculas/minúsculas: `acl palabras_bloqueadas url_regex -i juegos gaming`.

### Ejercicio 3 — Whitelist (si hay tiempo)

**Verificación:** Acceder a `microsoft.com` debe funcionar (log: `TCP_MISS/200`). Acceder a cualquier otro sitio no en la lista debe ser bloqueado (log: `TCP_DENIED`).

**Si no funciona:** Verificar que la regla `http_access deny !sitios_permitidos` está **ANTES** de `http_access allow red_local` en el archivo. El orden de las reglas `http_access` es crítico — Squid aplica la primera regla que coincide.

---

## Errores Comunes y Cómo Manejarlos

| Error | Causa probable | Solución |
|-------|---------------|----------|
| El servicio Squid no inicia | Puerto 3128 en uso por otro proceso | `netstat -ano \| findstr 3128` — identificar el proceso con PID y terminarlo desde el Administrador de Tareas |
| El navegador da error "connection refused" al usar el proxy | Squid no está corriendo | Verificar en `services.msc`; reiniciar el servicio. Verificar también que `localhost:3128` está en la configuración del proxy del navegador |
| El sitio bloqueado sigue siendo accesible después de agregar la ACL | Squid no fue reiniciado después del cambio al `squid.conf` | `net stop squid && net start squid` en CMD como Administrador, o reiniciar desde `services.msc` |
| El log muestra TCP_MISS pero la página cargó — ¿está usando el proxy? | El navegador puede haber cargado desde caché local (no del proxy) | Limpiar caché del navegador (Ctrl+Shift+Delete) y reintentar. Verificar también que el navegador no tiene configurada una excepción de proxy para `localhost` |
| Squid instalador da error de DLL durante la instalación | Dependencia de Visual C++ Redistributable faltante | Instalar VC++ Redistributable 2019 x64 desde microsoft.com antes de Squid |
| El `access.log` está vacío o no aparece | El navegador no está usando el proxy | Verificar configuración de proxy en el navegador — confirmar que el proxy es `localhost` puerto `3128`. Navegar a `http://example.com` (HTTP, no HTTPS) para la prueba inicial |
| HTTPS no funciona a través del proxy | Squid necesita configuración SSL adicional para HTTPS | Para el lab, usar únicamente sitios HTTP (`http://`, no `https://`). La inspección HTTPS requiere instalar un certificado de CA — fuera del alcance del lab |

---

## Notas de Dictado

### Timing sugerido (total: ~90 minutos de lab)

**Instalación (15 min):** Hacer que todos instalen Squid al mismo tiempo. Si algún equipo falla, agrupar con un compañero en lugar de retrasar al grupo. El instalador MSI es rápido si ya está descargado en USB.

**Configuración inicial y verificación de proxy en el navegador (10 min):** Verificar que **TODOS** tienen el proxy funcionando antes de avanzar a las ACLs. Este es el prerrequisito crítico para el resto del lab. La señal de éxito: el alumno abre `http://example.com` y el log muestra `TCP_MISS`.

**Ejercicio 1 — bloqueo de dominio (20 min):** El momento más impactante del lab — cuando el alumno ve por primera vez que Squid bloquea el acceso. Pausar aquí y pedir que lean la página de error de Squid — el mensaje explica qué regla bloqueó la petición.

**Lectura del access.log (15 min):** Mostrar en proyector cómo hacer `Get-Content -Wait` y leer el log en tiempo real mientras otro alumno navega. Demostrar la diferencia entre `TCP_MISS` y `TCP_DENIED`. Pedir que un alumno navegue al dominio bloqueado y señalar la entrada en el log.

**Ejercicios 2 y 3 (30 min):** Los alumnos más rápidos pueden llegar a la whitelist; los más lentos hacen el Ejercicio 2. Ambas trayectorias son válidas para la rúbrica.

### Momento pedagógico clave

La demostración de bypass al final: desactivar el proxy en el navegador (o abrirlo en modo privado que puede tener configuración diferente) y acceder al dominio bloqueado. El impacto visual de ver que Squid no puede proteger si el cliente no lo usa es el mensaje más importante del lab. Conectar esto con la pregunta del examen: "¿Qué necesita una organización además de Squid para asegurar que todos los equipos usen el proxy?"

---

## Rúbrica de Calificación

| Criterio | Puntos | Cómo verificar |
|----------|--------|----------------|
| Squid instalado y corriendo | 1 | `services.msc` muestra "Squid" con estado "En ejecución" |
| Navegador conecta a `http://example.com` via proxy | 1 | El log muestra `TCP_MISS` para `example.com` (alumno muestra captura del log) |
| Dominio bloqueado (Ejercicio 1) muestra error Squid | 2 | Captura de pantalla de la página de error "ERR_ACCESS_DENIED" de Squid |
| Log muestra `TCP_DENIED` correctamente | 1 | Al menos 1 línea `TCP_DENIED` en el extracto entregado por el alumno |
| Análisis del log: 5 líneas anotadas con identificación de campos | 3 | Entregable con timestamp, IP cliente, código Squid/HTTP y URL identificados en cada línea |
| Demostración de bypass documentada (dominio accesible sin proxy) | 2 | Captura de pantalla del sitio cargando con el proxy desactivado |

**Total: 10 puntos**

---

## Material de Apoyo para el Instructor

Recursos que el instructor puede mostrar en el proyector si hay preguntas:

- **Documentación de directivas squid.conf:** http://www.squid-cache.org/Doc/config/
- **Convertir timestamp Unix a fecha legible** (si el alumno pregunta qué significa el número en el log):
  ```cmd
  python -c "import datetime; print(datetime.datetime.fromtimestamp(1718500200))"
  ```
  Resultado: `2024-06-15 19:30:00` (aproximado — el valor exacto depende del timestamp).

---

<!-- Solución instructor para: docs/seguridad-criptografia/en-la-red/proxy-squid-p2.md -->
