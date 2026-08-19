# Diseño del examen parcial aleatorio en MoodleCloud

**Fecha:** 2026-08-19

**Estado:** aprobado para planificación

**Examen:** Seguridad de la Información y Criptografía — Unidad «En el Ordenador»

## Objetivo

Convertir el examen parcial actual en una evaluación web sencilla y automática.
Cada alumno se identificará con una cuenta numerada y recibirá 10 preguntas de
opción múltiple seleccionadas de un banco privado de 20. MoodleCloud almacenará
los intentos y permitirá al instructor revisar y exportar las calificaciones.

## Decisiones aprobadas

- MoodleCloud alojará el examen; la computadora del instructor no será el
  servidor.
- Habrá 20 cuentas independientes, `alumno01` a `alumno20`.
- El instructor conservará fuera de Moodle una relación número↔nombre.
- Todas las preguntas serán fáciles, tendrán cuatro alternativas y exactamente
  una respuesta correcta.
- Cada intento contendrá 10 preguntas aleatorias, mostradas una por página.
- El alumno podrá regresar, cambiar respuestas y revisar antes de entregar.
- Se permitirá un solo intento por cuenta.
- La duración será configurable y no forma parte del diseño funcional.
- La calificación será sobre 20; cada uno de los 10 aciertos aportará 2 puntos.
- Las respuestas correctas y la retroalimentación permanecerán ocultas hasta que
  el examen esté cerrado.

## Hallazgos sobre el examen actual

El archivo público `docs/seguridad-criptografia/en-el-ordenador/examen-parcial.md`
no puede utilizarse como banco operativo:

- publica las preguntas y la clave de respuestas en la misma página;
- combina opción múltiple, interpretación de pantallazos y respuesta abierta;
- contiene preguntas más complejas que el nivel solicitado;
- la pregunta sobre ransomware presupone exfiltración sin incluirla en el
  enunciado;
- la señal roja de Seguridad de Windows admite varias causas y no produce una
  única respuesta inequívoca.

Se creará por ello un banco nuevo. No basta con convertir las ocho preguntas
abiertas actuales. Reemplazar la página pública tampoco elimina su historial en
Git, por lo que ninguna pregunta nueva debe reutilizar literalmente ese banco.

## Arquitectura de contenido

El banco tendrá cuatro categorías:

| Categoría | Preguntas disponibles | Preguntas por intento |
|---|---:|---:|
| Controles defensivos | 6 | 3 aleatorias |
| Microsoft Defender | 6 | 3 aleatorias |
| Windows Firewall | 6 | 3 aleatorias |
| Conceptos generales | 2 | 1 aleatoria |
| **Total** | **20** | **10** |

Esta selección estratificada evita que un alumno reciba diez preguntas del mismo
tema. Moodle también mezclará el orden de las alternativas.

Cada pregunta deberá cumplir estas reglas editoriales:

1. Evaluar una sola idea enseñada explícitamente en las seis lecciones de la
   unidad.
2. Usar lenguaje directo, sin negaciones dobles, trampas ni «todas las
   anteriores».
3. Tener una respuesta inequívoca y tres distractores claramente incorrectos,
   pero gramaticalmente plausibles.
4. Evitar datos dependientes de una captura, una versión puntual o una salida que
   varíe entre estaciones.
5. Incluir para el instructor la respuesta correcta, una justificación breve y
   la referencia exacta a la lección de origen.
6. Ser revisada contra el material vigente, no contra la clave antigua.

## Artefactos y confidencialidad

El formato fuente recomendado es GIFT porque Moodle permite importar preguntas
de opción múltiple en bloque y conservar títulos, categorías y retroalimentación.

Los artefactos con secretos vivirán bajo `.private/examen-parcial/`, ruta que debe
añadirse a `.gitignore` antes de crearla:

- `banco-moodle.gift`: las 20 preguntas y sus respuestas;
- `auditoria-banco.md`: tabla pregunta→respuesta→evidencia didáctica;
- `usuarios-numerados.csv`: cuentas definitivas cuando existan correos o aliases
  válidos;
- `asignacion-alumnos.csv`: plantilla local para la relación número↔nombre;
- `contingencia.html`: versión imprimible reservada al instructor.

Ninguno de esos archivos se añadirá a Git ni a MkDocs. El repositorio público sólo
contendrá instrucciones de acceso y documentación operativa sin claves.

Moodle exige correo, nombre y apellido al cargar usuarios por CSV. Las cuentas
definitivas usarán direcciones reales o aliases únicos controlados por el
instructor. No se inventarán direcciones de terceros ni se compartirá una cuenta
entre alumnos. La creación del CSV definitivo queda pendiente hasta disponer de
esas direcciones o de un dominio de aliases.

## Configuración del cuestionario

- Curso privado: `Seguridad de la Información y Criptografía`.
- Actividad: `Examen parcial — En el Ordenador`.
- Método de calificación: calificación más alta, con un único intento permitido.
- Comportamiento: retroalimentación diferida.
- Diseño: una pregunta por página.
- Navegación: libre, para permitir revisión.
- Selección: diez preguntas aleatorias en proporción 3+3+3+1.
- Orden de preguntas y alternativas: mezclado.
- Nota máxima: 20.
- Acceso: ventana de apertura/cierre y contraseña anunciada en el aula.
- Al cierre del tiempo: enviar automáticamente el intento abierto.
- Revisión durante el examen y justo después: ocultar aciertos, feedback y
  respuestas correctas.
