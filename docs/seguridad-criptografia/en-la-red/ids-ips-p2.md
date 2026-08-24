---
# Horas asignadas: 3 hrs
# Tipo: Teoría + Laboratorio (versión alumno)
---

# IDS con Snort en Windows: Configuración y Práctica (Parte 2)

> **Duración:** 3 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red | **Continuación de Parte 1 — incluye laboratorio integrado**

## Recap de la Parte 1

En la Parte 1 aprendimos la diferencia fundamental entre IDS (detecta y alerta) e IPS (detecta y bloquea), distinguimos NIDS de HIDS, y explicamos los dos métodos de detección: por firma (preciso pero ciego ante zero-days) y por anomalía (detecta lo desconocido pero con más falsos positivos). También introdujimos los conceptos de true positive, false positive, true negative y false negative. En esta sesión instalaremos Snort 2.9.20 en Windows 11, escribiremos reglas propias y ejecutaremos el IDS en modo pasivo para generar y leer alertas reales.

---

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Preparar una VM Windows 11 x64 con Visual C++ Redistributable, Npcap y Snort 2.9.20
- Verificar por separado el ejecutable, el controlador de captura y la configuración
- Leer y escribir reglas Snort básicas explicando cada campo
- Ejecutar Snort con alertas en consola sobre la interfaz correcta
- Generar tráfico ICMP, TCP y HTTP que active reglas reproducibles
- Aplicar `threshold` para controlar el volumen de alertas

!!! warning "Herramienta heredada para una práctica controlada"
    Snort 2.9.20 sigue disponible oficialmente, pero Snort 2 es una rama heredada. Este laboratorio ha sido validado por el curso en una VM Windows 11 x64; no debe interpretarse como una recomendación para un despliegue nuevo de producción.

---

## Anatomía de una Regla Snort

Una regla Snort tiene una cabecera —acción, protocolo, direcciones y puertos— y un conjunto de opciones entre paréntesis:

```snort
alert tcp any any -> $HOME_NET 22 (msg:"Intento de conexion SSH"; sid:1000001; rev:1;)
```

| Campo | Valor en el ejemplo | Significado |
|-------|--------------------|-------------|
| Acción | `alert` | Genera una alerta cuando el paquete coincide |
| Protocolo | `tcp` | Protocolo que se inspecciona: `tcp`, `udp`, `icmp` o `ip` |
| IP origen | `any` | Dirección o red de origen; `any` significa cualquiera |
| Puerto origen | `any` | Puerto de origen |
| Dirección | `->` | Flujo unidireccional desde el origen hacia el destino |
| IP destino | `$HOME_NET` | Variable que representa la red protegida |
| Puerto destino | `22` | Puerto que se vigila; 22 corresponde a SSH |
| `msg` | `Intento de conexion SSH` | Mensaje que aparecerá en la alerta |
| `sid` | `1000001` | Identificador único; las reglas locales usan valores desde 1 000 000 |
| `rev` | `1` | Revisión de la regla; se incrementa cuando cambia |

!!! note "Dirección de la regla"
    `any any -> $HOME_NET 22` describe tráfico que **entra** a la red protegida por el puerto 22. Para vigilar una petición web que **sale** desde la VM, la dirección debe comenzar con `$HOME_NET any -> ... 80`. La dirección debe coincidir con el tráfico que realmente se genera.

---

## Laboratorio: preparar Snort en la VM Windows

### Topología usada

La VM Windows del curso tiene dos adaptadores y la interfaz loopback de Npcap:

| Uso | Dirección observada en la VM de referencia | Tráfico del laboratorio |
|-----|--------------------------------------------|--------------------------|
| Host-only | `192.168.56.102` | Comunicación con otras VMs |
| NAT | `10.0.2.15` | Petición HTTP hacia Internet |
| Loopback | `127.0.0.1` | Pruebas ICMP y TCP locales |

Los números de interfaz no son universales. Se deben descubrir con `snort.exe -W`; en la VM validada fueron NAT = 2 y loopback = 3.

### Paso 1 — Confirmar que Windows es de 64 bits

Abrir **PowerShell como administrador** y ejecutar:

```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object OSArchitecture
```

La salida debe indicar `64-bit`. Los instaladores utilizados en esta práctica son x64.

### Paso 2 — Instalar Visual C++ Redistributable x64

