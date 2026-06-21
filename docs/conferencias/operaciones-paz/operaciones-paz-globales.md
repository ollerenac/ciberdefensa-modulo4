---
# Horas asignadas: 2 hrs
# Tipo: Conferencia
---

# Operaciones de Paz en Entornos Globales

> **Duración:** 2 horas | **Asignatura:** Conferencias Magistrales | **Unidad:** Operaciones de Paz

## Objetivo de la clase

Al finalizar esta conferencia, el Técnico habrá comprendido qué demandas específicas de ciberseguridad presenta un teatro de operaciones multinacional ONU, qué protocolos de coordinación con aliados afectan el intercambio de información, y qué principios de clasificación de información aplican cuando se opera en coalición.

---

## El Perú en Operaciones de Paz — Contexto

El Ejército Peruano no es un observador externo de las operaciones de paz de Naciones Unidas. Ha participado activamente en misiones en Haití (MINUSTAH), en la República Democrática del Congo (MONUSCO) y en otros teatros internacionales. Técnicos de comunicaciones peruanos han operado en esos entornos, mantenido redes de campaña, y gestionado sistemas de mando y control junto a contingentes de países aliados. Este tema no es abstracto: es parte de la historia reciente del Ejército y de las exigencias reales que puede enfrentar cualquier Técnico en su carrera.

Estar desplegado en una misión de paz tiene implicaciones técnicas que van mucho más allá de las que se encuentran en operaciones domésticas. El Técnico opera en un teatro extranjero, con aliados de múltiples países, sobre una infraestructura de comunicaciones que en muchos casos no controla ni conoce completamente. Las amenazas de seguridad son simultáneamente físicas y cibernéticas, y el entorno operacional impone presiones que a menudo llevan a tomar atajos de seguridad: instalar una red de campaña rápidamente sin documentación, conectar equipos a redes locales de cuya seguridad no se tiene certeza, compartir información bajo presión operativa sin seguir los protocolos habituales.

La ciberseguridad en operaciones de paz es más compleja que en operaciones nacionales por una razón estructural: en el país, el Técnico opera dentro de una cadena de mando única y sobre infraestructura propia. En coalición, la información fluye por múltiples redes de distintos países, debe ser compartida pero también protegida de actores que pueden estar físicamente en el mismo teatro, y las decisiones de seguridad tienen consecuencias que afectan a contingentes aliados, no solo al propio. Esa complejidad es el tema central de esta conferencia.

---

## Amenazas Cibernéticas Específicas del Teatro Multinacional

Las amenazas que emergen cuando se opera en coalición no son idénticas a las amenazas que se enfrentan en redes nacionales. El contexto cambia la naturaleza del riesgo, y el Técnico que espera encontrar los mismos problemas de siempre puede quedar expuesto a vectores que no anticipó.

La primera amenaza característica del teatro multinacional es la red no confiable de origen desconocido. En una operación doméstica, el Técnico conoce la infraestructura sobre la que trabaja: la instaló, la mantiene, y sabe qué equipos la componen. En un teatro de operaciones en África subsahariana o en el Medio Oriente, la infraestructura de telecomunicaciones local puede estar comprometida, monitorizada por actores hostiles, o simplemente diseñada con estándares de seguridad muy diferentes a los peruanos.

!!! example "Escenario: La red del campamento base"
    El contingente peruano desplegado en un teatro africano recibe acceso a la red del campamento base de la misión ONU para transmitir informes operacionales diarios. La red fue instalada por un contratista civil local y no hay documentación técnica disponible. El Técnico Saavedra necesita enviar los informes ese día. ¿Qué información es seguro transmitir por esa red? ¿Qué información debería reservarse para el canal de comunicaciones satelital del contingente, que sí controla?

La segunda amenaza es la inteligencia por acumulación, conocida en el ámbito anglosajón de inteligencia como OSINT avanzado. En un teatro internacional, adversarios sofisticados no necesitan interceptar un único mensaje para obtener información de valor. Pueden correlacionar patrones de comunicaciones de radio, metadatos de tráfico de red, frecuencias de operación, y tiempos de transmisión para inferir la estructura, el tamaño y las capacidades de la fuerza. Una comunicación individual puede parecer completamente inocua; el patrón de centenares de comunicaciones durante semanas revela información de inteligencia operacional que ningún mensaje individual contendría.

!!! example "Escenario: El patrón que revela la operación"
    Durante una misión de observación, el contingente peruano transmite informes de situación cada cuatro horas. Los días previos a una operación de búsqueda y rescate, la frecuencia de transmisión aumenta a cada hora, el volumen de datos transmitidos se triplica, y los mensajes de voz aumentan entre el Técnico y el cuartel general de la misión. Un adversario que monitoriza el espectro radioeléctrico local no necesita descifrar el contenido de esas transmisiones para saber que algo significativo está por ocurrir. La seguridad de las comunicaciones no es solo sobre cifrado: es sobre disciplina en los patrones de uso.