- Revisión después del cierre: el instructor decidirá si libera sólo la nota o
  también la explicación.

## Experiencia del alumno

1. El instructor entrega una credencial numerada y registra a quién corresponde.
2. El alumno abre la URL de MoodleCloud o escanea el QR.
3. Inicia sesión con su cuenta individual.
4. Abre el cuestionario e introduce la contraseña anunciada en el aula.
5. Moodle selecciona las 10 preguntas y muestra la primera.
6. El alumno responde, avanza o regresa mediante la navegación del cuestionario.
7. La página de resumen señala preguntas contestadas y pendientes.
8. El alumno confirma la entrega.
9. Moodle registra usuario, selección de preguntas, respuestas, nota y marcas de
   tiempo. El alumno ve la confirmación, pero no la clave mientras el examen siga
   abierto.

## Experiencia del instructor

1. Crear el sitio MoodleCloud y escoger conscientemente su región de datos.
2. Crear el curso y las cuatro categorías del banco.
3. Importar el archivo GIFT y previsualizar las 20 preguntas.
4. Configurar el cuestionario y sus diez posiciones aleatorias.
5. Crear o cargar las 20 cuentas y matricularlas como estudiantes.
6. Ejecutar un ensayo completo con una cuenta ficticia desde una estación de
   alumno.
7. Abrir la actividad, anunciar la contraseña y supervisar los intentos.
8. Cerrar el examen, revisar incidencias y exportar calificaciones.
9. Suspender las cuentas cuando termine la evaluación y custodiar la exportación
   conforme a la política de la institución.

## Privacidad

MoodleCloud sólo necesita conocer las identidades pseudónimas `alumnoNN`. La
relación con nombres reales permanece en una hoja custodiada por el instructor.
Esto reduce los datos personales almacenados en la nube.

MoodleCloud ofrece regiones en Australia, Europa y Estados Unidos y advierte que
la región no puede cambiarse después de crear el sitio. Para Perú se propone
Estados Unidos/Oregon por proximidad de red, sujeto a la política institucional
de residencia de datos.

## Manejo de incidencias

- **Credencial olvidada:** el instructor conserva una copia impresa y puede
  restablecer la contraseña; no se reutiliza la cuenta de otro alumno.
- **Cuenta equivocada:** no iniciar el intento; corregir la asignación antes de
  entrar al cuestionario.
- **Desconexión breve:** volver a ingresar con la misma cuenta y continuar el
  intento. Este comportamiento se comprobará durante el ensayo.
- **Intento enviado accidentalmente:** el instructor documenta la incidencia y
  decide si abre un nuevo intento individual; nunca cambia globalmente el límite
  para toda la clase durante la evaluación.
- **Falla de MoodleCloud o Internet:** usar la contingencia imprimible custodiada
  por el instructor y registrar manualmente qué versión recibió cada alumno.
- **Pregunta defectuosa:** no modificarla mientras haya intentos activos. Cerrar
  la sesión, revisar el reporte y aplicar la corrección de calificación de forma
  uniforme.

## Verificación y aceptación

Antes de habilitar el examen real se ejecutará una prueba con cuenta ficticia que
debe demostrar:

- exactamente 20 preguntas importadas y clasificadas 6+6+6+2;
- exactamente 10 preguntas por intento y distribución 3+3+3+1;
- ninguna pregunta repetida dentro de un intento;
- cuatro alternativas y una única respuesta válida por pregunta;
- una pregunta por página, navegación libre y resumen previo a la entrega;
- un solo intento permitido;
- nota máxima 20 y cálculo de 2 puntos por acierto;
- clave, corrección y feedback ocultos antes del cierre;
- registro del usuario, respuestas y timestamps;
- exportación utilizable de las calificaciones;
- acceso correcto desde una estación distinta a la del instructor;
- recuperación comprobada tras una desconexión breve;
- contingencia imprimible legible y sin exposición pública.

La auditoría del banco no se considerará aprobada únicamente porque GIFT importe
sin errores. Cada pregunta debe contar además con evidencia didáctica y revisión
humana de claridad.

## Fuentes operativas

- [MoodleCloud y opciones de alojamiento](https://support.moodle.com/support/solutions/articles/80000825155-moodlecloud-and-moodle-hosting-options)
- [Creación e inscripción de usuarios en MoodleCloud](https://support.moodle.com/support/solutions/articles/80000830221-enrol-users-in-moodlecloud)
- [Construcción y preguntas aleatorias](https://docs.moodle.org/501/en/Building_Quiz)
- [Configuración, navegación y revisión del cuestionario](https://docs.moodle.org/500/en/Quiz_settings)
- [Formato de importación GIFT](https://docs.moodle.org/test/GIFT)
- [Regiones de datos de MoodleCloud](https://support.moodle.com/support/solutions/articles/80000831971-moodlecloud-data-hosting-locations)

## Fuera de alcance

- Desarrollar una plataforma de exámenes propia.
- Alojar respuestas o resultados en GitHub Pages.
- Registrar nombres reales en Moodle antes de contar con autorización y datos
  válidos.
- Proctoring, bloqueo del navegador, webcam o vigilancia biométrica.
- Automatizar la creación del sitio MoodleCloud o manejar credenciales
  administrativas del usuario.
