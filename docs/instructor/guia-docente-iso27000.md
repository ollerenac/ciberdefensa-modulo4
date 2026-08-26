---
search:
  exclude: true
meta:
  - name: robots
    content: noindex, nofollow
---

# Guía docente — Familia ISO/IEC 27000

> **Documento de uso del instructor.** Esta página no aparece en el menú ni en el buscador interno del curso. Su URL es técnicamente pública; no contiene claves de examen ni información sensible.

Esta guía no reemplaza el contenido de las clases. Sirve como apoyo durante la exposición: indica **qué aprendizaje debe quedar**, **cómo comunicarlo**, **qué preguntas hacer** y **qué confusiones corregir**.

## Orientación general para las ocho sesiones

### El hilo que debe conservar el alumno

Presente toda la unidad como una sola cadena de decisiones:

> **Comprender el contexto → identificar riesgos → decidir qué hacer → seleccionar controles → producir evidencia → comprobar y mejorar.**

Si los alumnos entienden esta cadena, podrán ubicar cada norma aunque no recuerden todos sus números:

- **ISO/IEC 27000** entrega el panorama, los conceptos y el vocabulario.
- **ISO/IEC 27001** establece los requisitos que debe cumplir un SGSI.
- **ISO/IEC 27002** orienta la implementación de los controles.
- **ISO/IEC 27005** orienta la gestión del riesgo de seguridad de la información.

### Regla de comunicación

Explique siempre en este orden:

1. **Problema operacional:** qué puede afectar la misión.
2. **Decisión de gestión:** quién debe decidir y con qué criterio.
3. **Acción concreta:** qué hacen el mando, el responsable y el Técnico.
4. **Evidencia:** cómo se demuestra que la acción ocurrió.
5. **Eficacia:** cómo se comprueba que realmente redujo el riesgo.

Evite comenzar por definiciones extensas o números de cláusulas. Primero haga visible el problema; después coloque el nombre ISO sobre una situación que el alumno ya comprendió.

### Distinciones que deben repetirse durante toda la unidad

- **Tener una herramienta no equivale a gestionar la seguridad.** Deben existir responsables, procedimientos, evidencia y revisión.
- **Documentar no es acumular papeles.** Es dejar evidencia útil para operar, verificar, aprender y rendir cuentas.
- **Cumplimiento no garantiza ausencia de incidentes.** Un SGSI reduce y gestiona el riesgo; no promete riesgo cero.
- **Un control implementado no necesariamente es eficaz.** La instalación o el registro demuestran ejecución; una prueba de resultado demuestra eficacia.
- **La norma no sustituye el criterio profesional.** Organiza las preguntas y exige justificar las decisiones.

### Comprobación rápida al finalizar cada clase

Use siempre una pregunta de transferencia, no una pregunta de memoria:

> “¿Cómo se aplicaría esta idea en una sección de comunicaciones y qué evidencia pediría usted para comprobarla?”

Una respuesta sólida debe mencionar una situación concreta, un responsable, una acción y una evidencia. Si el alumno solo menciona una herramienta o repite una definición, todavía no ha transferido el concepto.

## Ejemplo resuelto para modelar las exposiciones — Colonial Pipeline (2021)

Utilice este caso para mostrar el nivel de precisión esperado en el trabajo **Ataques → controles de seguridad**. Preséntelo después de explicar la consigna o al terminar las exposiciones, para evitar que los grupos simplemente copien la estructura.

!!! warning "Límite de la conclusión"

    Este es un análisis docente basado en información pública, no una auditoría de Colonial Pipeline. No diga que la empresa “incumplía ISO” ni que carecía de todos los controles. La formulación rigurosa es: **una VPN heredada continuaba utilizable y no exigía MFA; los controles seleccionados habrían añadido resistencia directa frente a ese vector de acceso**.

### 1. Detalles del ataque

| Campo | Ejemplo de respuesta esperada |
|---|---|
| **Víctima** | Colonial Pipeline Company, operador estadounidense de infraestructura energética. |
| **Atacante** | Un afiliado que utilizó el ransomware como servicio **DarkSide**. El FBI atribuyó el compromiso a DarkSide, pero no identificó públicamente al operador humano concreto. |
| **Acceso inicial** | La evidencia forense más temprana, del **29 de abril de 2021**, muestra un inicio de sesión en un dispositivo VPN con el usuario y la contraseña de un empleado. Se utilizó un perfil de VPN heredado que la empresa creía inactivo, pero que todavía funcionaba. |
| **Debilidad explotada** | El perfil VPN aceptaba solamente usuario y contraseña: **no exigía autenticación multifactor (MFA)**. La contraseña era compleja, pero también había sido utilizada en otro sitio posteriormente comprometido. La investigación no pudo determinar de manera concluyente cómo obtuvo el atacante las credenciales. |
| **Vulnerabilidad CVE** | **No se identificó una CVE para el acceso inicial.** El atacante abusó de credenciales válidas y de una configuración de acceso remoto débil; no explotó un defecto de software publicado. |
| **Acción del atacante** | Una vez dentro de la red de TI, desplegó DarkSide, cifró sistemas y presentó una exigencia de rescate. Las fuentes oficiales no publicaron una reconstrucción completa del movimiento lateral. |
| **Impacto** | El ransomware afectó sistemas de TI. Colonial detuvo preventivamente aproximadamente **5.500 millas de tubería** mientras contenía y evaluaba el incidente. CISA y el FBI no reportaron afectación directa de la red operacional —OT—. |

#### Cadena técnica que debe explicar el alumno

> **Credenciales comprometidas** → **VPN heredada todavía activa** → **contraseña como único factor** → **acceso no autorizado a TI** → **ransomware y cifrado** → **apagado preventivo para contener el riesgo hacia OT**.

Insista en dos precisiones:

- La reutilización de la contraseña está documentada, pero no se confirmó que esa fuera exactamente la vía por la que el atacante la consiguió.
- La interrupción operacional no significa que el atacante controlara válvulas o sistemas industriales; el cierre fue una decisión preventiva de la empresa.

### 2. Controles de ISO/IEC 27002:2022 que habrían añadido resistencia

| Control | Condición observada | Resistencia que debía aportar |
|---|---|---|
| **8.5 — Autenticación segura** | La ruta VPN utilizada no exigía MFA. | Aunque el atacante conociera la contraseña, no habría podido ingresar sin el segundo factor. Es la relación más directa y mejor documentada. |
| **5.17 — Información de autenticación** | La contraseña corporativa también se utilizaba en otro sitio comprometido. | Contraseñas únicas, un gestor institucional y la detección de credenciales expuestas reducen el riesgo de reutilización y abuso. |
| **5.18 — Derechos de acceso** | Un perfil VPN que se creía fuera de uso todavía permitía autenticarse. | La revisión periódica y la revocación verificable eliminan accesos que ya no tienen una necesidad operacional. |
| **8.9 — Gestión de la configuración** | Persistía una ruta heredada con autenticación de un solo factor, distinta del mecanismo normal con token. | Una línea base aprobada y la verificación del retiro completo impiden que sobrevivan configuraciones antiguas expuestas. |
| **5.16 — Gestión de identidades** | Existían credenciales válidas capaces de autenticarse mediante una ruta que no debía utilizarse. | Cada identidad y cuenta remota debe tener propietario, propósito, vigencia y un ciclo formal de alta, revisión y baja. Este vínculo es complementario porque no se publicó el expediente interno de la cuenta. |

