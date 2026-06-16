---
# Horas asignadas: 3 hrs
# Tipo: Teoría + Laboratorio (versión alumno)
---

# Proxy Squid: Configuración y Práctica (Parte 2)

> **Duración:** 3 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red | **Continuación de Parte 1 — incluye laboratorio integrado**

## Recap de la Parte 1

En la Parte 1 aprendimos que un proxy forward actúa como intermediario entre los clientes internos e internet, evaluando ACLs para permitir o denegar peticiones HTTP/HTTPS y registrando toda la actividad en el `access.log`. También vimos que el proxy debe estar acompañado de una regla de firewall para evitar que los usuarios lo bypaseen configurando su navegador directamente. En esta sesión instalamos y configuramos Squid en Windows 11, aplicamos reglas ACL reales y leemos e interpretamos el log de acceso.

---

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Instalar y verificar el servicio Squid en Windows 11 usando el instalador oficial
- Configurar el navegador Firefox o Chrome para usar el proxy local
- Aplicar ACLs en `squid.conf` para bloquear dominios y palabras clave
- Leer y analizar líneas del `access.log` de Squid identificando cada campo
- Demostrar que el proxy sin regla de firewall complementaria puede ser bypasseado

---

## Instalación de Squid en Windows 11

Squid tiene un instalador oficial para Windows mantenido por la empresa Diladele.

**Paso 1 — Descargar el instalador:**
Ir a `http://squid.diladele.com/` y descargar la versión MSI de 64 bits para Windows. La página muestra la versión disponible (normalmente Squid 5.x para Windows).

**Paso 2 — Instalar como Administrador:**
Hacer clic derecho sobre el archivo `.msi` descargado y seleccionar "Ejecutar como administrador". Seguir el asistente de instalación con las opciones predeterminadas.

**Paso 3 — Verificar el servicio:**
Al terminar la instalación, Squid queda registrado como servicio de Windows. Para verificar:
- Presionar `Win + R`, escribir `services.msc` y pulsar Enter
- Buscar "Squid Web Proxy Cache" en la lista
- El estado debe mostrar "En ejecución" (Running)

**Rutas importantes después de la instalación:**

| Archivo | Ruta |
|---------|------|
| Configuración | `C:\Squid\etc\squid\squid.conf` |
| Log de acceso | `C:\Squid\var\log\squid\access.log` |
| Ejecutable | `C:\Squid\bin\squid.exe` |

!!! tip "Si el servicio no inicia"
    Verificar que el puerto 3128 no está en uso por otro proceso. En CMD como Administrador:
    ```
    netstat -an | findstr 3128
    ```
    Si aparece alguna línea, otro proceso ocupa el puerto. Identificarlo en el Administrador de Tareas y cerrarlo antes de reiniciar Squid.

---

## Configuración del Navegador

Para que el tráfico web pase por Squid, el navegador debe estar configurado para usar el proxy local.

**Firefox:**

1. Abrir Preferencias → General → (desplazar al final) → Configuración de red → Configuración...
2. Seleccionar "Configuración manual del proxy"
3. En "Proxy HTTP" escribir: `127.0.0.1`, Puerto: `3128`
4. Marcar la casilla "Usar este proxy también para HTTPS"
5. Hacer clic en Aceptar

**Verificar que funciona:**

```
# En CMD — probar una petición a través del proxy
curl -x http://127.0.0.1:3128 http://example.com
```

La respuesta debe ser el HTML de `example.com`. Si aparece un error de Squid, el servicio no está corriendo o hay un problema de configuración.

---

## Estructura Básica de squid.conf

El archivo `squid.conf` controla todo el comportamiento de Squid. A continuación, la configuración mínima funcional con comentarios en español:

```squid
# Puerto en que escucha Squid (3128 es el estándar)
http_port 3128

# ACL que define la red local del laboratorio
acl red_local src 127.0.0.1/32

# Permitir acceso desde la red local
http_access allow red_local

# Denegar todo lo demás (política de seguridad por defecto)
http_access deny all
```