La tercera amenaza es la de dispositivos comprometidos de aliados, y es la más delicada porque involucra la relación con contingentes amigos. El principio "confianza pero verifica" aplica incluso en operaciones de coalición. No se trata de desconfiar políticamente de los aliados — la confianza política entre contingentes es el fundamento de la misión. Se trata de reconocer que un dispositivo infectado de un contingente aliado puede comprometer la red compartida independientemente de la voluntad de ese contingente. El malware no distingue entre amigos y adversarios.

!!! example "Escenario: El USB del colega aliado"
    El Técnico Saavedra recibe de un colega de un contingente de otro país un USB con mapas de reconocimiento actualizados de un área que la patrulla peruana recorrerá al día siguiente. El colega garantiza que el USB es seguro. ¿Qué hace el Técnico Saavedra? La respuesta no está en evaluar si el colega miente: está en el protocolo. Un USB de origen externo no se conecta a los sistemas del contingente sin pasar por el proceso de verificación establecido, independientemente de quién lo entregue o qué urgencia haya. La norma protege tanto al contingente como al colega aliado.

---

## Coordinación con Aliados — El Marco de Intercambio de Información

Las misiones multinacionales ONU operan bajo marcos de clasificación de información propios de Naciones Unidas, que no son idénticos a los marcos nacionales de cada país contribuyente. El sistema ONU tiene sus propias categorías: UN Confidential, UN Secret, UN Restricted, y Public Domain. Estas categorías no se mapean directamente a los equivalentes nacionales peruanos, lo que crea una tensión operacional que el Técnico encontrará en cualquier misión multinacional.

La pregunta que surge con frecuencia es: cuando el contingente peruano clasifica un documento como "Secreto" según su sistema nacional, y ese documento debe compartirse con el cuartel general ONU o con un contingente aliado, ¿qué nivel de clasificación rige? La respuesta práctica — aunque no siempre documentada explícitamente en los acuerdos de la misión — es que el documento mantiene el nivel más alto de clasificación que aplique bajo cualquiera de los sistemas involucrados. Si "Secreto" peruano es equivalente a "UN Confidential", el documento se trata como Secreto en los sistemas nacionales y como UN Confidential en los sistemas de la misión. No se "degrada" por conveniencia operacional.

El principio de "need to know" en coalición presenta una complejidad adicional. En una misión nacional, el Técnico comparte información con su cadena de mando directa, que es una línea clara. En coalición, la información fluye también lateralmente hacia contingentes aliados que trabajan por el mismo objetivo de la misión. Pero ese objetivo común no significa que todo aliado necesite toda la información. Un contingente que opera en el sector norte de la misión no necesariamente necesita acceder a los planes de operaciones del sector sur. El "need to know" en coalición requiere que el Técnico — o más frecuentemente su Oficial de Seguridad — evalúe cada solicitud de información en función de la función operacional del solicitante, no solo de su afiliación a la misión.

Cuando el Técnico recibe una solicitud de información de un oficial de un contingente aliado, el protocolo práctico tiene tres pasos que no deben abreviarse por presión operacional.

!!! tip "Protocolo de solicitud de información en coalición"
    Primero, verificar la identidad y afiliación del solicitante: la credencial de la misión ONU es el documento de referencia, no el uniforme ni el idioma. Segundo, confirmar el nivel de clasificación de la información solicitada y compararlo con el nivel de autorización del solicitante según los acuerdos de la misión. Tercero, escalar al Oficial de Inteligencia o al Oficial de Seguridad si la solicitud excede el nivel habitual de intercambio o si hay cualquier ambigüedad sobre la autorización. En una misión ONU, escalar no es debilidad operacional: es procedimiento correcto.

---

## Clasificación de Información en Coalición

En una misión de paz, el Técnico de comunicaciones maneja simultáneamente tres tipos de información con marcos de clasificación distintos: la información nacional del contingente peruano, regida por la normativa peruana de clasificación; la información ONU de la misión, regida por el marco de clasificación de Naciones Unidas; y la información compartida con aliados de terceros países, que puede estar regida por acuerdos bilaterales o multilaterales que el Técnico probablemente no conoce en detalle — y no necesita conocer en detalle, siempre que siga el principio de tratar la información al nivel más restrictivo que aplique.

Ese principio — tratar al nivel más restrictivo — es la regla operativa que simplifica las decisiones en campo. Si un documento contiene una sección de información nacional Secreta y una sección de información ONU UN Confidential, el documento completo se trata como Secreto en los sistemas nacionales y se maneja con los protocolos correspondientes. No se "abre" la clasificación del documento porque parte de su contenido sea de menor restricción.