!!! note "Cómo evaluar una selección diferente"

    Acepte otro control si el alumno identifica primero una debilidad concreta, explica cómo el control la previene, detecta o limita y no presenta una inferencia como hecho. No acepte una lista extensa de controles sin relación causal.

No presente como ausentes los controles de respuesta a incidentes, segmentación, copias de seguridad o continuidad. La evidencia pública no permite esa conclusión y, de hecho, Colonial activó su respuesta, notificó a las autoridades, aisló sistemas y operó parcialmente con procedimientos manuales.

### 3. Procedimientos específicos para implementar los controles

Los procedimientos siguientes muestran el nivel de detalle esperado: indican **qué se hace, quién lo hace y qué evidencia permite comprobarlo**.

#### Procedimiento A — Exigir MFA y contraseñas únicas en todo acceso remoto

**Implementa principalmente:** 8.5 y 5.17.

1. El responsable de seguridad obtiene de los firewalls, concentradores VPN, servicios en la nube y directorios una lista única de todos los accesos remotos.
2. Para cada acceso registra el sistema, dirección expuesta, propietario, usuarios autorizados, método de autenticación y fecha prevista de retiro.
3. La organización prohíbe utilizar la contraseña corporativa en servicios personales y entrega un gestor de contraseñas para generar una clave distinta por cuenta.
4. El administrador bloquea contraseñas conocidas como expuestas y ordena su cambio inmediato cuando existe una alerta confiable de compromiso.
5. Se configura MFA para todas las conexiones remotas. Para administradores y sistemas críticos se priorizan llaves FIDO2/WebAuthn o tokens resistentes al *phishing*.
6. Una VPN que no admite MFA se retira o se coloca detrás de un servicio moderno que sí lo exija. No se mantiene abierta como excepción permanente.
7. Un técnico de pruebas intenta ingresar con una contraseña válida, pero sin el segundo factor. El resultado esperado es **acceso rechazado**.
8. Mensualmente se informa el porcentaje de accesos remotos cubiertos por MFA. La meta es 100 %; cada excepción debe tener responsable, tratamiento compensatorio y fecha de vencimiento.

**Evidencia:** inventario de accesos, política de contraseñas, exportación de la configuración MFA, reporte de usuarios inscritos, resultado de la prueba negativa y registro de excepciones.

#### Procedimiento B — Dar de baja accesos que ya no son necesarios

**Implementa principalmente:** 5.16 y 5.18.

1. Todo acceso VPN se crea mediante una solicitud aprobada que identifica a la persona, función, responsable, recursos permitidos y fecha de expiración.
2. No se crean cuentas compartidas. Cada acceso debe poder atribuirse a una persona o a un servicio técnico con propietario identificado.
3. Recursos Humanos o el responsable de contratistas comunica inmediatamente las bajas y cambios de función. El administrador desactiva ese mismo día los accesos que dejan de corresponder.
4. El sistema suspende las cuentas VPN que superen el periodo de inactividad definido. Su reactivación requiere una nueva aprobación registrada.
5. Cada trimestre, el dueño del sistema revisa la lista de usuarios y confirma uno por uno quién mantiene una necesidad operacional. Los accesos no confirmados se suspenden.
6. El administrador compara personal vigente, identidades del directorio y usuarios configurados en la VPN. Investiga y corrige cada diferencia.
7. Después de la baja, otro técnico intenta autenticar la cuenta y adjunta al ticket el rechazo obtenido. Cambiar un estado en una hoja de cálculo no demuestra que el acceso dejó de funcionar.

**Evidencia:** solicitudes y aprobaciones, reporte de inactividad, revisión trimestral firmada, conciliación de identidades, ticket de baja y prueba posterior de bloqueo.

#### Procedimiento C — Retirar completamente una VPN o configuración heredada

**Implementa principalmente:** 8.9.

1. La organización aprueba una línea base de acceso remoto: tecnologías y versiones permitidas, MFA obligatorio, cifrado, registros y redes alcanzables.
2. El responsable compara todos los concentradores, perfiles y reglas de acceso con la línea base. Marca cualquier componente antiguo, no documentado, sin propietario o sin MFA.
3. Para retirar un servicio abre un cambio formal y elimina todos sus componentes: perfil VPN, cuentas locales, certificados, reglas de firewall, publicación DNS y rutas asociadas.
4. Desde una red externa de prueba intenta conectarse al servicio retirado. El resultado esperado es que no responda o rechace el acceso; se conserva la captura de la prueba.
5. Periódicamente se descubren los servicios expuestos a Internet y se comparan con el inventario aprobado. Toda VPN o puerto no reconocido genera una alerta y un ticket.
6. Las configuraciones se exportan a un repositorio controlado y se revisan para detectar autenticación de un solo factor, cuentas locales no autorizadas y otras desviaciones.
7. Toda excepción requiere aprobación del responsable del riesgo, medidas compensatorias y fecha de vencimiento. Al vencer, se cierra automáticamente o se renueva formalmente.

**Evidencia:** línea base, inventario, ticket de cambio, configuración anterior y posterior, prueba externa de cierre, resultados del descubrimiento y registro de excepciones.

#### Procedimiento D — Comprobar que los controles funcionan juntos

**Implementa y verifica:** 5.16, 5.18, 8.5 y 8.9.

1. Trimestralmente, un revisor que no administre la VPN selecciona una muestra de usuarios activos, usuarios dados de baja y servicios retirados.
2. Para cada usuario activo comprueba aprobación vigente, MFA inscrito, permisos mínimos y fecha de revisión.
3. Para cada usuario dado de baja comprueba que no puede autenticarse ni conserva certificados, tokens o grupos con acceso indirecto.
4. Para cada servicio retirado comprueba desde el exterior que no responde y revisa que no queden reglas, DNS, cuentas o perfiles asociados.
5. Cada diferencia recibe responsable y fecha de corrección. Después se repite la prueba: cerrar el ticket sin volver a probar no demuestra eficacia.
6. El mando recibe cuatro indicadores: cobertura MFA, cuentas sin propietario, accesos vencidos todavía activos y tiempo promedio de revocación.

**Evidencia:** plan de muestreo, lista de comprobación, resultados, tickets de corrección, revalidación e informe de indicadores.

### Cómo comunicar el ejemplo en el aula