**Lógica de evaluación de ACLs:** Las reglas se evalúan en orden, de arriba a abajo. La primera regla que coincide con la petición determina la acción. La línea `http_access deny all` al final es la "red de seguridad": cualquier petición que no coincidió con ninguna regla anterior es denegada. Esta es la postura correcta por defecto.

!!! warning "Reiniciar Squid después de cada cambio"
    Cada vez que se modifica `squid.conf`, Squid debe reiniciarse para aplicar los cambios.
    En `services.msc`: clic derecho en "Squid Web Proxy Cache" → Reiniciar.
    O desde CMD como Administrador:
    ```
    net stop squid && net start squid
    ```

---

## Configuración de ACLs en squid.conf

Esta sección es el núcleo del laboratorio. Las ACLs definen las políticas de control de acceso.

### Ejercicio 1 — Bloquear dominios específicos

```squid
# ACL para dominios bloqueados
acl sitio_bloqueado dstdomain .facebook.com .instagram.com .twitter.com

# IMPORTANTE: la regla de denegación va ANTES del allow general
http_access deny sitio_bloqueado
http_access allow red_local
http_access deny all
```

Después de reiniciar Squid, intentar navegar a `facebook.com` con el proxy configurado. Squid debe mostrar una página de error indicando que el acceso fue denegado. La URL completa, la IP del cliente y la hora quedarán registradas en `access.log` con el código `TCP_DENIED`.

### Ejercicio 2 — Bloquear por palabras clave en la URL

```squid
# Bloquear URLs que contengan ciertas palabras clave
acl palabras_bloqueadas url_regex -i juegos gaming entretenimiento streaming

http_access deny palabras_bloqueadas
http_access allow red_local
http_access deny all
```

La opción `-i` hace la comparación sin distinguir mayúsculas de minúsculas. Esta ACL bloqueará cualquier URL que contenga "juegos", "gaming", "entretenimiento" o "streaming" en cualquier parte de la URL.

### Ejercicio 3 — Enfoque whitelist (solo permitir dominios autorizados)

```squid
# Solo permitir dominios explícitamente autorizados
acl sitios_permitidos dstdomain .microsoft.com .windows.com .google.com .github.com

# Denegar todo lo que NO esté en la lista de permitidos
http_access deny !sitios_permitidos
http_access allow red_local
http_access deny all
```

!!! warning "Whitelist: más seguro, más mantenimiento"
    El enfoque whitelist es el más seguro: por defecto todo está bloqueado, solo lo que el administrador autoriza explícitamente puede pasar. Es el enfoque correcto para redes militares con internet muy restringido. La desventaja es que requiere mantenimiento continuo: cada vez que un dominio legítimo nuevo es necesario (una actualización de software, un nuevo servicio), el administrador debe agregarlo a la lista.

---

## Lectura del access.log

El `access.log` de Squid registra cada petición HTTP procesada. Ejemplo de una línea real:

```
1718500200.123   142 127.0.0.1 TCP_MISS/200 4521 GET http://example.com/ - DIRECT/93.184.216.34 text/html
```

| Campo | Valor en el ejemplo | Descripción |
|-------|--------------------|----|
| Timestamp Unix | `1718500200.123` | Fecha y hora de la petición en formato Unix epoch |
| Tiempo de respuesta | `142` | Tiempo en milisegundos que tardó Squid en procesar la petición |
| IP del cliente | `127.0.0.1` | Equipo que hizo la petición |
| Código Squid/HTTP | `TCP_MISS/200` | TCP_MISS = no estaba en caché; 200 = servidor respondió OK |
| Bytes transferidos | `4521` | Tamaño de la respuesta en bytes |
| Método HTTP | `GET` | Tipo de petición (GET, POST, CONNECT...) |
| URL | `http://example.com/` | URL completa solicitada |
| Usuario | `-` | Guion = acceso anónimo (sin autenticación configurada) |
| Tipo de conexión | `DIRECT` | Squid fue directamente al servidor (no a otro proxy) |
| IP del servidor destino | `93.184.216.34` | IP real del servidor que respondió |
| Tipo de contenido | `text/html` | Tipo MIME de la respuesta |

