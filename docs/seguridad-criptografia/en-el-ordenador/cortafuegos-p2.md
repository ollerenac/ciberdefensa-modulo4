---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# Cortafuegos: Reglas Avanzadas y Logging (Parte 2)

> **Duración:** 2 horas | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En el Ordenador | Continuación de Parte 1

En la Parte 1 establecimos los fundamentos: la diferencia entre host firewall y firewall perimetral, el filtrado stateful vs. stateless, los tres perfiles de red (Domain, Private, Public), la lógica de reglas de entrada vs. salida, y cómo navegar la consola WFAS (`wf.msc`). En esta Parte 2 creamos reglas avanzadas por aplicación, puerto y rango IP; habilitamos y leemos el log del firewall; y entendemos las limitaciones del host firewall frente a soluciones UTM corporativas.

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Crear reglas avanzadas en WFAS que filtren por aplicación específica, puerto y rango de IP.
- Habilitar el logging del firewall y leer una línea del log para extraer información de diagnóstico.
- Explicar las limitaciones del host firewall de Windows frente a un UTM corporativo.

---

## Reglas Avanzadas por Aplicación

Las reglas por puerto (como "bloquear TCP 23") aplican a todo el tráfico en ese puerto, independientemente de qué aplicación lo genere. Las reglas por aplicación van más allá: bloquean o permiten tráfico generado por un ejecutable específico, independientemente del puerto que use.

¿Por qué las reglas por aplicación son más robustas? Porque un malware puede cambiar el puerto que usa para comunicarse — pero el ejecutable del malware es siempre el mismo. Si la regla está basada en el ejecutable, el cambio de puerto no la evade.

**Cómo crear una regla que bloquee una aplicación del acceso a la red en WFAS:**

1. Abrir WFAS: `Win+R → wf.msc`
2. Panel izquierdo → **Outbound Rules** → Action (menú) → **New Rule...**
3. Tipo de regla: **Program** → Next
4. This program path → Browse → seleccionar el ejecutable (por ejemplo: `C:\Windows\System32\calc.exe` para la Calculadora como prueba)
5. Action: **Block the connection** → Next
6. Profile: seleccionar los perfiles donde aplica → Next
7. Name: "Bloquear [nombre de la aplicación] — tráfico saliente" → Finish

La regla aparece en la lista de Outbound Rules con Action=Block y el ejecutable identificado en la columna Program.

!!! note "Reglas de bloqueo tienen precedencia"
    En WFAS, si existe tanto una regla de bloqueo como una regla de permiso que aplican al mismo tráfico, la regla de bloqueo siempre gana. Esto es intencional — diseño de seguridad que prioriza el bloqueo ante cualquier ambigüedad.

---

## Reglas por Puerto y Protocolo

Las reglas por puerto son las más comunes y las más prácticas para bloquear protocolos inseguros conocidos o restringir el acceso a servicios específicos.

**Protocolos inseguros que deben bloquearse en redes militares:**

| Protocolo | Puerto | Por qué bloquearlo |
|-----------|--------|-------------------|
| **Telnet** | TCP 23 | Transmite credenciales en texto claro — cualquiera que monitoree la red puede ver usuario y contraseña. Reemplazado completamente por SSH (TCP 22). |
| **FTP (sin cifrado)** | TCP 21 | Igual que Telnet — credenciales y datos en texto claro. Reemplazado por SFTP o FTPS. |
| **RDP sin restricción de IP** | TCP 3389 | RDP es el protocolo de escritorio remoto — si está abierto desde cualquier IP, es un vector de ataque masivo para ataques de fuerza bruta. Siempre limitar por IP de origen. |

**Crear regla para bloquear Telnet (TCP 23):**

```cmd
netsh advfirewall firewall add rule name="Bloquear Telnet entrante" protocol=TCP dir=in localport=23 action=block
```

**Crear regla para permitir RDP solo desde una subred específica (via GUI):**