!!! example "Escenario: El cierre de misión y el inventario de datos"
    El contingente peruano en una misión de paz en África Central prepara el repliegue hacia el Perú. El Técnico Ramos tiene la responsabilidad de auditar el servidor de comunicaciones del puesto de mando de campaña antes de su desmantelamiento. En el servidor encuentra: informes de situación diarios del contingente peruano (información nacional), informes de la reunión de comandantes de la misión ONU (información ONU), registros de conversaciones con civiles locales colaboradores de la misión — nombres, ubicaciones, números de contacto (información sensible sobre personas que colaboraron con la misión), y copias de mapas de reconocimiento compartidos por un contingente aliado europeo.

    ¿Qué archivos pueden volver al Perú? ¿Cuáles deben destruirse en teatro? ¿Cuáles deben entregarse al cuartel general ONU? ¿Quién toma esa decisión? Sin un inventario previo y sin protocolos de fin de misión establecidos desde el inicio del despliegue, este proceso se convierte en una gestión de riesgo bajo presión de tiempo — exactamente la situación que el Técnico debe evitar.

La lección práctica del escenario anterior no es técnica: es de planificación. Los protocolos de clasificación, inventario y destrucción de información deben establecerse al inicio del despliegue, no en las 72 horas previas al repliegue. El Técnico que llega a una misión y pregunta "¿cómo manejamos el fin de misión para los datos?" en el primer día está haciendo su trabajo. El que espera al último día está improvisando bajo presión.

---

## Aplicación en Contexto Castrense

Las lecciones de ciberseguridad de misiones de paz son transferibles al entorno nacional, y esta transferibilidad es una de las razones por las que la experiencia internacional es tan valiosa para el desarrollo profesional del Técnico. Un Técnico que ha operado en coalición con aliados de distintos países y culturas de seguridad regresa con una comprensión de controles de acceso, gestión de clasificación y coordinación de seguridad que supera con mucho lo que puede aprender en un ejercicio doméstico. El entorno multinacional es exigente precisamente porque expone al Técnico a actores con culturas de seguridad diferentes — y el Técnico aprende a mantener sus propios estándares independientemente del estándar de quienes lo rodean.

Los principios que rigen la ciberseguridad en una misión ONU en África Central no son distintos de los que rigen la ciberseguridad en el Batallón de Comunicaciones N°3 en el Perú. La clasificación de información, el control de acceso, el principio de "need to know", el protocolo de destrucción de medios al cierre de operaciones — son los mismos fundamentos. La escala cambia. La presión operacional cambia. La complejidad de los aliados cambia. Pero quien domina los fundamentos en su unidad puede aplicarlos en cualquier teatro. Y quien los improvisa en su unidad los improvisará también en el teatro internacional.

Los dos escenarios siguientes están diseñados para discusión en clase. No tienen una respuesta única correcta. Su propósito es que los alumnos articulen el razonamiento que llevaría a la decisión — no solo la decisión en sí.

**Escenario A — El USB del contingente aliado**

El Técnico Saavedra, desplegado en una misión de la ONU en la República Democrática del Congo como parte de MONUSCO, recibe durante una reunión de coordinación un USB de un colega del contingente de otro país. El colega le explica que el USB contiene los mapas de reconocimiento actualizados de la zona de patrullaje que el contingente peruano cubrirá al día siguiente — información que el Técnico Saavedra necesita para preparar las rutas de comunicación. "Todos lo están usando", dice el colega. El Técnico Saavedra no tiene autorización explícita de su superior para conectar el USB a los sistemas de comunicaciones del contingente peruano.

¿Qué hace el Técnico Saavedra? La presión operacional es real: los mapas son necesarios para la misión del día siguiente. La restricción también es real: no tiene autorización, y el USB proviene de un sistema externo de cuya seguridad no tiene certeza. ¿Cómo resuelve la tensión entre la necesidad operacional y el protocolo de seguridad? ¿A quién escala? ¿En qué plazo?

**Escenario B — El disco duro del servidor de campaña**

Al cierre de una misión de paz, el Técnico Ramos descubre que el disco duro del servidor de comunicaciones del puesto de mando contiene registros de conversaciones con civiles locales colaboradores de la misión: nombres completos, números de teléfono, ubicaciones de domicilio. Algunos de esos civiles colaboraron con información sobre la ubicación de grupos armados en la zona. El disco debe entregarse al cuartel general ONU mañana para su inventario final. El Técnico Ramos no tiene un protocolo establecido para este caso específico, y su superior jerárquico no está disponible porque está en la reunión final de transferencia de mando.

¿Qué pasos debe seguir el Técnico Ramos? La información en el disco puede comprometer la seguridad de personas reales si cae en manos equivocadas durante el proceso de transferencia. ¿Actúa de forma autónoma para proteger esa información? ¿Espera a su superior? ¿Escala al Oficial de Seguridad de la misión ONU? ¿Qué consideraciones éticas y operacionales pesan sobre cada opción?

---

*Siguiente: Fin de la Asignatura 4 — Conferencias Magistrales*
