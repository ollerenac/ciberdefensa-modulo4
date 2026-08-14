---
# Horas asignadas: 0 hrs
# Tipo: Referencia de laboratorio
---

# Laboratorio virtual — VM-WIN-SI

> **Referencia rápida** | Estación virtual usada en los laboratorios de la unidad *En el Ordenador*

Esta página reúne todo lo necesario para entrar y trabajar en la máquina virtual. Tenerla abierta durante las prácticas.

---

## Cuentas y contraseñas

!!! info "Credenciales de la estación"

    | Cuenta | Contraseña | Para qué sirve |
    |--------|-----------|----------------|
    | **`operador_alumno`** | `Operador2026` | **La cuenta de trabajo.** Con esta se inicia sesión siempre. Es una cuenta estándar, sin privilegios administrativos. |
    | **`tec_admin`** | *la que indique el instructor* | **Cuenta de elevación.** No se inicia sesión con ella: se teclea solo cuando Windows pide permiso de administrador (UAC). |
    | **`operador_temp`** | `Temporal2026` | **No usar.** Forma parte del escenario del laboratorio. |

!!! warning "Estas credenciales son de laboratorio"
    Son idénticas en las veinte estaciones del aula y las conoce todo el grupo. Eso es correcto para una imagen de práctica desechable, y **solo** para eso.

    - La VM no sale de la red del aula.
    - No se guarda en ella ningún dato real, personal o de servicio.
    - Estas contraseñas no se reutilizan jamás fuera de este laboratorio.

    Al terminar cada sesión el instructor restaura el snapshot `01-MISION-CONTROLES` y la estación vuelve a su estado inicial: **todo lo que quede sin entregar se pierde**.

---

## Cómo iniciar la máquina

1. Abrir **Oracle VirtualBox** en el equipo del aula.
2. Seleccionar **VM-WIN-SI** en la lista de la izquierda.
3. Comprobar con el instructor que el estado actual es el snapshot **`01-MISION-CONTROLES`**.
4. Pulsar **Iniciar**.
5. Iniciar sesión como **`operador_alumno`**.

!!! tip "La tecla de tubería `|`"
    Casi todos los comandos del laboratorio usan el carácter `|`. En un teclado español se obtiene con `AltGr + 1`. Si no aparece, avisar al instructor antes de empezar: el problema es la distribución del teclado, no el comando.

!!! tip "Salir de la ventana de la máquina virtual"
    Si el ratón queda "atrapado" dentro de la VM, pulsar la tecla **Ctrl derecha** para liberarlo.

---

## Carpetas de la estación

| Ruta | Permiso del alumno | Contenido |
|------|-------------------|-----------|
| `C:\Mision` | **Solo lectura** | Documentos de la misión: el parte de incidentes y el acta de identidades autorizadas. Es evidencia: no se altera. |
| `C:\Lab\Evidencias` | **Lectura y escritura** | Aquí se guarda todo lo que produzca el alumno: transcripciones, partes y capturas. |

!!! danger "Acceso denegado en `C:\Mision` es lo esperado"
    Si un intento de escribir en `C:\Mision` devuelve *Acceso denegado*, el sistema está funcionando correctamente. No es un fallo ni hay que buscar la forma de saltárselo.

---

## Abrir PowerShell con y sin privilegios

Los laboratorios distinguen con precisión los dos modos. Usar el equivocado hace que un comando falle o que se modifique algo que no tocaba.

=== "Sin elevación (Parte 1)"

    Menú **Inicio** → escribir `Windows PowerShell` → **Enter**.

    La barra de título dice `Windows PowerShell`, **sin** la palabra *Administrador*.

    Comprobación:

    ```powershell
    [Security.Principal.WindowsPrincipal]::new(
        [Security.Principal.WindowsIdentity]::GetCurrent()
    ).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
    ```

    Debe devolver **`False`**.

=== "Con elevación (Parte 2)"

    Menú **Inicio** → escribir `Windows PowerShell` → clic derecho → **Ejecutar como administrador**.

    Windows pedirá credenciales: introducir **`tec_admin`** y su contraseña.

    La barra de título dice `Administrador: Windows PowerShell`.

    Comprobación: el mismo comando de la pestaña anterior debe devolver **`True`**.

!!! note "Por qué se pide usuario y contraseña"
    `operador_alumno` no es administrador, así que Windows no ofrece un simple «Sí»: exige las credenciales de una cuenta que sí lo sea. Eso es exactamente el principio de mínimo privilegio en funcionamiento.

---

## Guardar una captura de pantalla

Varios ejercicios piden guardar capturas con un nombre exacto. El procedimiento completo:

1. Pulsar `Win + Shift + S`.
2. Seleccionar con el ratón la zona a capturar.
3. Aparece un aviso en la esquina inferior derecha: **hacer clic en él**. Si desaparece, abrir la aplicación **Recorte y anotación** desde el menú Inicio.
4. En la ventana que se abre, `Ctrl + S` (o el icono del disquete).
5. Navegar hasta `C:\Lab\Evidencias`, escribir el nombre **exacto** que pide el ejercicio y guardar.

!!! warning "El paso 3 es el que se olvida"
    Sin hacer clic en el aviso, la captura se queda solo en el portapapeles y **no existe como archivo**. La evidencia no se habrá guardado.

---

## Si algo va mal

| Situación | Qué hacer |
|-----------|-----------|
| Un comando devuelve un error | Registrar el error tal cual y avisar al instructor. **No improvisar otro comando.** |
| Un comando pide credenciales y no se esperaba | Cancelar y avisar. Probablemente se abrió PowerShell elevado sin necesidad. |
| La transcripción se cerró por accidente | Volver a abrirla con `Start-Transcript` y anotar el corte en el parte. |
| Se modificó algo que no tocaba | Anotarlo. Es un hallazgo, no una falta: el instructor restaura el snapshot. |

---

*Para el instructor: el estado exacto que esta imagen presenta al alumno está descrito en la solución de cada laboratorio.*