1. WFAS → Inbound Rules → New Rule → Port
2. Protocol: TCP, Specific local ports: 3389
3. Action: Allow the connection
4. Profile: Domain, Private
5. Name: "RDP solo desde red de unidad 192.168.1.0/24"
6. Tras crear la regla, hacer doble clic → pestaña **Scope** → Remote IP address → **These IP addresses** → Add: `192.168.1.0/24`

Con esta configuración, solo los equipos en el rango 192.168.1.0/24 pueden iniciar sesiones RDP. Un atacante desde fuera de ese rango recibe un bloqueo silencioso.

---

## Logging del Firewall

El log del firewall registra cada decisión que toma WFAS: qué tráfico se permitió y qué tráfico se bloqueó. Sin el log habilitado, el firewall actúa en silencio — no hay evidencia de intentos de conexión bloqueados.

**Habilitar el log:**

1. WFAS → Panel central → **Windows Defender Firewall with Advanced Security on Local Computer** → Properties (en el panel derecho o Action menu)
2. Seleccionar el perfil para el que activar el log (por ejemplo: **Public Profile** → pestaña Logging)
3. Log dropped packets: **Yes**
4. Log successful connections: **Yes**
5. Log file path: (por defecto `%SystemRoot%\System32\LogFiles\Firewall\pfirewall.log`) → se puede cambiar si se necesita
6. Maximum size (KB): **4096** (4 MB, suficiente para diagnóstico inicial)
7. OK

**Formato del log:**

El archivo `pfirewall.log` es texto plano. Cada línea tiene la siguiente estructura:

```
Fecha    Hora     Acción  Protocolo  IP-origen      IP-destino     Puerto-origen Puerto-destino Flags
2026-06-15 14:32:11 DROP TCP 10.0.0.55 192.168.1.100 54321 22 - - - - - - -
2026-06-15 14:33:05 ALLOW TCP 192.168.1.100 8.8.8.8 49152 443 - - - - - - -
```

Interpretando la primera línea:
- **2026-06-15 14:32:11** → Fecha y hora del evento
- **DROP** → El firewall bloqueó este tráfico
- **TCP** → Protocolo
- **10.0.0.55** → IP de origen (quien intentó conectarse)
- **192.168.1.100** → IP de destino (nuestro equipo)
- **54321** → Puerto de origen (en el equipo atacante)
- **22** → Puerto de destino en nuestro equipo (SSH — alguien intentó conectarse al servicio SSH)

Esta línea de log nos dice: el 15 de junio a las 14:32, alguien con IP 10.0.0.55 intentó conectarse al puerto SSH (22) de nuestro equipo (192.168.1.100) y fue bloqueado.

!!! tip "Filtrar el log para ver solo los DROP"
    El archivo de log puede crecer rápido. Para extraer solo las líneas de bloqueo en PowerShell:

    ```powershell
    # Mostrar solo las líneas DROP del log del firewall
    Get-Content "$env:SystemRoot\System32\LogFiles\Firewall\pfirewall.log" |
        Where-Object {$_ -match "DROP"}
    ```

    Esto filtra el ruido del tráfico permitido normal y muestra solo los intentos de conexión rechazados — útil para detectar escaneos de puertos o intentos de acceso no autorizado.

---

## Limitaciones del Host Firewall

Windows Defender Firewall es una herramienta poderosa para el hardening de estaciones de trabajo, pero tiene limitaciones fundamentales que es necesario conocer para entender dónde necesitamos herramientas adicionales.

**Qué no puede hacer WFAS:**

- **Inspección de contenido (DPI — Deep Packet Inspection):** WFAS filtra por IP, puerto y protocolo — no puede "abrir" los paquetes para ver su contenido. Si un malware usa el puerto 443 (HTTPS), WFAS lo permite igual que el navegador web. No puede distinguir entre tráfico HTTPS legítimo y tráfico C2 (Comando y Control) que usa el mismo puerto.

- **Análisis de malware en el tráfico permitido:** Si una regla permite el tráfico y ese tráfico contiene un exploit o payload malicioso, WFAS lo deja pasar — no es su función analizarlo. Para eso necesitamos el AV (Defender Antivirus) o un IDS/IPS.

- **Control por usuario:** Las reglas de WFAS aplican a procesos y puertos, no a usuarios individuales. No se puede decir "el usuario Juan puede usar FTP pero el usuario María no" con WFAS solo.

