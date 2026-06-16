---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# Controles Defensivos en el Ordenador (Parte 2)

> **Duración:** 1 hora | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En el Ordenador | Continuación de Parte 1

En la Parte 1 establecimos el marco conceptual: la Triada CIA como referencia, la superficie de ataque como lo que el atacante ve, el Windows Security Center como panel de estado, el principio de mínimo privilegio y los seis controles CIS L1 que todo equipo debe cumplir. En esta Parte 2 aplicamos esos conceptos con comandos concretos: auditamos las cuentas locales del equipo, revisamos la política de contraseñas y deshabilitamos los servicios que amplían la superficie de ataque sin aportar valor a la misión.

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Auditar las cuentas de usuario locales de un equipo Windows 11 usando PowerShell e identificar cuentas con privilegios excesivos o sin uso.
- Verificar y configurar la política de contraseñas local siguiendo las recomendaciones CIS Level 1.
- Deshabilitar servicios innecesarios (Print Spooler, Remote Registry, SMBv1) usando PowerShell para reducir la superficie de ataque.

---

## Auditoría de Cuentas Locales

Toda cuenta de usuario que existe en un equipo es una identidad que puede ser comprometida. Cuentas que nadie usa actualmente, cuentas con nombres genéricos, o cuentas que tienen privilegios de Administrador sin justificación son vectores de ataque que un adversario buscará.

El primer paso de la auditoría es listar todas las cuentas locales con su estado:

```powershell
# Listar todas las cuentas locales con nombre, estado y último inicio de sesión
Get-LocalUser | Select-Object Name, Enabled, LastLogon
```

La salida típica muestra columnas: Name (nombre de la cuenta), Enabled (si está habilitada o no), LastLogon (cuándo se usó por última vez). Una cuenta con `Enabled: True` y `LastLogon` de hace más de 90 días debe investigarse — ¿por qué existe esta cuenta y quién la usa?

Para identificar qué cuentas tienen privilegios de Administrador local:

```powershell
# Listar todos los miembros del grupo Administradores local
Get-LocalGroupMember -Group "Administrators"
```

En un equipo correctamente configurado, este comando debería mostrar solo las cuentas que tienen justificación explícita para tener privilegios administrativos. Si aparece una cuenta con nombre como "admin", "temp", "prueba" o "usuario2", debe investigarse inmediatamente.

!!! warning "Señales de alerta en cuentas locales"
    Las siguientes situaciones requieren investigación inmediata y posiblemente reporte al oficial de seguridad:

    - Cuenta con nombre genérico ("admin", "temp", "test", "prueba") con estado habilitado.
    - Cuenta en el grupo Administrators que no corresponde a personal autorizado.
    - Cuenta con LastLogon de una fecha durante un período en que nadie debería haber usado el equipo (fines de semana, vacaciones, etc.).
    - La cuenta Guest (`Invitado` en español) aparece con estado habilitado — debe estar siempre deshabilitada.

Si se encuentra una cuenta desconocida con privilegios de Administrador, la acción inmediata es: documentar el hallazgo, deshabilitar la cuenta (`Disable-LocalUser -Name "nombre"`), y reportar al oficial de seguridad antes de investigar el origen.

---

## Política de Contraseñas

La política de contraseñas define las reglas que Windows aplica a todas las contraseñas de cuentas locales. Para acceder a ella:

`Win+R → secpol.msc → Account Policies → Password Policy`

Las configuraciones recomendadas por el CIS Benchmark Level 1 son:

| Parámetro | Valor CIS L1 | Valor mínimo aceptable | Por qué importa |
|-----------|-------------|----------------------|-----------------|
| Minimum password length | 14 caracteres | 12 caracteres | Contraseñas cortas son vulnerables a ataques de diccionario y fuerza bruta |
| Maximum password age | 60 días | 90 días | Limita el tiempo de exposición si una contraseña es comprometida |
| Password must meet complexity requirements | Habilitado | Habilitado | Exige que la contraseña contenga mayúsculas, minúsculas, números y símbolos |
| Account lockout threshold | 5 intentos | 10 intentos | Bloquea la cuenta después de N intentos fallidos — previene fuerza bruta |
| Account lockout duration | 15 minutos | 15 minutos | Tiempo que la cuenta permanece bloqueada antes de desbloquearse automáticamente |

Para configurar el umbral de bloqueo, ir a: `secpol.msc → Account Policies → Account Lockout Policy → Account lockout threshold: 5`.

