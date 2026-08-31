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

## Parte 4 — Verificar la red interna

### Paso 11 — Registrar las direcciones

En Ubuntu:

```bash
ip -brief address
```

En Windows 11, abrir PowerShell:

```powershell
ipconfig
```

Identificar las direcciones que pertenecen a la misma red interna o *host-only*.

**Registrar E4:** IP interna de Ubuntu, IP interna de Windows y prefijo de red.

### Paso 12 — Comprobar SMB en Windows

Esta parte presupone que el instructor habilitó previamente un recurso SMB 2/3 de laboratorio en Windows 11.

En PowerShell como administrador:

```powershell
Get-Service LanmanServer
Get-SmbServerConfiguration |
  Select-Object EnableSMB1Protocol, EnableSMB2Protocol
Get-NetTCPConnection -LocalPort 445 -State Listen
```

Criterios esperados:

- `LanmanServer` está en ejecución;
- `EnableSMB1Protocol` es `False`;
- `EnableSMB2Protocol` es `True`;
- existe un listener TCP/445.

No cambiar estas opciones durante el ejercicio. Si no se cumplen, detenerse y consultar al instructor.

### Paso 13 — Probar únicamente la IP autorizada

Desde Ubuntu, sustituir `<IP_WINDOWS>` por la dirección registrada en E4:

```bash
nc -vz -w 3 <IP_WINDOWS> 445
```

Ejemplo:

```bash
nc -vz -w 3 192.168.56.102 445
```

Un mensaje `succeeded` confirma que Ubuntu alcanza el servicio. `Connection refused` indica que la VM responde, pero no acepta el puerto. Un *timeout* puede indicar IP incorrecta, perfil de red, regla de firewall o falta de conectividad.

!!! danger "Prueba dirigida, no escaneo"
    Comprobar una IP y un puerto previamente autorizados no equivale a explorar toda la red. No utilizar rangos, comodines, descubrimiento masivo ni módulos de Metasploit en esta práctica.

**Registrar E5:** comando exacto, resultado e interpretación en una oración.

---

## Parte 5 — Cerrar y aislar el entorno

### Paso 14 — Retirar el instalador descargado

```bash
rm -f ./msfinstall
```

Este comando elimina únicamente la copia local del script; Metasploit permanece instalado mediante APT.

### Paso 15 — Desconectar el adaptador NAT

1. Apagar las dos VMs.
2. En el hipervisor, desactivar o retirar el adaptador NAT de Ubuntu.
3. Conservar únicamente la red interna o *host-only* en ambas VMs.
4. Encenderlas nuevamente.

En Ubuntu:

```bash
ip -brief address
ip route
```

La interfaz interna debe conservar su dirección. La ruta predeterminada asociada al adaptador NAT ya no debe aparecer.

Repetir la comprobación dirigida:

```bash
nc -vz -w 3 <IP_WINDOWS> 445
```

El criterio final es que las VMs puedan comunicarse dentro de la red del laboratorio, pero Ubuntu ya no dependa de Internet para continuar la siguiente práctica.

---

## Problemas frecuentes

| Problema | Comprobación | Acción segura |
|----------|-------------|---------------|
| `msfconsole: command not found` | `ls /opt/metasploit-framework/bin/msfconsole` | Ejecutar la ruta completa y abrir una terminal nueva |
| `db_status` indica desconexión | `msfdb status` | Salir de la consola y ejecutar `msfdb init` como usuario normal |
| APT no descarga el paquete | Fecha, DNS y adaptador NAT | Corregir conectividad; no editar manualmente la etiqueta del repositorio |
| TCP/445 devuelve `refused` | `Get-Service LanmanServer` y listener en Windows | Revisar el servicio y la configuración preparada por el instructor |
| TCP/445 termina en *timeout* | IP, perfil privado y regla de Windows Firewall | Verificar la red interna y limitar la regla a la IP de Ubuntu |

## Entrega — 20 puntos

| Criterio | Puntos |
|----------|-------:|
| E1: sistema y arquitectura correctamente identificados | 3 |
| E2: paquete y consola instalados desde la fuente oficial | 4 |
| E3: PostgreSQL conectado y *workspace* creado | 5 |
| E4: topología e IP internas correctamente registradas | 3 |
| E5: prueba dirigida interpretada correctamente | 3 |
| Aislamiento final y respuestas de reflexión | 2 |
| **Total** | **20** |

## Preguntas de reflexión

1. ¿Qué diferencia existe entre Metasploit Framework y Metasploitable?
2. ¿Por qué se inspecciona `msfinstall` antes de ejecutarlo con privilegios?
3. ¿Qué demuestra `db_status` y qué no demuestra?
4. ¿Por qué se retira el adaptador NAT después de instalar las herramientas?
5. ¿Por qué comprobar una IP y un puerto autorizados es diferente de explorar una subred completa?

---

*Anterior: [Honeypots — Parte 2](honeypots-p2.md) | Siguiente: [Examen Final](examen-final.md)*