**Códigos Squid más comunes:**

| Código | Significado |
|--------|------------|
| `TCP_MISS` | Petición enviada al servidor (no en caché) |
| `TCP_HIT` | Petición servida desde la caché de Squid |
| `TCP_DENIED` | Petición bloqueada por una ACL |
| `TCP_TUNNEL` | Conexión HTTPS tunel (CONNECT method) |

**Leer el log en tiempo real desde PowerShell:**

```powershell
# Ver nuevas líneas del access.log conforme llegan
Get-Content -Wait "C:\Squid\var\log\squid\access.log"
```

---

## Demostración de Bypass

**Objetivo:** Verificar empíricamente que el proxy sin firewall puede ser bypasseado.

1. Con el proxy configurado en el navegador y `facebook.com` bloqueado por ACL, intentar acceder — debe mostrar error de Squid.
2. Desactivar el proxy en la configuración del navegador (Firefox: Configuración de red → Sin proxy).
3. Intentar acceder a `facebook.com` de nuevo — ahora funciona, porque el tráfico va directo sin pasar por Squid.

**Conclusión:** El proxy solo controla el tráfico que pasa **a través de él**. Para que sea efectivo, debe combinarse con una regla de firewall que bloquee el tráfico HTTP/HTTPS saliente que no provenga del proceso Squid. En producción en Windows, esto se configura en el Firewall de Windows con Seguridad Avanzada (WFAS) bloqueando el tráfico TCP saliente en puertos 80 y 443 para todos los procesos excepto `squid.exe`.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Un Técnico detecta en el `access.log` que un equipo de la unidad (IP 192.168.1.47) realizó 200 peticiones en una hora a un dominio que contiene "upload" y "cloud" en la URL — patrón de posible exfiltración de datos. El método HTTP es `POST` y los bytes transferidos acumulan más de 50 MB. El Técnico reporta inmediatamente al oficial de seguridad con el extracto del log como evidencia y bloquea el dominio en `squid.conf` de forma preventiva. La investigación posterior confirma que el equipo tenía un troyano de exfiltración. Sin el proxy, esta actividad hubiera sido invisible: el firewall de capa 3 solo registra IP origen, IP destino y puerto — sin URL, sin método HTTP, sin tamaño de datos.

---

## Validación del laboratorio

Al terminar las prácticas, verificar que se cumple cada punto:

- [ ] El servicio "Squid Web Proxy Cache" aparece como "En ejecución" en `services.msc`
- [ ] El navegador configurado con el proxy puede cargar `http://example.com` correctamente
- [ ] El acceso a un dominio bloqueado (Ejercicio 1) muestra la página de error de Squid, no un error del navegador
- [ ] El `access.log` muestra líneas con `TCP_MISS` para peticiones exitosas y `TCP_DENIED` para las bloqueadas
- [ ] El mismo dominio bloqueado es accesible si se desactiva el proxy en el navegador (confirmando la necesidad del firewall adicional)

---

## Resumen

- Squid se instala en Windows 11 como servicio usando el instalador de `squid.diladele.com` y escucha en el puerto 3128
- La configuración de ACLs en `squid.conf` permite bloquear por dominio, palabras clave en URL o mediante whitelist; las reglas se evalúan en orden y la primera coincidencia gana
- El `access.log` registra: timestamp, tiempo de respuesta, IP cliente, código de resultado, bytes, método, URL completa y tipo de contenido — más detalle que cualquier firewall de capa 3/4
- El proxy sin firewall complementario es bypasseable: el control completo requiere ambas capas
- En entornos militares con internet por satélite, la caché de Squid reduce costes y el log proporciona visibilidad de auditoría

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- Documentación oficial Squid para Windows: `http://squid.diladele.com/` — guías de instalación y configuración avanzada
- Referencia completa de `squid.conf`: `http://www.squid-cache.org/Doc/config/` — todos los parámetros con descripción

<!-- Solución disponible para el instructor en: docs/instructor/lab-squid-solucion.md -->

---

*Siguiente: [IDS e IPS (Parte 1)](ids-ips-p1.md)*