!!! example "Aplicación en entorno castrense"
    Los sistemas de comunicaciones militares frecuentemente requieren contraseñas más robustas que el estándar corporativo. En un puesto de mando, los equipos que acceden a sistemas de comunicaciones cifradas deben tener contraseñas de al menos 16 caracteres, rotadas cada 30 días, con historial de contraseñas configurado para que no se pueda reusar ninguna de las últimas 24. El oficial de seguridad establece estas políticas mediante directiva; el Técnico verifica que estén implementadas antes de conectar el equipo a la red clasificada.

---

## Deshabilitar Servicios Innecesarios

Cada servicio de Windows que está en ejecución es código activo con acceso al sistema operativo. Si ese servicio tiene una vulnerabilidad, un atacante puede explotarla — incluso si la vulnerabilidad no tiene nada que ver con el trabajo que realiza el usuario en ese equipo.

El principio es simple: si un servicio no cumple ninguna función útil en este equipo específico, debe estar deshabilitado. Cada servicio deshabilitado reduce la superficie de ataque.

Los tres servicios que se deben deshabilitar en la mayoría de estaciones de trabajo militares que no cumplen funciones de servidor de impresión ni de administración remota:

| Servicio | Por qué deshabilitar | Comando PowerShell |
|----------|---------------------|-------------------|
| **Print Spooler** | Vector del exploit PrintNightmare (CVE-2021-34527) — permite escalación de privilegios a SYSTEM y ejecución remota de código. Si el equipo no imprime, este servicio no tiene propósito. | `Stop-Service Spooler; Set-Service Spooler -StartupType Disabled` |
| **Remote Registry** | Permite modificar el registro de Windows de forma remota. Nadie debe poder alterar el registro de un equipo desde la red — si se necesita hacer cambios, el Técnico debe estar físicamente frente al equipo. | `Stop-Service RemoteRegistry; Set-Service RemoteRegistry -StartupType Disabled` |
| **SMBv1 (protocolo, no servicio)** | El protocolo SMB versión 1 es el vector que explotó WannaCry en 2017 para propagarse entre equipos en segundos. La versión moderna SMBv2/3 lo reemplaza completamente. | `Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol` |

Verificar el estado actual de estos servicios antes de deshabilitar:

```powershell
# Verificar estado del servicio Print Spooler
Get-Service -Name Spooler | Select-Object Name, Status, StartType

# Verificar estado del servicio Remote Registry
Get-Service -Name RemoteRegistry | Select-Object Name, Status, StartType

# Verificar si SMBv1 está habilitado (debe mostrar State: Disabled)
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    Antes de conectar una estación de trabajo a la red de unidad, el Técnico ejecuta la siguiente secuencia de auditoría:

    ```powershell
    # 1. Auditar cuentas locales
    Get-LocalUser | Select-Object Name, Enabled, LastLogon

    # 2. Verificar miembros del grupo Administradores
    Get-LocalGroupMember -Group "Administrators"

    # 3. Verificar estado de servicios críticos
    Get-Service -Name Spooler, RemoteRegistry | Select-Object Name, Status, StartType

    # 4. Verificar SMBv1 (debe estar deshabilitado)
    Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol | Select-Object State
    ```

    Los resultados se documentan en el registro de auditoría del equipo: fecha, nombre del Técnico, estado encontrado y acciones correctivas tomadas. Este registro se reporta al oficial de seguridad de la unidad antes de la integración a la red. El control no es burocrático — es la evidencia de que el equipo fue verificado antes de conectarse y que alguien es responsable de su estado.

---

## Resumen

1. La auditoría de cuentas locales (`Get-LocalUser` y `Get-LocalGroupMember`) revela identidades con acceso al sistema — cualquier cuenta desconocida con privilegios de Administrador es una señal de alarma.
2. La política de contraseñas se configura en `secpol.msc` — el mínimo CIS L1 es 14 caracteres, complejidad habilitada, bloqueo a los 5 intentos fallidos.
3. Print Spooler, Remote Registry y SMBv1 son servicios y protocolos que deben estar deshabilitados en equipos que no los necesitan — cada uno tiene vulnerabilidades críticas documentadas.
4. Todo hallazgo de auditoría se documenta y se reporta al oficial de seguridad antes de conectar el equipo a la red.

---

*Siguiente: [Antivirus Avanzados (Parte 1)](antivirus-avanzados-p1.md)*
