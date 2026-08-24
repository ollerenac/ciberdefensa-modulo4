---
# DOCUMENTO INSTRUCTOR — NO COMPARTIR CON ALUMNOS
# Tipo: Solución de laboratorio
---

# SOLUCIÓN: Lab IDS con Snort en Windows (LAB-04)

!!! info "Nota histórica sobre el nombre"
    El archivo conserva el nombre `lab-suricata-solucion.md` por razones históricas. El laboratorio utiliza **Snort 2.9.20 x64**, no Suricata.

!!! danger "Solo para instructores"
    Este documento contiene configuraciones, reglas completas, alertas esperadas, diagnóstico y rúbrica. No publicarlo ni proyectarlo durante el trabajo autónomo del alumno.

!!! warning "Alcance tecnológico"
    Snort 2 es una rama heredada. Cisco todavía publica 2.9.20, pero no certifica Windows 11 como plataforma verificada. La formulación correcta es: “laboratorio validado por el curso en una VM Windows 11 x64”. No presentarlo como arquitectura recomendada para un despliegue nuevo.

---

## Estado de validación en la VM de referencia

| Componente | Evidencia | Estado al 24-08-2026 |
|------------|-----------|----------------------|
| Snort | `snort.exe -V` muestra `2.9.20-WIN64` | Validado |
| Npcap | `snort.exe -W` enumera Host-only, NAT y `NPF_Loopback` | Validado |
| Configuración | `snort.exe -T -c ...snort-lab.conf` lee 3 reglas | Validado |
| ICMP | Alerta SID `1000010` sobre `127.0.0.1` | UAT validada |
| TCP/23 | Alerta SID `1000011` mediante `Test-NetConnection` | UAT validada |
| HTTP saliente | SID `1000012` sobre adaptador NAT | Pendiente de UAT final |
| `threshold` | Una alerta para diez Echo Request | Pendiente de UAT final |

No declarar el laboratorio completamente validado hasta cerrar las dos últimas filas en la VM.

---

## Prerrequisitos — Preparar antes de la clase

### 1. Resolver la licencia de Npcap

Npcap Free permite normalmente hasta cinco instalaciones cuando se usa con Snort y no autoriza redistribuir libremente el instalador. Si el aula supera ese número, gestionar autorización o licencia institucional antes de clonar las VMs. No incorporar el instalador de Npcap a una ISO o USB de distribución sin revisar esos términos.

### 2. Confirmar Windows x64 y privilegios

```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object OSArchitecture
```

Usar una cuenta que pueda elevar mediante UAC. La salida debe indicar `64-bit`.

### 3. Instalar Visual C++ Redistributable x64