1. Proyecte primero solamente los detalles del ataque y pregunte: “¿En qué punto de la cadena habría sido más barato detenerlo?”.
2. Revele después los controles. Por cada uno, obligue a completar la frase: “Este control habría añadido resistencia porque…”.
3. Muestre los procedimientos al final y contraste **actividad** con **eficacia**: configurar MFA es una actividad; comprobar que la contraseña sola ya no permite entrar es evidencia de eficacia.
4. Cierre preguntando: “¿Qué afirmaciones de este ejemplo son hechos documentados y cuáles son inferencias profesionales?”.

??? info "Fuentes oficiales del caso"

    - [Audiencia de la Cámara: testimonios de Colonial Pipeline y Mandiant, 9 de junio de 2021](https://www.congress.gov/117/chrg/CHRG-117hhrg45085/CHRG-117hhrg45085.pdf).
    - [Testimonio de Joseph Blount ante el Senado, 8 de junio de 2021](https://www.hsgac.senate.gov/wp-content/uploads/imo/media/doc/Testimony-Blount-2021-06-08.pdf).
    - [Interrogatorio oficial del Senado sobre la VPN y MFA](https://www.hsgac.senate.gov/media/reps/portman-presses-president-and-ceo-of-colonial-pipeline-on-gaps-in-cybersecurity-practices/).
    - [Aviso conjunto CISA–FBI AA21-131A sobre DarkSide](https://www.cisa.gov/sites/default/files/publications/AA21-131A_Darkside_Ransomware.pdf).
    - [Cronología del Departamento de Energía](https://www.energy.gov/ceser/colonial-pipeline-cyber-incident).
    - [Comunicado del Departamento de Justicia sobre la recuperación parcial del rescate](https://www.justice.gov/archives/opa/pr/department-justice-seizes-23-million-cryptocurrency-paid-ransomware-extortionists-darkside).

---

## 1. Generalidades — Parte 1

**Duración:** 4 horas · [Abrir material de clase](../capas-si/familia-iso27000/iso27000-generalidades-p1.md)

### Resultado que debe quedar

El alumno debe poder explicar que un **SGSI es un sistema continuo para tomar y verificar decisiones sobre riesgos**, no una colección de productos tecnológicos ni un certificado colocado en la pared.

### Mensaje irrenunciable

> “La seguridad deja de depender de la improvisación cuando la unidad sabe qué protege, qué riesgos enfrenta, qué controles eligió, quién responde y cómo comprueba que funcionan.”

Si solo puede transmitir una idea en esta sesión, transmita esa.

### Cómo abrir la clase

No empiece preguntando qué significa ISO. Plantee esta situación:

> “Una sala tiene guardia, cámaras y cerradura. ¿Eso basta para afirmar que está protegida? ¿Quién puede entrar, quién revisa la cámara, qué ocurre si se pierde una llave y cómo sabemos si el procedimiento funciona?”

Recoja respuestas y muestre que los dispositivos aislados no forman un sistema. Luego traslade la misma lógica a información, radios, terminales C2, cuentas, respaldos y procedimientos. Introduzca el término **SGSI** después de que los alumnos hayan reconocido la necesidad del sistema.

### Secuencia recomendada de explicación

1. **Problema que originó los estándares.** Diferentes organizaciones protegían información con criterios incompatibles y sin evidencia comparable.
2. **Familia, no norma única.** Presente las cuatro normas principales como herramientas con funciones diferentes.
3. **Qué es un SGSI.** Destaque la palabra “sistema”: personas, responsabilidades, procesos, tecnología y mejora.
4. **Los cuatro componentes.** Alcance y contexto, riesgos, controles y mejora continua.
5. **Ejemplo SC-RI8.** Mantenga el mismo escenario durante toda la explicación para que el alumno vea cómo se conectan los componentes.

### Cómo comunicar la relación entre las normas

Use esta fórmula breve y repítala:

- **27000 orienta:** “¿Qué significan estos conceptos y cómo se relaciona la familia?”
- **27001 exige:** “¿Qué debe existir en el sistema de gestión?”
- **27002 aconseja:** “¿Cómo podemos implementar los controles?”
- **27005 analiza:** “¿Cómo identificamos y tratamos el riesgo?”

No presente ISO 27002 como una lista obligatoria de 93 compras o instalaciones. Los controles se seleccionan según el riesgo y el contexto.

### Ejemplo conductor

Utilice las cuentas compartidas del servidor de la SC-RI8:

- **Activo:** archivos operativos.
- **Riesgo:** acceso o modificación sin trazabilidad.
- **Decisión:** el Jefe de Comunicaciones determina que el riesgo es inaceptable.
- **Controles:** cuentas individuales, mínimo privilegio y registro de accesos.
- **Evidencia:** listado de cuentas, autorizaciones y logs.
- **Eficacia:** una revisión demuestra que cada acción puede atribuirse a una persona y que no quedan cuentas de personal trasladado.

Vuelva a este ejemplo cada vez que aparezca un concepto nuevo. Evita que la clase se convierta en ocho definiciones desconectadas.

### Preguntas para hacer a los alumnos

- “¿Qué diferencia hay entre instalar un antivirus y gestionar el riesgo de malware?”
- “¿Qué puede quedar fuera del alcance y quién debe responder por ello?”
- “¿Qué necesita saber el Comandante que no puede responder una lista de herramientas instaladas?”
- “¿Qué documento o registro probaría que una medida se aplica realmente?”

### Respuestas que revelan comprensión

Busque que mencionen decisiones y responsabilidades: inventario, propietario del riesgo, procedimiento, autorización, registro, revisión o prueba. Valore más una explicación sencilla y aplicada que una definición recitada literalmente.

### Confusiones que debe corregir

- **“ISO 27000 es la norma certificable.”** Corrija: el estándar de requisitos certificables es ISO/IEC 27001.
- **“Implementar un SGSI es instalar seguridad.”** Corrija: la tecnología es una parte; el SGSI coordina gestión, personas, procesos y controles.
- **“Certificado significa que no habrá incidentes.”** Corrija: la certificación evalúa conformidad del sistema dentro de un alcance; no elimina el riesgo.
- **“Todo debe estar dentro del alcance.”** Corrija: el alcance puede ser limitado, pero sus límites y exclusiones deben justificarse y tener responsables conocidos.

### Distribución sugerida del tiempo

- 20 min: problema inicial y propósito de los estándares.
- 35 min: familia ISO/IEC 27000 y función de cada norma.
- 35 min: concepto y componentes del SGSI.
- 10 min: pausa y comprobación oral.
- 60 min: ejemplo integrador SC-RI8.
- 40 min: análisis en parejas de otro activo de la unidad.
- 25 min: puesta en común y corrección de confusiones.
- 15 min: síntesis y salida individual.

### Cierre sugerido

Pida completar esta frase en una hoja o verbalmente:

> “Un SGSI no es __________; es __________ porque permite __________.”

No cierre hasta escuchar al menos una respuesta que incluya gestión sistemática del riesgo y verificación.

---

## 2. Generalidades — Parte 2

**Duración:** 2 horas · [Abrir material de clase](../capas-si/familia-iso27000/iso27000-generalidades-p2.md)

### Resultado que debe quedar

El alumno debe comprender que el SGSI **se mantiene útil mediante el ciclo PDCA** y que la eficacia se demuestra con resultados, no con documentos o controles que simplemente existen.

### Mensaje irrenunciable

> “Planificar e implementar no basta: hay que comprobar el resultado y corregir el sistema.”

### Cómo abrir la clase

Use una rutina conocida:

> “Se ordena verificar diariamente los radios. El registro muestra que todos fueron revisados, pero durante el ejercicio una radio de respaldo no funciona. ¿Se cumplió el procedimiento? ¿Funcionó el control?”

La respuesta esperada es que puede existir evidencia de ejecución sin evidencia de eficacia. Use esa diferencia para introducir **CHECK** y **ACT**.

### Cómo explicar PDCA

Mantenga un único ejemplo durante las cuatro fases:

- **Planificar:** identificar el riesgo de pérdida de comunicaciones y definir respaldo, responsables y tiempo máximo de recuperación.
- **Hacer:** configurar el canal alternativo, instruir al personal y registrar la ejecución.
- **Verificar:** simular la caída del canal principal y medir si el alternativo entra en servicio a tiempo.
- **Actuar:** corregir equipos, responsabilidades o procedimientos según el resultado y volver a probar.

Evite presentar PDCA como cuatro palabras para memorizar. Cada fase debe responder una pregunta:

- PLAN: “¿Qué resultado necesitamos y qué riesgo trataremos?”
- DO: “¿Qué se implementó realmente?”
- CHECK: “¿Qué evidencia demuestra el resultado?”
- ACT: “¿Qué cambiaremos a partir de lo aprendido?”

### Compliance y postura de seguridad

Comunique la diferencia sin despreciar el cumplimiento:

- **Compliance:** demuestra que se satisfacen requisitos definidos.
- **Postura de seguridad:** expresa la capacidad real de prevenir, detectar, responder y recuperarse.
- **Relación correcta:** el cumplimiento aporta disciplina y evidencia; la verificación de eficacia evita que se convierta en cumplimiento aparente.

No diga “compliance no sirve”. Diga: “cumplir es necesario cuando el requisito aplica, pero debemos comprobar que el control protege en condiciones reales”.

### Conducción del trabajo con incidentes

Antes de repartir los expedientes, establezca tres reglas:

1. Trabajar solamente con hechos documentados.
2. Elegir una debilidad prioritaria y un control principal defendible.
3. Proponer una implementación con responsable, alcance, frecuencia, evidencia y prueba de eficacia.

Durante el trabajo, no responda “cuál es el control correcto” de inmediato. Pregunte:

- “¿Qué debilidad decidieron priorizar?”
- “¿Cómo trata ese control la debilidad de forma más directa?”
- “¿Qué vería usted para distinguir que se ejecutó de que funcionó?”

En la retroalimentación, acepte elecciones diferentes cuando la debilidad priorizada y la justificación sean coherentes.

### Frases que debe modelar

Use lenguaje prudente:

> “La debilidad documentada se relaciona con este control; una aplicación eficaz habría podido prevenir, detectar o limitar el incidente.”

Evite:

> “La empresa violó ISO” o “si hubiera estado certificada, el ataque no habría ocurrido”.

Los expedientes sirven para aprender a relacionar evidencia y controles, no para emitir conclusiones de auditoría sin información suficiente.

### Preguntas para hacer a los alumnos

- “¿Qué diferencia hay entre un ticket de parche instalado y un escaneo que confirma el cierre?”
- “¿Qué fase de PDCA falta cuando un error se repite cada mes?”
- “¿Puede una organización cumplir un procedimiento y continuar expuesta? Dé un ejemplo.”
- “¿Qué indicador presentaría al mando para demostrar mejora?”

### Confusiones que debe corregir

- **“CHECK es revisar que exista el documento.”** Corrija: también exige analizar resultados y eficacia.
- **“ACT es reparar el incidente.”** Corrija: incluye eliminar la causa, mejorar el sistema y verificar la corrección.
- **“Más controles siempre significan más seguridad.”** Corrija: los controles deben responder al riesgo y funcionar en el contexto real.
- **“Solo hay una respuesta correcta por incidente.”** Corrija: puede haber varias rutas defendibles; lo obligatorio es justificar con evidencia.

### Distribución sugerida del tiempo

- 10 min: recuperación de conceptos de la Parte 1.
- 25 min: PDCA con un solo escenario operacional.
- 15 min: compliance, postura y evidencia de eficacia.
- 50 min: trabajo por equipos y exposiciones.
- 15 min: comparación de decisiones y retroalimentación.
- 5 min: cierre individual.

### Cierre sugerido

Solicite dos ejemplos breves:

1. Una evidencia de **ejecución**.
2. Una evidencia de **eficacia** del mismo control.

Si los ejemplos son indistinguibles, repita la demostración antes de terminar.

---

## 3. ISO 27001 — Requisitos, Parte 1

**Duración:** 2 horas · [Abrir material de clase](../capas-si/familia-iso27000/iso27001-requisitos-p1.md)

### Resultado que debe quedar

El alumno debe interpretar las cláusulas 4 a 7 como una secuencia de preguntas de gestión y asociarlas con acciones observables en una unidad militar. No necesita recitar el texto normativo.

### Mensaje irrenunciable

> “Antes de operar controles, la organización debe comprender su misión, recibir dirección del mando, planificar según sus riesgos y proporcionar apoyo real.”

### Cómo abrir la clase

Presente la salida de una unidad a una operación y pregunte:

- “¿Qué misión y entorno enfrentamos?” → **§ 4 Contexto**.
- “¿Quién fija la prioridad y asume las decisiones?” → **§ 5 Liderazgo**.
- “¿Qué riesgos, objetivos y acciones se planifican?” → **§ 6 Planificación**.
- “¿Con qué personal, competencia, comunicación y registros?” → **§ 7 Apoyo**.

Después muestre los números de cláusula. La secuencia debe aprenderse por su lógica, no por repetición mecánica.

### Cómo comunicar cada bloque

#### § 4 — Contexto

Idea clave: un control correcto en una organización puede ser insuficiente o innecesario en otra.

Pregunte siempre: “¿Qué misión, información, sistemas, límites y partes interesadas determinan nuestras necesidades de seguridad?”. Use el puesto de mando avanzado para hacer visible que el alcance incluye procesos, personas y terceros, no solo equipos.

#### § 5 — Liderazgo

Idea clave: el área técnica no puede aceptar riesgos de misión ni sostener políticas sin autoridad y recursos del mando.

No reduzca liderazgo a “firmar la política”. Busque conducta visible: asignar recursos, participar en revisiones, respetar los controles bajo presión y decidir sobre riesgos residuales.

#### § 6 — Planificación

Idea clave: los controles se seleccionan porque tratan riesgos y objetivos, no porque estén de moda o aparezcan en una lista.

Convierta “mejorar la seguridad” en un objetivo medible: “verificar el 100 % de equipos con material criptográfico cada 24 horas durante operaciones”. Pregunte qué dato mostraría avance.

#### § 7 — Apoyo

Idea clave: una orden sin tiempo, personal competente, comunicación y evidencia no se convierte en capacidad.

Diferencie:

- **Competencia:** saber ejecutar correctamente una responsabilidad.
- **Concienciación:** comprender la política, la propia contribución y las consecuencias.
- **Comunicación:** definir qué se comunica, a quién, cuándo y cómo.
- **Información documentada:** conservar instrucciones y evidencia adecuadas, controladas y disponibles.

### Analogía útil, con límite

Puede comparar las cláusulas con la preparación de una operación: comprensión del entorno, intención del mando, planeamiento y sostenimiento. Aclare que es una analogía para recordar la lógica; ISO 27001 tiene requisitos específicos y no equivale a una orden de operaciones.

### Preguntas para hacer a los alumnos

- “¿Puede el Técnico aceptar un riesgo que puede comprometer la misión? ¿Quién tiene autoridad?”
- “¿Qué diferencia hay entre asignar responsabilidad a ‘TI’ y asignarla al Suboficial de turno?”
- “¿Cómo convertiría ‘capacitar al personal’ en un objetivo verificable?”
- “¿Qué evidencia mínima permitiría que una persona nueva continúe el proceso correctamente?”

### Confusiones que debe corregir

- **“Contexto es hacer una lista de computadoras.”** Corrija: incluye misión, factores internos y externos, partes interesadas y límites.
- **“La política es suficiente si está firmada.”** Corrija: debe comunicarse, aplicarse y estar respaldada por decisiones del mando.
- **“Todos son responsables de seguridad.”** Matice: todos contribuyen, pero las responsabilidades y autoridades concretas deben asignarse.
- **“Documentar significa producir muchos formatos.”** Corrija: la documentación debe ser proporcional, útil y controlada.

### Distribución sugerida del tiempo

- 10 min: escenario de apertura y mapa 4–7.
- 25 min: contexto y alcance.
- 20 min: liderazgo, política y responsabilidades.
- 25 min: riesgos y objetivos medibles.
- 25 min: recursos, competencia, concienciación, comunicación y evidencia.
- 10 min: aplicación a una jefatura de comunicaciones.
- 5 min: salida individual.

### Cierre sugerido

Asigne a cuatro alumnos una cláusula del 4 al 7. Cada uno debe expresarla sin vocabulario normativo mediante:

> “La pregunta que responde es…; una acción observable sería…; la evidencia sería…”.

---

## 4. ISO 27001 — Requisitos, Parte 2

**Duración:** 3 horas · [Abrir material de clase](../capas-si/familia-iso27000/iso27001-requisitos-p2.md)

### Resultado que debe quedar

El alumno debe explicar cómo las cláusulas 8, 9 y 10 convierten el plan en operación, verifican el desempeño y producen mejora. También debe entender que una auditoría interna busca evidencia y oportunidades de corrección, no culpables.

### Mensaje irrenunciable

> “Un SGSI existe de verdad cuando opera, deja evidencia, se verifica y aprende de sus fallas.”

### Cómo abrir la clase

Retome un objetivo de la sesión anterior: “verificar diariamente los equipos con material criptográfico”. Pregunte:

1. “¿Cómo demostramos que se ejecuta?” → **§ 8 Operación**.
2. “¿Cómo sabemos si funciona?” → **§ 9 Evaluación**.
3. “¿Qué hacemos si falla?” → **§ 10 Mejora**.

Relacione luego § 8, § 9 y § 10 con DO, CHECK y ACT.

### Cómo comunicar § 8 — Operación

Subraye la brecha entre el plan y la práctica. Un procedimiento aprobado no prueba operación. Solicite evidencias concretas: registro firmado, configuración aplicada, ticket cerrado, lista de cuentas revisada o prueba de restauración.

La evaluación y el tratamiento de riesgos deben repetirse cuando cambian la misión, los sistemas o las amenazas. Use la incorporación de un sistema C2 nuevo: antes de conectarlo deben revisarse riesgos, controles, responsables y dependencias.

### Cómo comunicar § 9 — Evaluación del desempeño

Diferencie tres actividades:

- **Seguimiento y medición:** observar indicadores y resultados definidos.
- **Auditoría interna:** obtener evidencia objetiva y compararla con criterios.
- **Revisión por la dirección:** usar resultados, cambios y riesgos para tomar decisiones de mando.

Defina al auditor interno como alguien que formula preguntas, muestrea evidencia y comunica hallazgos con independencia. No lo presente como inspector que intenta sorprender o sancionar.

Frase útil:

> “La auditoría no pregunta solamente ‘¿tiene un procedimiento?’, sino ‘¿se aplica, deja evidencia y produce el resultado previsto?’”.

### Cómo comunicar § 10 — Mejora

Explique la secuencia completa:

1. Contener o corregir el problema inmediato.
2. Determinar su causa.
3. Evaluar si puede repetirse en otro lugar.
4. Implementar la acción correctiva.
5. Verificar que la acción fue eficaz.

Ejemplo: cerrar una cuenta de una persona trasladada corrige el problema inmediato. Revisar y modificar el proceso de bajas para que ninguna cuenta permanezca activa elimina la causa sistémica. Verificar el listado un mes después demuestra eficacia.

### Actividad recomendada

Entregue un hallazgo: “tres cuentas de personal trasladado siguen activas”. Pida a los grupos responder:

- ¿Cuál es la corrección inmediata?
- ¿Qué evidencia buscarían para conocer la causa?
- ¿Qué acción correctiva evitaría la repetición?
- ¿Cómo comprobarían su eficacia?
- ¿Quién debe conocer el resultado?

No acepte “capacitar” como respuesta automática. La acción debe corresponder a la causa encontrada.

### Preguntas para hacer a los alumnos

- “¿Qué diferencia hay entre auditoría interna y certificación?”
- “¿Por qué quien diseñó y opera un control no debería ser su único auditor?”
- “¿Cerrar el incidente significa que se eliminó su causa?”
- “¿Qué decisión corresponde al mando después de revisar los resultados?”

### Confusiones que debe corregir

- **“La cláusula 9 trata de conseguir el certificado.”** Corrija: trata del desempeño del SGSI; la certificación externa es otra actividad.
- **“Una no conformidad significa que todo el SGSI fracasó.”** Corrija: señala un requisito no satisfecho y activa corrección y aprendizaje.
- **“Auditar es revisar todos los registros.”** Corrija: se planifica el alcance y se obtiene evidencia suficiente mediante muestreo y otras técnicas.
- **“Acción correctiva es volver a hacer bien la tarea.”** Corrija: debe tratar la causa para evitar repetición.

### Distribución sugerida del tiempo

- 15 min: recapitulación y puente 8–10 con PDCA.
- 35 min: operación y reevaluación de riesgos.
- 45 min: seguimiento, medición, auditoría y revisión por la dirección.
- 10 min: pausa.
- 35 min: no conformidad, corrección, causa y mejora.
- 30 min: actividad de cuentas activas.
- 10 min: puesta en común.
- 10 min: cierre y comprobación.

### Cierre sugerido

Muestre la secuencia **plan → registro → medición → hallazgo → acción correctiva → verificación** y pida a un alumno ubicar § 8, § 9 y § 10. Debe explicar el porqué, no solo asignar el número.

---

## 5. ISO 27002 — Controles, Parte 1

**Duración:** 1 hora · [Abrir material de clase](../capas-si/familia-iso27000/iso27002-controles-p1.md)

### Resultado que debe quedar

El alumno debe diferenciar ISO 27001, su Anexo A e ISO 27002, y reconocer que los 93 controles se organizan en cuatro temas complementarios: organizacional, personas, físico y tecnológico.

### Mensaje irrenunciable

> “ISO 27001 exige seleccionar y justificar controles; el Anexo A ofrece el catálogo de referencia; ISO 27002 orienta cómo implementarlos.”

### Cómo abrir la clase

Pregunte:

> “Si el análisis indica riesgo de acceso no autorizado a una sala de comunicaciones, ¿basta con instalar una cerradura?”

Conduzca las respuestas hacia cuatro dimensiones:

- quién autoriza y revisa (**organizacional**),
- quién está habilitado e instruido (**personas**),
- cómo se protege y registra el ingreso (**físico**),
- cómo se protegen los sistemas dentro de la sala (**tecnológico**).

Así aparecen los cuatro temas antes de mostrar sus números.

### Cómo explicar la relación 27001–27002

Use una necesidad concreta, no una analogía aislada:

- El riesgo exige controlar accesos.
- ISO 27001 requiere tratar el riesgo, seleccionar controles y justificar la decisión.
- El Anexo A permite identificar controles relacionados.
- ISO 27002 proporciona orientación para implementarlos en el contexto propio.

Repita que ISO 27002 es guía y que los 93 controles no se aplican indiscriminadamente.

### Los cuatro temas que deben recordar

- **Tema 5 — Organizacional:** políticas, procesos, responsabilidades, proveedores e incidentes.
- **Tema 6 — Personas:** selección, obligaciones, formación, concienciación y reporte.
- **Tema 7 — Físico:** perímetros, ingreso, monitoreo, equipos e instalaciones.
- **Tema 8 — Tecnológico:** autenticación, configuración, malware, logs, redes y datos.

No dedique la hora a memorizar cantidades. Menciónelas para describir la estructura, pero evalúe la capacidad de clasificar y combinar controles.

### Preguntas para hacer a los alumnos

- “¿Una política de contraseñas es organizacional o tecnológica? ¿Qué parte técnica la hace efectiva?”
- “¿Qué control de personas complementa un registro de incidentes?”
- “¿Por qué un control físico puede proteger información digital?”
- “¿Qué tema faltaría si instalamos autenticación, pero nadie revisa accesos ni sabe reportar anomalías?”

### Confusiones que debe corregir

- **“ISO 27002 certifica a la organización.”** Corrija: ISO 27002 es una guía de controles; los requisitos del SGSI están en ISO 27001.
- **“El Anexo A y la guía ISO 27002 son documentos idénticos.”** Corrija: comparten referencias de controles, pero ISO 27002 desarrolla la orientación.
- **“Los controles importantes son los tecnológicos.”** Corrija: las cuatro categorías se complementan; una herramienta sin proceso y personas puede ser ineficaz.
- **“Los 93 controles son un checklist obligatorio.”** Corrija: deben considerarse, seleccionarse y justificarse según riesgos y contexto.

### Distribución sugerida del tiempo

- 5 min: situación de apertura.
- 15 min: relación ISO 27001, Anexo A e ISO 27002.
- 20 min: cuatro temas con un mismo escenario.
- 15 min: clasificación oral de ejemplos.
- 5 min: comprobación final.

### Cierre sugerido

Lea cuatro acciones y pida que identifiquen el tema y el control complementario que faltaría. Una respuesta completa debe mostrar que los temas trabajan juntos.

---

## 6. ISO 27002 — Controles, Parte 2

**Duración:** 4 horas · [Abrir material de clase](../capas-si/familia-iso27000/iso27002-controles-p2.md)

### Resultado que debe quedar

El alumno debe poder proponer controles combinados para un riesgo y explicar que la **Declaración de Aplicabilidad (SoA)** documenta qué controles se incluyen o excluyen, por qué y en qué estado se encuentran.

### Mensaje irrenunciable

> “Un control se selecciona por el riesgo, se adapta al contexto, se asigna a un responsable y se conserva evidencia de su funcionamiento.”

### Cómo abrir la clase

Presente la pérdida de un radio con material criptográfico y divida la pizarra en cuatro:

- **Organizacional:** procedimiento de asignación, reporte y respuesta.
- **Personas:** instrucción y práctica del operador.
- **Físico:** custodia, transporte y registro de entrega.
- **Tecnológico:** borrado, cambio de claves, autenticación o capacidades del equipo.

Pregunte qué ocurriría si se elimina una de las cuatro columnas. El objetivo es demostrar que la defensa se construye como sistema, no como control aislado.

### Cómo enseñar controles concretos

Para cada control presentado, formule cinco preguntas:

1. ¿Qué riesgo trata?
2. ¿Quién es responsable?
3. ¿Qué acción observable exige?
4. ¿Qué evidencia deja?
5. ¿Cómo se prueba su eficacia?

Ejemplo con **8.15 Registro de actividad**:

- Riesgo: acciones no autorizadas sin trazabilidad.
- Responsable: administrador del sistema y responsable de revisión.
- Acción: generar, proteger y revisar eventos definidos.
- Evidencia: logs, alertas y registros de revisión.
- Eficacia: una prueba controlada genera la alerta esperada y el responsable responde dentro del tiempo establecido.

Evite recorrer largas listas sin contexto. Seleccione controles representativos y haga que los alumnos reutilicen las cinco preguntas.

### Cómo comunicar el SoA

Preséntelo como el puente entre riesgo y control, no como otra tabla administrativa:

> “El SoA permite responder qué controles decidimos aplicar, cuáles excluimos, por qué tomamos esas decisiones y cuál es su estado.”

Diferencie tres situaciones:

- **Aplicable e implementado:** existe justificación, acción y evidencia.
- **Aplicable en proceso:** se reconoce la necesidad y existe un plan de tratamiento.
- **No aplicable:** la exclusión está respaldada por alcance, contexto y riesgos.

Subraye que “no tenemos presupuesto” no convierte automáticamente un control necesario en “no aplicable”. Si el riesgo exige el control y aún no está implementado, debe registrarse la brecha y tratarse.

### Actividad recomendada

Use una mini-SoA para una jefatura de comunicaciones. Asigne a cada grupo cinco controles del material y solicite:

- decisión de aplicabilidad,
- justificación basada en contexto o riesgo,
- estado,
- evidencia que esperaría encontrar,
- control complementario de otro tema.

Incluya al menos un control realmente no aplicable, como filtrado web para terminales sin conectividad a Internet, y otro necesario pero todavía no implementado. La comparación permite detectar exclusiones incorrectas.

### Preguntas para hacer a los alumnos

- “¿Qué diferencia hay entre ‘no aplica’ y ‘aplica, pero no está implementado’?”
- “¿Qué riesgo justifica seleccionar este control?”
- “¿Qué otro tema debe acompañarlo para que funcione?”
- “¿Qué evidencia pediría un auditor y qué prueba demostraría eficacia?”
- “¿Cuándo debe revisarse una exclusión del SoA?”

### Confusiones que debe corregir

- **“El SoA es copiar los 93 controles y marcar sí o no.”** Corrija: debe justificar decisiones y reflejar el estado real frente a riesgos y alcance.
- **“Excluir un control elimina el riesgo.”** Corrija: la exclusión describe aplicabilidad; el riesgo debe gestionarse de todos modos.
- **“Tener logs implementa el monitoreo.”** Corrija: los eventos deben definirse, protegerse, revisarse y generar respuesta.
- **“Capacitación resuelve cualquier control de personas.”** Corrija: la acción debe responder a una necesidad de competencia o conducta y evaluarse.

### Distribución sugerida del tiempo

- 20 min: escenario de radio y combinación de cuatro temas.
- 60 min: controles representativos mediante las cinco preguntas.
- 10 min: pausa.
- 35 min: concepto, decisiones y uso del SoA.
- 55 min: actividad de mini-SoA.
- 30 min: exposición y contraste de justificaciones.
- 20 min: respuesta a incidentes usando los cuatro temas.
- 10 min: cierre y comprobación.

### Cierre sugerido

Entregue un riesgo nuevo y pida una respuesta de sesenta segundos:

> “Control principal, control complementario, responsable, evidencia y forma de probar eficacia.”

No acepte respuestas que solo nombren herramientas.

---

## 7. ISO 27005 — Gestión del Riesgo, Parte 1

**Duración:** 4 horas · [Abrir material de clase](../capas-si/familia-iso27000/iso27005-riesgo-p1.md)

### Resultado que debe quedar

El alumno debe construir un escenario de riesgo coherente conectando **activo, amenaza, vulnerabilidad y consecuencia para la misión**, además de reconocer la gestión del riesgo como un ciclo de decisión del mando.

### Mensaje irrenunciable

> “Una amenaza no es un riesgo por sí sola y una vulnerabilidad tampoco: el riesgo aparece cuando una amenaza puede aprovechar una debilidad y causar una consecuencia sobre un activo valioso.”

### Cómo abrir la clase

Escriba tres elementos desordenados:

- radio con material criptográfico,
- captura por el adversario,
- operador sin procedimiento de borrado.

Pregunte cuál es el activo, cuál la amenaza y cuál la vulnerabilidad. Después pida formular la consecuencia completa para la misión. Mantenga este ejemplo para introducir el proceso.

### Cómo comunicar el proceso de riesgo

No presente las seis etapas como una lista aislada. Formule una pregunta por etapa:

- **Contexto:** ¿qué misión, alcance y criterios orientan la decisión?
- **Identificación:** ¿qué puede suceder, sobre qué activo y debido a qué debilidad?
- **Análisis:** ¿qué probabilidad e impacto estimamos y por qué?
- **Evaluación:** ¿el nivel está dentro de los criterios aceptables?
- **Tratamiento:** ¿qué decisión y controles se aplicarán?
- **Monitoreo:** ¿cambió el contexto y funcionan los controles?

Repita que el proceso vuelve a comenzar cuando cambian la misión, los activos, las amenazas o la eficacia observada.

### Cómo enseñar el inventario de activos

Empiece por la **información y la misión**, no por el hardware. Pregunte: “¿Qué información o servicio perdería valor si este equipo desaparece?”. Así evitará que el inventario se convierta en una lista de seriales sin relación con la criticidad.

Incluya datos, hardware, software, personas, instalaciones y servicios. Valore confidencialidad, integridad y disponibilidad por separado: un activo puede ser crítico por una dimensión y no por las otras.

Ejemplo: para un enlace de mando durante una operación, disponibilidad e integridad pueden ser críticas; para material criptográfico vigente, la confidencialidad es igualmente decisiva.

### Cómo diferenciar amenaza y vulnerabilidad

Use esta prueba verbal:

- La **amenaza** puede expresarse como algo que ocurre o que un actor hace.
- La **vulnerabilidad** puede expresarse como una debilidad existente.
- La **consecuencia** explica el efecto sobre la misión.

Transforme respuestas vagas:

- “Malware” → amenaza todavía incompleta: infección o ejecución de malware.
- “No hay antivirus” → vulnerabilidad o ausencia de control.
- “Se cae el sistema” → consecuencia; falta explicar activo, amenaza y debilidad.
- “Riesgo alto” → valoración; todavía falta redactar el escenario.

Una fórmula útil para redactar:

> “Debido a **[vulnerabilidad]**, **[amenaza]** podría afectar **[activo]** y causar **[consecuencia sobre la misión]**.”

### Actividad recomendada

Entregue a cada grupo un activo diferente: radio táctico, terminal C2, enlace satelital, sala de comunicaciones, operador habilitado o plan de frecuencias. Solicite:

1. valor según confidencialidad, integridad y disponibilidad;
2. una amenaza deliberada, accidental o ambiental;
3. una vulnerabilidad realista;
4. un escenario de riesgo redactado con la fórmula;
5. una evidencia que permitiría confirmar la vulnerabilidad.

En la puesta en común, corrija primero la lógica del escenario y después el vocabulario.

### Preguntas para hacer a los alumnos

- “¿Puede existir una vulnerabilidad sin que haya un riesgo relevante en este contexto?”
- “¿Por qué una persona o un servicio también son activos?”
- “¿Qué cambia en la valoración del mismo radio entre almacén y operación?”
- “¿Qué evidencia demuestra que la vulnerabilidad existe?”
- “¿Quién tiene autoridad para aceptar el efecto sobre la misión?”

### Confusiones que debe corregir

- **“Riesgo es lo mismo que amenaza.”** Corrija construyendo la cadena completa.
- **“Solo se inventarían equipos.”** Corrija incluyendo información, servicios, personas, procesos e instalaciones.
- **“El valor del activo es su precio.”** Corrija: interesa el impacto de perder confidencialidad, integridad o disponibilidad para la misión.
- **“Una lista de riesgos se hace una vez.”** Corrija: el inventario y los escenarios deben revisarse ante cambios.
- **“El Técnico decide qué riesgo aceptar.”** Corrija: el Técnico aporta evidencia y alternativas; acepta quien tiene autoridad sobre la misión y los recursos.

### Distribución sugerida del tiempo

- 20 min: situación inicial y distinciones fundamentales.
- 30 min: proceso completo de gestión del riesgo.
- 50 min: activos, inventario y valoración CID.
- 10 min: pausa.
- 40 min: amenazas y sus categorías.
- 40 min: vulnerabilidades y redacción de escenarios.
- 35 min: actividad por grupos.
- 10 min: retroalimentación.
- 5 min: salida individual.

### Cierre sugerido

Muestre un activo y solicite a tres alumnos construir consecutivamente la cadena: amenaza, vulnerabilidad y consecuencia. Un cuarto alumno debe redactar el riesgo completo y explicar qué información todavía faltaría para valorarlo.

---

## 8. ISO 27005 — Gestión del Riesgo, Parte 2

**Duración:** 1 hora · [Abrir material de clase](../capas-si/familia-iso27000/iso27005-riesgo-p2.md)

### Resultado que debe quedar

El alumno debe justificar probabilidad e impacto, calcular el nivel con la matriz 3×3, seleccionar una opción de tratamiento y convertir la decisión en una acción con responsable y plazo.

### Mensaje irrenunciable

> “La matriz ayuda a priorizar; no decide por nosotros. La decisión debe justificarse, asignarse y revisarse.”

### Cómo abrir la clase

Presente el escenario del radio capturado y pida dos votaciones rápidas: probabilidad e impacto. Cuando aparezcan valores diferentes, no revele una cifra “correcta”. Pregunte qué hechos o criterios sustentan cada valoración.

Use la discrepancia para mostrar que la matriz exige criterios comunes y justificación; multiplicar números sin contexto produce precisión aparente.

### Cómo enseñar la matriz

Mantenga separados los conceptos:

- **Probabilidad:** posibilidad de que el escenario ocurra bajo el contexto definido.
- **Impacto:** consecuencia para la misión si ocurre.
- **Nivel:** resultado usado para comparar con criterios de aceptación y priorizar.

No permita calificar primero y redactar el riesgo después. La secuencia correcta es escenario claro → criterios → probabilidad → impacto → nivel.

### Cómo comunicar las cuatro opciones

- **Modificar o mitigar:** reducir probabilidad o impacto mediante controles.
- **Retener o aceptar:** decisión autorizada y documentada, con riesgo residual y fecha de revisión; no significa ignorar.
- **Compartir o transferir:** distribuir consecuencias o responsabilidades mediante un tercero; la organización conserva responsabilidad sobre su misión e información.
- **Evitar:** eliminar la actividad o condición que origina el riesgo cuando la misión lo permite.

Explique que las opciones pueden combinarse. Después de mitigar queda un **riesgo residual** que debe volver a evaluarse y, si corresponde, aceptarse por la autoridad adecuada.

### Conducción del ejercicio

Reserve la mayor parte de la hora para que cada alumno complete una fila. Circule haciendo preguntas, no dando el puntaje:

- “¿Qué evidencia sustenta que la probabilidad es alta?”
- “¿Su impacto describe una consecuencia sobre la misión?”
- “¿El control reduce probabilidad, impacto o ambos?”
- “¿Quién tiene capacidad real para ejecutar la acción?”
- “¿Cómo sabremos que quedó implementada y fue eficaz?”

La fila final debe contener riesgo, nivel, tratamiento, acción concreta, responsable, fecha y estado. Si falta responsable o plazo, todavía no existe un plan ejecutable.

### Preguntas para hacer a los alumnos

- “¿Dos riesgos con puntuación 6 son necesariamente iguales?”
- “¿Aceptar significa no hacer nada?”
- “¿Un contrato transfiere toda la responsabilidad por la información?”
- “¿Qué riesgo queda después de implementar el control?”
- “¿Cuándo debe reevaluarse la decisión?”

### Confusiones que debe corregir

- **“La multiplicación produce una medida exacta.”** Corrija: es una escala cualitativa para priorizar con criterios consistentes.
- **“Todo riesgo alto se acepta si falta presupuesto.”** Corrija: la autoridad debe decidir y justificar; la falta de recursos puede requerir controles compensatorios o escalamiento.
- **“Transferir elimina el riesgo.”** Corrija: puede compartir consecuencias, pero quedan dependencias y responsabilidad.
- **“Mitigar lleva el riesgo a cero.”** Corrija: normalmente queda riesgo residual que se revisa y decide.
- **“El control propuesto es ‘capacitar’.”** Pida responsable, contenido, población, momento, evaluación y evidencia.

### Distribución sugerida del tiempo

- 5 min: escenario y votación inicial.
- 10 min: matriz, criterios y justificación.
- 10 min: cuatro tratamientos y riesgo residual.
- 25 min: ejercicio individual y fila del plan.
- 7 min: comparación de dos decisiones.
- 3 min: cierre.

### Cierre sugerido

Pida a un alumno informar el riesgo como si hablara con el Comandante en treinta segundos:

> “Escenario, nivel y justificación, tratamiento recomendado, responsable, plazo y riesgo residual esperado.”

La respuesta debe permitir tomar una decisión; si solo contiene el número de la matriz, está incompleta.

---

## Lista de verificación antes de cada clase

- [ ] Puedo expresar en una frase el aprendizaje irrenunciable de la sesión.
- [ ] Tengo un escenario militar sencillo que conecta el concepto con la misión.
- [ ] Sé qué pregunta usaré para activar conocimientos previos.
- [ ] He elegido qué detalles son esenciales y cuáles son solo referencia.
- [ ] Tengo una pregunta de transferencia para comprobar comprensión.
- [ ] Puedo distinguir evidencia de ejecución y evidencia de eficacia.
- [ ] He reservado tiempo para que los alumnos expliquen, decidan o construyan algo.
- [ ] El cierre recupera el hilo contexto → riesgo → control → evidencia → mejora.

## Señal final de dominio de la unidad

Al terminar las ocho sesiones, presente este caso sin indicar la norma:

> “Una unidad incorpora un terminal C2 nuevo. No está claro quién autoriza su conexión, qué riesgos introduce, qué configuración debe usar ni cómo se comprobará que opera de forma segura.”

El alumno domina la unidad si puede proponer, en orden:

1. definir contexto, alcance y responsables;
2. identificar y valorar riesgos;
3. decidir el tratamiento;
4. seleccionar y justificar controles;
5. implementar acciones y conservar evidencia;
6. medir, auditar, corregir y mejorar.

No necesita mencionar todas las normas para demostrar comprensión. Después de construir la solución, pídale ubicar dónde aportan ISO/IEC 27001, 27002 y 27005.