Descargar desde Microsoft el paquete vigente [`VC_redist.x64.exe`](https://aka.ms/vc14/vc_redist.x64.exe), ejecutarlo como administrador y aceptar el reinicio si lo solicita.

!!! note "Cómo reconocer si ya estaba instalado"
    Si `C:\Snort\bin\snort.exe -V` muestra la versión de Snort, Windows ya dispone del runtime necesario. Un mensaje que menciona `VCRUNTIME140.dll` ausente sí indica que falta este prerrequisito. No descargar DLL individuales desde sitios de terceros.

### Paso 3 — Instalar Npcap

Para reproducir la VM validada, descargar **Npcap 1.88** desde [npcap.com](https://npcap.com/#download) y ejecutar el instalador como administrador.

Durante la instalación:

- Confirmar **Install Npcap in WinPcap API-compatible Mode**.
- No instalar el antiguo WinPcap en paralelo.
- Si se activa la opción que restringe Npcap a administradores, todas las capturas deberán ejecutarse desde una consola elevada.

!!! danger "Licencia para un aula"
    Npcap Free limita normalmente a cinco instalaciones cuando se utiliza con aplicaciones como Snort y no permite redistribuir libremente su instalador. Un aula que supere ese número necesita autorización o una licencia institucional aplicable. El instructor debe resolverlo antes de desplegar las VMs.

### Paso 4 — Instalar Snort 2.9.20 x64

Descargar `Snort_2_9_20_Installer.x64.exe` desde la [página oficial de Snort 2](https://www.snort.org/downloads/) e instalarlo en la ruta predeterminada `C:\Snort\`.

Verificar primero el ejecutable, sin cargar ninguna configuración:

```powershell
C:\Snort\bin\snort.exe -V
```

Debe aparecer `Version 2.9.20-WIN64`. Si este comando falla, no continuar con reglas ni interfaces.

### Paso 5 — Verificar Npcap y descubrir las interfaces

```powershell
C:\Snort\bin\snort.exe -W
```

La salida debe incluir:

- El adaptador NAT con la IP `10.0.2.15` o la IP NAT asignada a la VM.
- `Adapter for loopback traffic capture`, con el dispositivo `\Device\NPF_Loopback`.

Anotar ambos índices. Si no aparece ninguna interfaz, revisar Npcap antes de cambiar `snort.conf`.

---

## Crear una configuración mínima y reproducible

El `C:\Snort\etc\snort.conf` incluido con el instalador es una plantilla extensa. En la distribución Windows puede conservar rutas de preprocesadores orientadas a Unix; editar solo `HOME_NET` y los `include` no la convierte en una configuración funcional para esta práctica.

No eliminar bloques uno por uno. Crear un archivo independiente:

```powershell
@'
# Configuración mínima para LAB-04

ipvar HOME_NET [127.0.0.1/32,10.0.2.0/24,192.168.56.0/24]
ipvar EXTERNAL_NET any

var RULE_PATH C:\Snort\rules

include $RULE_PATH\mis-reglas.rules
'@ | Set-Content -Path C:\Snort\etc\snort-lab.conf -Encoding ascii
```

!!! note "Ajustar HOME_NET"
    La lista anterior corresponde a la VM validada. Si `ipconfig` muestra otras redes, conservar `127.0.0.1/32` y sustituir `10.0.2.0/24` o `192.168.56.0/24` por las subredes reales.

Crear el archivo de reglas locales:

```powershell
if (-not (Test-Path C:\Snort\rules\mis-reglas.rules)) {
    New-Item -Path C:\Snort\rules\mis-reglas.rules -ItemType File
}
notepad C:\Snort\rules\mis-reglas.rules
```

La condición evita borrar reglas existentes al repetir el laboratorio.

### ¿Por qué no cargamos Community Rules todavía?

Las tres reglas del laboratorio son locales y no necesitan el paquete comunitario. Algunas Community Rules dependen de variables y preprocesadores que no forman parte de esta configuración mínima. Mezclarlas durante la instalación hace más difícil distinguir un error de Snort de un error del paquete de reglas.

Las Community Rules quedan como ampliación posterior, no como criterio de aprobación del laboratorio.

---

## Escribir las tres reglas

Guardar las tres reglas siguientes, una por línea, en `C:\Snort\rules\mis-reglas.rules`.

### Regla 1 — Echo Request ICMP recibido por HOME_NET

```snort
alert icmp any any -> $HOME_NET any (msg:"Ping ICMP detectado"; itype:8; sid:1000010; rev:1;)
```

- `itype:8` selecciona el Echo Request enviado por `ping`.
- En loopback, origen y destino serán `127.0.0.1`.

### Regla 2 — Intento TCP hacia Telnet

```snort
alert tcp any any -> $HOME_NET 23 (msg:"Intento de conexion Telnet inseguro"; sid:1000011; rev:1;)
```

- Detecta los SYN dirigidos al puerto TCP 23.
- No es necesario que exista un servidor Telnet ni instalar Telnet Client.

### Regla 3 — Petición HTTP saliente que contiene `cmd.exe`

```snort
alert tcp $HOME_NET any -> $EXTERNAL_NET 80 (msg:"HTTP saliente con cadena sospechosa cmd.exe"; content:"cmd.exe"; nocase; sid:1000012; rev:1;)
```

- El origen es `$HOME_NET` porque `curl.exe` se ejecuta desde la VM.
- El destino usa el puerto 80 porque se inspeccionará HTTP sin cifrar.
- `content` busca la cadena dentro del paquete y `nocase` ignora mayúsculas y minúsculas.

!!! warning "HTTPS no sirve para esta prueba"
    Si la petición usa HTTPS, el contenido viaja cifrado y esta regla no podrá leer `cmd.exe`. El ejercicio usa explícitamente `http://`.

---

## Validar antes de capturar

```powershell
C:\Snort\bin\snort.exe -T -c C:\Snort\etc\snort-lab.conf
```

La salida debe indicar:

```text
3 Snort rules read
3 detection rules
Snort successfully validated the configuration!
```

Si aparece `Unknown preprocessor: "ftp_telnet"`, se está ejecutando por error el `snort.conf` completo. Revisar que el comando termine en `snort-lab.conf`.

---

## Prueba 1 — ICMP sobre loopback

En la primera PowerShell elevada, sustituir `<LOOPBACK>` por el índice obtenido con `-W`:

```powershell
C:\Snort\bin\snort.exe -i <LOOPBACK> -A console -N -c C:\Snort\etc\snort-lab.conf
```

La opción `-N` desactiva el almacenamiento de paquetes, pero conserva las alertas en consola. Evita que Snort intente escribir en un directorio relativo `log` durante esta práctica.

Cuando aparezca `Commencing packet processing`, abrir otra PowerShell:

```powershell
ping 127.0.0.1
```

Alerta esperada:

```text
[1:1000010:1] Ping ICMP detectado ... {ICMP} 127.0.0.1 -> 127.0.0.1
```

!!! note "Aviso esperado"
    La configuración mínima puede mostrar `WARNING: No preprocessors configured for policy 0.`. Para estas tres reglas locales, que trabajan con paquetes simples, el aviso no invalida la captura ni la alerta.

---

## Prueba 2 — Intento TCP hacia el puerto 23

Mantener Snort sobre la interfaz loopback y ejecutar en la segunda PowerShell:

```powershell
Test-NetConnection -ComputerName 127.0.0.1 -Port 23
```

`TcpTestSucceeded` puede ser `False`: la prueba busca el intento TCP, no un servicio Telnet funcionando. Snort debe mostrar al menos una alerta:

```text
[1:1000011:1] Intento de conexion Telnet inseguro ... 127.0.0.1:puerto -> 127.0.0.1:23
```

Puede haber varias alertas con el mismo puerto de origen debido a retransmisiones TCP. `Test-NetConnection` también puede realizar una comprobación ICMP y activar la Regla 1.

Detener Snort con `Ctrl+C` antes de cambiar de interfaz.

---

## Prueba 3 — Contenido HTTP saliente

Iniciar Snort sobre el índice del adaptador NAT obtenido con `-W`:

```powershell
C:\Snort\bin\snort.exe -i <NAT> -A console -N -c C:\Snort\etc\snort-lab.conf
```

En la segunda PowerShell ejecutar una petición HTTP directa:

```powershell
curl.exe --noproxy "*" --http1.1 --max-time 10 http://example.com/cmd.exe
```

El resultado HTTP puede ser `404`; eso no afecta la detección. Snort inspecciona la petición enviada y debe mostrar:

```text
[1:1000012:1] HTTP saliente con cadena sospechosa cmd.exe ... 10.0.2.15:puerto -> IP_EXTERNA:80
```

Si no hay alerta:

1. Confirmar que se eligió el adaptador cuya IP coincide con la ruta por defecto.
2. Confirmar que la URL empieza con `http://`, no `https://`.
3. Confirmar que la regla comienza con `$HOME_NET any -> $EXTERNAL_NET 80`.
4. Detener con `Ctrl+C` y revisar que las estadísticas indiquen paquetes recibidos.

---

## Controlar el volumen de alertas con `threshold`

La regla ICMP inicial genera una alerta por cada Echo Request. Sustituirla por esta versión de **una sola línea**:

```snort
alert icmp any any -> $HOME_NET any (msg:"Ping ICMP detectado"; itype:8; threshold: type limit, track by_src, count 1, seconds 60; sid:1000010; rev:2;)
```

| Parámetro | Significado |
|-----------|-------------|
| `type limit` | Limita cuántas alertas se publican |
| `track by_src` | Mantiene un contador independiente por IP de origen |
| `count 1` | Permite una alerta en la ventana |
| `seconds 60` | Define una ventana de 60 segundos |

Volver a validar con `-T`, reiniciar Snort sobre loopback y ejecutar:

```powershell
ping -n 10 127.0.0.1
```

Se capturan los diez Echo Request, pero se muestra como máximo una alerta para ese origen durante la ventana de 60 segundos.

---

## Diagnóstico por etapas

| Punto de control | Error observado | Interpretación y acción |
|------------------|-----------------|------------------------|
| `snort.exe -V` | Falta `VCRUNTIME140.dll` | Instalar `VC_redist.x64.exe` oficial |
| `snort.exe -W` | No aparecen interfaces | Revisar Npcap y el modo compatible con WinPcap |
| `snort.exe -T ...snort.conf` | `Unknown preprocessor: ftp_telnet` | Se usó la plantilla completa; cambiar a `snort-lab.conf` |
| Inicio de IDS sin `-N` | `Failed to open log file "log/snort.log..."` | Usar `-N` para el modo consola o configurar `-l` con una ruta absoluta |
| Captura loopback | No aparece ICMP/TCP | Elegir `NPF_Loopback` y conservar `127.0.0.1/32` en `HOME_NET` |
| Captura NAT | No aparece HTTP | Revisar interfaz, dirección de la regla y uso de HTTP sin cifrar |

No reinstalar todo ante un error de reglas. Cada comando verifica una capa distinta.

---

## Validación del laboratorio

- [ ] Windows informa arquitectura de 64 bits
- [ ] `snort.exe -V` muestra `2.9.20-WIN64`
- [ ] `snort.exe -W` enumera NAT y `NPF_Loopback`
- [ ] `snort.exe -T -c C:\Snort\etc\snort-lab.conf` valida exactamente tres reglas
- [ ] `ping 127.0.0.1` genera la alerta ICMP sobre loopback
- [ ] `Test-NetConnection 127.0.0.1 -Port 23` genera la alerta TCP
- [ ] `curl.exe` por HTTP genera la alerta de contenido sobre el adaptador NAT
- [ ] La Regla 1 con `threshold` limita las alertas a una por minuto y origen

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    El analista recibe una alerta `[1:1000012:1] HTTP saliente con cadena sospechosa cmd.exe` originada desde una dirección de la red protegida hacia un servidor web externo. La cadena no demuestra por sí sola un compromiso, pero justifica correlacionar el evento con el proxy, el inventario del equipo y los logs del endpoint antes de escalarlo. Snort aporta visibilidad del contenido HTTP que un firewall de capa 3/4 no examina.

---

## Resumen

- Preparar la VM en el orden: Visual C++ Redistributable x64, Npcap compatible con WinPcap y Snort 2.9.20 x64
- Verificar cada capa con `-V`, `-W` y `-T`
- Usar `snort-lab.conf`; no modificar la plantilla completa para este laboratorio
- Ejecutar con `-N` cuando solo se necesitan alertas en consola
- Capturar ICMP y TCP local en loopback, y HTTP saliente en el adaptador NAT
- Hacer coincidir la dirección de cada regla con el tráfico generado
- Aplicar `threshold` para reducir alertas repetidas sin dejar de inspeccionar paquetes

<!-- Solución disponible para el instructor en: docs/instructor/lab-suricata-solucion.md -->

---

*Siguiente: [Honey Pots (Parte 1)](honeypots-p1.md)*