**Por qué un UTM corporativo es necesario además del host firewall:**

Una UTM (Unified Threat Management) es un appliance de red que combina: firewall perimetral, DPI, sistema de detección de intrusiones (IDS/IPS), proxy web, filtrado de contenido, y antivirus de red en un solo dispositivo. Opera en la entrada de la red, no en cada equipo individual.

La arquitectura correcta es **en capas**:

1. **UTM perimetral** → Protege contra amenazas externas, inspecciona el contenido del tráfico que entra y sale de la red.
2. **Windows Defender Firewall (host firewall)** → Protege cada equipo contra movimiento lateral dentro de la red.
3. **Microsoft Defender Antivirus** → Detecta malware que llegó al equipo por cualquier vía (USB, email, descarga web).

Ninguna capa es suficiente sola — las tres trabajan en conjunto.

!!! example "Qué WFAS no detecta"
    Un equipo tiene una regla que permite todo el tráfico saliente en el puerto 443 (HTTPS) — lo que es normal para el navegador web. Un atacante que instaló un troyano en el equipo lo configura para que su tráfico de Comando y Control también use HTTPS al puerto 443. Desde el punto de vista de WFAS, este tráfico es indistinguible del tráfico web normal — ambos son TCP saliente al puerto 443. WFAS lo permite. Para detectar este tráfico malicioso necesitaríamos un proxy con inspección SSL o un IDS/IPS — herramientas que estudiamos en la unidad "En la Red".

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Un Técnico va a realizar cambios significativos en la política de firewall de un equipo crítico del puesto de mando. Antes de hacer cualquier cambio, exporta la política actual como respaldo.

    **Exportar política actual:**
    En WFAS → Action (menú) → **Export Policy...** → Guardar como `firewall-politica-backup-YYYYMMDD.wfw` en una ubicación segura.

    Después de hacer los cambios y verificar que todo funciona correctamente, documenta qué reglas se añadieron, modificaron o eliminaron, y quién autorizó cada cambio.

    **Si los cambios generan problemas y necesita revertir:**
    WFAS → Action → **Import Policy...** → Seleccionar el archivo `.wfw` exportado anteriormente.

    Esta práctica de backup antes de cambios es obligatoria en entornos militares donde los cambios deben ser auditables y reversibles. Un cambio de firewall que interrumpe comunicaciones críticas puede afectar la misión — la capacidad de revertir en segundos es tan importante como la capacidad de implementar el cambio.

    El archivo de política exportado también puede usarse para aplicar la misma configuración a múltiples equipos de la misma función, garantizando consistencia en la postura de seguridad.

---

## Resumen

1. Las **reglas por aplicación** son más robustas que las reglas por puerto porque el ejecutable del malware no cambia aunque cambie el puerto — creadas en WFAS → New Rule → Program.
2. Los **protocolos inseguros** (Telnet/23, FTP/21, RDP sin restricción de IP/3389) deben bloquearse — `netsh advfirewall firewall add rule` permite hacerlo desde la línea de comandos.
3. El **logging del firewall** se habilita en WFAS Properties → perfil → Logging; el archivo `pfirewall.log` registra DROP y ALLOW con IP origen, IP destino, puertos y protocolo para cada evento.
4. Las **limitaciones del host firewall** (sin DPI, sin análisis de contenido, sin control por usuario) justifican una arquitectura de capas: UTM perimetral + host firewall + AV en cada equipo.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Windows Firewall with Advanced Security — Microsoft Learn:** https://learn.microsoft.com/es-es/windows/security/operating-system-security/network-security/windows-firewall/windows-firewall-with-advanced-security — Documentación completa incluyendo administración via GPO y PowerShell.
- **netsh advfirewall firewall — referencia de comandos:** https://learn.microsoft.com/es-es/troubleshoot/windows-server/networking/netsh-advfirewall-firewall-control-firewall-behavior — Referencia completa de los comandos de línea de comandos para gestión de reglas.

---

*Siguiente: [Examen Parcial](examen-parcial.md)*