Descargar [`VC_redist.x64.exe`](https://aka.ms/vc14/vc_redist.x64.exe) directamente desde Microsoft e instalarlo antes de Snort. No usar solo la edición x86: `Snort_2_9_20_Installer.x64.exe` es un binario x64 e importa `VCRUNTIME140.dll`.

Si `snort.exe -V` ya funciona, el runtime está satisfecho y no debe reinstalarse para resolver errores de reglas, interfaces o configuración.

### 4. Instalar Npcap

1. Descargar Npcap 1.88 —versión de la VM validada— desde [npcap.com](https://npcap.com/#download).
2. Ejecutarlo como administrador.
3. Confirmar **Install Npcap in WinPcap API-compatible Mode**.
4. No instalar el WinPcap antiguo en paralelo.
5. Mantener `NPF_Loopback`; reemplaza al antiguo adaptador de loopback.

### 5. Instalar Snort 2.9.20 x64

Descargar `Snort_2_9_20_Installer.x64.exe` desde la [página oficial](https://www.snort.org/downloads/) e instalar en `C:\Snort\`.

Los instaladores de Snort y Npcap son ejecutables, no archivos MSI.

### 6. Verificar por capas

```powershell
C:\Snort\bin\snort.exe -V
C:\Snort\bin\snort.exe -W
```

No continuar hasta que:

- `-V` muestre `Version 2.9.20-WIN64`.
- `-W` enumere el adaptador NAT y `Adapter for loopback traffic capture`.

En la VM de referencia:

| Índice observado | IP | Uso |
|------------------|----|-----|
| 1 | `192.168.56.102` | Host-only |
| 2 | `10.0.2.15` | NAT/HTTP saliente |
| 3 | `127.0.0.1` mediante `NPF_Loopback` | ICMP y TCP locales |

Los índices pueden cambiar después de modificar la VM. Redescubrirlos antes de cada clase.

---

## snort.conf Mínimo Funcional

No utilizar el `C:\Snort\etc\snort.conf` completo para LAB-04. La copia incluida con el instalador puede conservar la directiva dinámica comentada con una ruta Unix:

```text
#dynamicpreprocessor directory /usr/local/lib/snort_dynamicpreprocessor/
```

Aunque `sf_ftptelnet.dll` exista en Windows, Snort no la carga y termina en `Unknown preprocessor: "ftp_telnet"`. No solucionar esto eliminando preprocesadores uno por uno: el laboratorio no necesita esa plantilla.

Crear `C:\Snort\etc\snort-lab.conf`:

```powershell
@'
# Configuración mínima para LAB-04

ipvar HOME_NET [127.0.0.1/32,10.0.2.0/24,192.168.56.0/24]
ipvar EXTERNAL_NET any

var RULE_PATH C:\Snort\rules

include $RULE_PATH\mis-reglas.rules
'@ | Set-Content -Path C:\Snort\etc\snort-lab.conf -Encoding ascii
```

Crear el archivo de reglas:

```powershell
if (-not (Test-Path C:\Snort\rules\mis-reglas.rules)) {
    New-Item -Path C:\Snort\rules\mis-reglas.rules -ItemType File
}
```

No usar `New-Item -Force` sobre un archivo existente: podría destruir evidencia o reglas de una ejecución anterior.

### Decisión sobre Community Rules

No cargar `community.rules` durante el camino principal. Algunas reglas comunitarias requieren variables y preprocesadores ausentes en la configuración mínima; agregarlas impediría atribuir un error a una sola causa.

Las reglas locales bastan para los objetivos y la rúbrica. El paquete comunitario puede utilizarse posteriormente en una demostración preparada, con versión congelada y configuración validada por el instructor.

---

## Reglas Snort Esperadas

Guardar cada regla en una sola línea dentro de `C:\Snort\rules\mis-reglas.rules`.

### Regla 1 — ICMP inicial

```snort
alert icmp any any -> $HOME_NET any (msg:"Ping ICMP detectado"; itype:8; sid:1000010; rev:1;)
```

### Regla 2 — Intento TCP hacia Telnet

```snort
alert tcp any any -> $HOME_NET 23 (msg:"Intento de conexion Telnet inseguro"; sid:1000011; rev:1;)
```

### Regla 3 — Contenido HTTP saliente

```snort
alert tcp $HOME_NET any -> $EXTERNAL_NET 80 (msg:"HTTP saliente con cadena sospechosa cmd.exe"; content:"cmd.exe"; nocase; sid:1000012; rev:1;)
```

La regla HTTP original estaba invertida: `any -> $HOME_NET:80` describe tráfico entrante, mientras que `curl.exe` genera una petición saliente desde la VM. No añadir `http_uri` a esta configuración mínima; esa opción presupone el preprocesamiento HTTP que deliberadamente no estamos cargando.

### Regla 1 final con control de volumen

```snort
alert icmp any any -> $HOME_NET any (msg:"Ping ICMP detectado"; itype:8; threshold: type limit, track by_src, count 1, seconds 60; sid:1000010; rev:2;)
```

Mostrarla en una sola línea. Un bloque multilínea sin continuaciones no es una regla lista para copiar a Snort.

---

## Validación de configuración

```powershell
C:\Snort\bin\snort.exe -T -c C:\Snort\etc\snort-lab.conf
```

Criterios:

```text
3 Snort rules read
3 detection rules
Snort successfully validated the configuration!
```

Si el error menciona un SID, revisar `mis-reglas.rules`. Si menciona `ftp_telnet`, el alumno ejecutó el archivo equivocado.

---

## UAT 1 — ICMP sobre loopback

Usar el índice real de `NPF_Loopback`:

```powershell
C:\Snort\bin\snort.exe -i <LOOPBACK> -A console -N -c C:\Snort\etc\snort-lab.conf
```

`-N` desactiva el almacenamiento de paquetes y mantiene las alertas. Sin esta opción, el comando anterior del curso intentaba crear `log/snort.log...` en una ruta relativa y podía terminar con error fatal.

Desde otra PowerShell:

```powershell
ping 127.0.0.1
```

Salida validada:

```text
[1:1000010:1] Ping ICMP detectado ... {ICMP} 127.0.0.1 -> 127.0.0.1
```

El aviso `No preprocessors configured for policy 0` es esperado en esta configuración mínima y no invalida las reglas simples a nivel de paquete.

---

## UAT 2 — TCP/23 sin instalar Telnet Client

Con Snort todavía en loopback:

```powershell
Test-NetConnection -ComputerName 127.0.0.1 -Port 23
```

Salida validada en Snort:

```text
[1:1000011:1] Intento de conexion Telnet inseguro ... 127.0.0.1:puerto -> 127.0.0.1:23
```

Un `TcpTestSucceeded: False` es aceptable. Las alertas repetidas con el mismo puerto de origen son retransmisiones TCP. La herramienta puede efectuar además una comprobación ICMP y activar la Regla 1.

Esta prueba reemplaza al cliente Telnet opcional de Windows.

---

## UAT 3 — HTTP saliente sobre NAT

Detener Snort con `Ctrl+C` y reiniciarlo sobre el índice NAT:

```powershell
C:\Snort\bin\snort.exe -i <NAT> -A console -N -c C:\Snort\etc\snort-lab.conf
```

En otra PowerShell:

```powershell
curl.exe --noproxy "*" --http1.1 --max-time 10 http://example.com/cmd.exe
```

Alerta esperada:

```text
[1:1000012:1] HTTP saliente con cadena sospechosa cmd.exe ... 10.0.2.15:puerto -> IP_EXTERNA:80
```

El código HTTP puede ser 404. El criterio es que la petición sin cifrar contenga `/cmd.exe` y la alerta aparezca sobre el adaptador de salida.

---

## UAT 4 — Threshold

Sustituir la Regla 1 por la revisión 2, validar nuevamente con `-T`, iniciar sobre loopback y ejecutar:

```powershell
ping -n 10 127.0.0.1
```

Esperado: diez Echo Request procesados y como máximo una alerta SID `1000010` para `127.0.0.1` durante 60 segundos.

---

## Diagnóstico de errores observados

| Error o síntoma | Causa comprobada o probable | Acción correcta |
|-----------------|-----------------------------|-----------------|
| `snort.exe -V` no inicia y menciona `VCRUNTIME140.dll` | Falta runtime x64 | Instalar `VC_redist.x64.exe` desde Microsoft |
| `snort.exe -W` no enumera adaptadores | Npcap ausente o sin compatibilidad WinPcap | Revisar instalación de Npcap, no las reglas |
| `Unknown preprocessor: "ftp_telnet"` | Se usó el `snort.conf` completo sin cargar DLL dinámicas | Usar `snort-lab.conf`; no borrar preprocesadores uno por uno |
| `Failed to open log file "log/snort.log..."` | El comando no usó `-N` ni una ruta absoluta de log | Añadir `-N` para alertas solo en consola |
| `snort -T` lee cero reglas | Falta el `include` o se editó otro archivo | Verificar ruta y `mis-reglas.rules` |
| ICMP/TCP no alerta | Snort captura Ethernet/NAT, pero la prueba circula por loopback | Seleccionar el índice `NPF_Loopback` |
| HTTP no alerta | Interfaz incorrecta, regla invertida, proxy o HTTPS | Usar NAT, regla saliente y HTTP directo |
| Muchas alertas TCP/23 | Retransmisiones del mismo SYN | Explicarlo; no confundir con ejecuciones independientes |

---

## Notas de dictado

| Bloque | Tiempo | Evidencia para avanzar |
|--------|--------|------------------------|
| Prerrequisitos y `-V` | 20 min | Versión 2.9.20-WIN64 |
| Npcap e interfaces | 10 min | NAT y `NPF_Loopback` enumerados |
| Configuración mínima y `-T` | 15 min | Tres reglas validadas |
| Escritura y anatomía de reglas | 25 min | SIDs únicos y direcciones justificadas |
| ICMP y TCP loopback | 20 min | Alertas 1000010 y 1000011 |
| HTTP sobre NAT | 15 min | Alerta 1000012 |
| `threshold` y análisis | 15 min | Una alerta para diez pings |

La instalación se realiza en paralelo, pero ningún alumno avanza de `-V` a `-W`, ni de `-W` a `-T`, sin mostrar el checkpoint anterior.

---

## Rúbrica de calificación

| Criterio | Puntos | Cómo verificar |
|----------|--------|----------------|
| `snort-lab.conf` valida tres reglas | 2 | Salida `Snort successfully validated the configuration!` |
| Regla ICMP funciona sobre loopback | 2 | Regla y alerta SID 1000010 |
| Regla TCP funciona sin Telnet Client | 2 | Regla y alerta SID 1000011 |
| Regla HTTP saliente funciona sobre NAT | 2 | Regla y alerta SID 1000012 |
| `threshold` limita el volumen | 1 | Revisión 2 y resultado de diez pings |
| El alumno interpreta una alerta | 1 | Explica timestamp, SID, protocolo, origen y destino |

**Total: 10 puntos**

---

## Checklist previo al dictado

- [ ] Licencia o autorización de Npcap resuelta para el número de VMs
- [ ] `VC_redist.x64.exe`, Npcap y Snort obtenidos desde fuentes oficiales
- [ ] Snapshot de VM tomado después de instalar el driver con la VM apagada
- [ ] Índices NAT y loopback redescubiertos
- [ ] `snort-lab.conf` y tres reglas validados
- [ ] UAT ICMP, TCP, HTTP y `threshold` ejecutada de principio a fin
- [ ] Ningún Community Ruleset forma parte del camino crítico

<!-- Solución instructor para: docs/seguridad-criptografia/en-la-red/ids-ips-p2.md -->
