---
search:
  exclude: true
meta:
  - name: robots
    content: noindex, nofollow
---

# Guía del Instructor — PDCA para el Control de Equipos Conectados

!!! danger "Solo para instructores"
    Este documento contiene las decisiones técnicas esperadas, rutas de respuesta aceptables y criterios de retroalimentación del ejercicio integrador de Generalidades — Parte 2. No mostrar durante el trabajo de los alumnos.

[Abrir el material de clase](../capas-si/familia-iso27000/iso27000-generalidades-p2.md#ejercicio-integrador-mejorar-el-control-de-equipos-conectados-a-la-red)

## Resultado esperado

El alumno no necesita diseñar una red lista para producción ni utilizar una marca comercial. Debe demostrar que comprende cómo el SGSI conecta:

1. una necesidad operacional legítima;
2. el riesgo de admitir equipos externos;
3. una política de identidad, autorización y mínimo privilegio;
4. controles administrativos, físicos y técnicos;
5. evidencia de ejecución y pruebas de eficacia; y
6. acciones correctivas que alimentan un nuevo ciclo PLAN.

### Enfoque didáctico

El ejercicio parte de un SGSI existente. La revisión de sus registros —CHECK del ciclo anterior— descubre una debilidad. El hallazgo se registra y se utiliza como entrada para un nuevo ciclo de mejora.

No se evalúa el diseño de todo el SGSI. Se evalúa cómo una mejora concreta se integra al sistema mediante responsables, reglas, evidencia, pruebas y revisión.

La expresión **“control total”** no debe aceptarse como garantía absoluta. Una formulación profesional sería:

> Mantener una garantía razonable de que cada equipo admitido está registrado y asociado con un responsable, recibe únicamente el acceso autorizado, deja una sesión reconstruible y pierde el acceso cuando vence su autorización; mientras los equipos desconocidos son bloqueados o aislados.

## Arquitectura de referencia recomendada

```text
Visita y patrocinador
        ↓
Registro mínimo + identificador de visita
        ↓
Credencial individual con vigencia
        ↓
Autenticación y decisión de admisión
        ├── Equipo institucional → rol según función y estado
        ├── Invitado registrado → Internet, sin rutas internas
        ├── Excepción aprobada → destino/puerto/horario exactos
        └── Desconocido → bloqueo o cuarentena
        ↓
AP o puerto + sesión + DHCP + firewall/DNS
        ↓
Correlación, monitoreo, expiración y revocación
```

La arquitectura puede implementarse por etapas. Para el ejercicio, la ruta más equilibrada es:

- **equipos institucionales:** administración mediante dominio o MDM, certificado de dispositivo y 802.1X/EAP-TLS para Wi-Fi y cable;
- **visitantes comunes:** patrocinio y voucher individual o clave individual de duración limitada, con acceso solamente a Internet;
- **visitantes con necesidad excepcional:** autorización previa y temporal hacia un recurso concreto, preferentemente mediante un gateway, bastión o servicio intermedio;
- **equipos desconocidos:** rechazo o segmento de cuarentena sin acceso operacional; y
- **observabilidad:** correlación central de visita, autenticación, AP/puerto, DHCP, política y firewall.

No es obligatorio que los alumnos nombren 802.1X, EAP-TLS, RADIUS o NAC si describen correctamente sus funciones.

## Decisiones técnicas que deben moldear la retroalimentación

### 1. Registro físico y minimización

Una respuesta sólida puede proponer:

- identificador único de visita;
- visitante, organización, patrocinador, propósito, horario y zonas autorizadas;
- tipo, marca/modelo y número de serie del equipo;
- sistema operativo y versión **declarados**;
- servicios solicitados y decisión de autorización;
- credencial emitida, vigencia y cierre de la visita; y
- dirección MAC observada durante la sesión, diferenciada de la identidad física.

La fotografía con la tapa abierta no demuestra identidad de red y puede capturar información innecesaria. Si la política física exige evidencia visual, es preferible fotografiar el exterior o la etiqueta del número de serie, o colocar una etiqueta temporal vinculada al identificador de visita. El alumno debe justificar para qué se recopila cada dato, quién puede consultarlo y cuándo se elimina.

### 2. Contraseña compartida, voucher, claves individuales y certificados

| Alternativa | Valor | Debilidad o costo |
|---|---|---|
| Contraseña compartida, aunque se comunique verbalmente | Configuración simple | No atribuye sesiones individualmente, se comparte y no puede revocarse por visitante |
| Voucher individual temporal | Apropiado para una visita ocasional; permite patrocinio, vencimiento y revocación | Debe limitar dispositivos o sesiones simultáneas |
| PPSK/DPSK o clave individual por dispositivo, si la plataforma la soporta | Revocación y política individual bajo un mismo SSID | Si depende de la MAC, no constituye por sí sola identidad criptográfica fuerte |
| 802.1X con identidad individual | Autenticación central y asignación dinámica de política | Requiere RADIUS y configuración del cliente |
| EAP-TLS con certificado de dispositivo | Autenticación fuerte y adecuada para equipos institucionales | Requiere PKI, aprovisionamiento y revocación; puede ser excesivo para una visita ocasional |

La recomendación mínima para el consultor es una credencial individual, de una sola visita, con límite de sesiones y expiración automática. Una contraseña “pública pero no escrita” sigue siendo un secreto compartido y no ofrece atribución individual.

### 3. La dirección MAC es un dato de correlación, no la identidad

La respuesta debe reconocer que una dirección MAC:

- puede ser privada o aleatoria;
- puede cambiar entre conexiones;
- puede diferir de la dirección de fábrica; y
- puede ser suplantada.

Vincular un voucher con la primera MAC observada reduce el intercambio casual de credenciales, pero no autentica criptográficamente al equipo. La relación correcta es:

```text
visitante → credencial o certificado → sesión → MAC observada
```

No debe aceptarse sin reservas:

```text
MAC = identidad comprobada del equipo
```

### 4. Segmentación, ubicación y mínimo privilegio

La segmentación principal debe responder al **rol y nivel de confianza**, no a cada punto de acceso:

- institucional administrado;
- invitado;
- cuarentena; y
- excepción temporal.

El controlador inalámbrico puede registrar AP/BSSID y hora para estimar una zona. En cable, el switch, puerto y toma física proporcionan contexto de ubicación. Una VLAN por sala puede ser válida cuando el riesgo físico lo justifica, pero una VLAN por AP aumenta la complejidad y no prueba una ubicación exacta porque las coberturas se superponen.

Para un consultor que solamente presenta material desde Internet, la política base debe negar rutas internas y permitir únicamente los servicios salientes necesarios. Si requiere RDP, SSH u otro servicio, la excepción debe incluir identidad, destino, dirección del flujo, protocolo, puerto, inicio, vencimiento, aprobador y evidencia. Debe desaparecer automáticamente al finalizar.

### 5. Dominio y equipos externos

Los equipos institucionales pueden requerir dominio o MDM, certificado, configuración de firewall y postura administrada. La laptop del visitante no debería incorporarse al dominio institucional para una visita temporal. Si necesita un recurso interno, debe recibir acceso específico mediante una identidad temporal y un punto de control, no confianza amplia dentro de la red.

### 6. Cadena mínima de trazabilidad

Una respuesta completa debe aproximarse a:

```text
ID de visita
→ patrocinador
→ credencial o certificado
→ sesión de autenticación
→ MAC observada
→ AP o puerto
→ rol, VLAN o filtro aplicado
→ concesión DHCP e IP
→ registros DNS/firewall/proxy
→ desconexión, expiración o revocación
```

Los relojes deben estar sincronizados y los registros deben conservar como mínimo inicio, fin, decisión de acceso, política asignada, causa de rechazo y responsable de la excepción. Observabilidad no significa inspeccionar indiscriminadamente el contenido personal del visitante.

---

## Solucionario por fase

### PLAN

#### Pregunta 1 — Fuentes que deben examinarse

Rutas sólidas:

| Fuente | Pregunta que permite responder |
|---|---|
| Registro de visitas | ¿Quién ingresó, patrocinado por quién y durante qué intervalo? |
| Política de visitantes y uso aceptable | ¿Quién puede autorizar y qué obligaciones conoce el visitante? |
| Inventario y administración de endpoints | ¿Qué equipos institucionales son conocidos y cómo se distinguen? |
| Diagrama, SSID, VLAN y reglas de firewall | ¿Qué rutas y recursos podría alcanzar cada categoría? |
| Autenticación o RADIUS | ¿Qué identidad recibió acceso y qué política fue asignada? |
| Controlador Wi-Fi y switches | ¿Desde qué AP o puerto apareció el equipo? |
| DHCP | ¿Qué relación temporal existe entre sesión, MAC e IP? |
| DNS, proxy o firewall | ¿Qué destinos o servicios utilizó la sesión? |

También son válidas entrevistas con recepción, patrocinadores, administradores y responsables de seguridad. No basta con “revisar la red”; debe explicarse la pregunta concreta que responde cada evidencia.

#### Pregunta 2 — Relación mínima entre persona, equipo y sesión

La respuesta esperada relaciona un identificador de visita con:

- persona, organización y patrocinador verificados por el proceso físico;
- propósito, horario y servicios solicitados;
- marca/modelo, número de serie y sistema operativo declarado;
- credencial individual y vigencia;
- sesión técnica, MAC observada, AP/puerto e IP; y
- expiración y cierre.

Debe diferenciarse lo **declarado** —por ejemplo, el sistema operativo— de lo **observado** —por ejemplo, la MAC usada en esa asociación— y de lo **verificado** mediante un mecanismo técnico —por ejemplo, un certificado válido.

#### Pregunta 3 — Política y objetivo medible

Riesgo prioritario modelo:

> Si una persona no autorizada obtiene o reutiliza una credencial compartida, podría conectar un equipo no atribuido a la red y alcanzar servicios fuera de su necesidad, afectando la confidencialidad, integridad o disponibilidad de recursos institucionales y la capacidad de reconstruir lo ocurrido.

Una clasificación defendible:

| Categoría | Autenticación | Acceso | Vigencia |
|---|---|---|---|
| Institucional administrado | Certificado o identidad institucional | Según función y mínimo privilegio | Mientras conserve autorización y postura |
| Visitante registrado | Credencial temporal individual | Internet; aislamiento de redes internas | Duración de la visita |
| Visitante excepcional | Identidad temporal más aprobación | Recurso, destino y puerto exactos | Ventana aprobada |
| Desconocido | Sin autenticación válida | Bloqueo o cuarentena | Hasta regularización |

Objetivo modelo:

> Antes del siguiente ciclo de visitas, el responsable de redes conseguirá que el 100 % de las conexiones admitidas queden asociadas con una identidad o visita vigente; que ningún visitante alcance recursos internos no autorizados; que toda credencial y excepción expire en el plazo aprobado; y que una sesión pueda reconstruirse en menos de diez minutos.

No es indispensable utilizar esos porcentajes o tiempos, pero el objetivo debe ser medible.

### DO

#### Pregunta 1 — Procedimiento de principio a fin

Secuencia modelo:

1. El patrocinador registra previamente visitante, propósito, horario y necesidad de red.
2. Seguridad física valida la visita y relaciona el equipo con el identificador correspondiente.
3. El responsable autorizado aprueba el nivel de acceso y cualquier excepción.
4. El sistema emite una credencial individual con vigencia y límite de uso.
5. La infraestructura autentica la conexión y asigna dinámicamente la política correspondiente.
6. DHCP, AP/switch y firewall registran la sesión con marcas temporales consistentes.
7. El monitoreo alerta sobre usos incompatibles con el rol asignado.
8. Al vencer la visita se revocan credencial y excepciones y se registra el cierre.

La implementación debe incluir responsables, no solamente dispositivos.

#### Pregunta 2 — Autenticación por categoría

Ruta recomendada:

- institucional: 802.1X/EAP-TLS con certificado gestionado;
- visitante común: voucher o clave individual temporal con una sola sesión o límite de dispositivo;
- visitante excepcional: identidad temporal reforzada y aprobación específica; y
- desconocido: sin acceso o cuarentena.

Son aceptables otras tecnologías si producen identidad individual, expiración, revocación y evidencia equivalentes. La vinculación con MAC solamente es una barrera contra intercambio casual.

#### Pregunta 3 — Mínimo privilegio y registros

Debe aparecer:

- separación por confianza o rol;
- invitado sin rutas hacia recursos internos;
- aislamiento entre clientes cuando sea aplicable;
- AP/puerto como atributo de contexto;
- decisión de acceso aplicada por NAC, AP, switch o firewall;
- excepción limitada a destino, puerto y tiempo; y
- correlación de visita, autenticación, DHCP y firewall.

### CHECK

#### Pregunta 1 — Pruebas de admisión

Resultados esperados:

| Prueba | Resultado esperado |
|---|---|
| Equipo no registrado | Bloqueo o cuarentena; evento registrado y, si corresponde, alerta |
| Credencial usada simultáneamente desde un teléfono | Rechazo de la segunda sesión o alerta y revocación según política |
| Cambio de MAC privada | Reautenticación y conservación de la relación con la identidad o visita, sin confiar únicamente en la MAC |

#### Pregunta 2 — Pruebas de mínimo privilegio

El consultor debe acceder a la presentación y a los servicios legítimos, pero los intentos hacia redes internas no autorizadas deben fallar y quedar registrados. Una excepción debe permitir solamente la combinación aprobada de identidad, destino, protocolo, puerto y horario. También debe probarse que los equipos institucionales conservan los servicios necesarios.

#### Pregunta 3 — Trazabilidad y cierre

Desde el identificador de visita, el equipo evaluador debería reconstruir la cadena completa sin búsquedas manuales extensas. Indicadores defendibles:

- porcentaje de sesiones admitidas vinculadas con identidad vigente;
- intentos exitosos hacia recursos no autorizados;
- tiempo para reconstruir una sesión;
- credenciales o excepciones activas después de su vencimiento;
- porcentaje de fuentes con reloj sincronizado; y
- tiempo para bloquear o revocar un acceso.

### ACT

#### Pregunta 1 — Correcciones inmediatas

Prioridad esperada:

1. retirar la excepción de firewall que quedó activa;
2. bloquear o revocar la credencial reutilizada;
3. limitar sesiones simultáneas o emitir credenciales por dispositivo;
4. sincronizar relojes y restablecer la correlación; y
5. reconciliar la segunda MAC con la sesión autorizada sin declararla automáticamente como identidad distinta.

El orden puede variar si el equipo justifica el riesgo.

#### Pregunta 2 — Causas y acciones correctivas

| Hallazgo | Corrección puntual | Acción correctiva sistémica |
|---|---|---|
| Credencial reutilizada | Revocar y reemplazarla | Política de una sesión/dispositivo, alerta de simultaneidad o autenticación más fuerte |
| Cambio de MAC rompe la relación | Asociar manualmente la sesión | Correlacionar por credencial/certificado y tratar la MAC como atributo cambiante |
| Registros con siete minutos de diferencia | Ajustar la hora | Fuente horaria común, monitoreo de desviación y prueba periódica |
| Excepción permanece activa | Eliminar la regla | Automatizar vencimiento, propietario, revisión y alerta de excepción huérfana |

#### Pregunta 3 — Siguiente ciclo PLAN

El nuevo PLAN debería actualizar la política de credenciales, el diseño de correlación, el proceso de excepciones y los indicadores. Después se repiten las pruebas que fallaron. El riesgo residual puede incluir suplantación de atributos, indisponibilidad del sistema de autenticación, dispositivos que usan conectividad celular o errores del patrocinador; el equipo debe decidir cómo tratarlo o aceptarlo formalmente.

---

## Ejemplo de narrativa integrada

> **PLAN:** La unidad examinó el proceso de visitas, el inventario, el diseño de red y los registros de autenticación, DHCP y firewall. Identificó el riesgo de admitir equipos sin poder atribuirlos a una visita ni limitar su acceso. Estableció que cada sesión admitida debía vincularse con una identidad vigente, impedir acceso interno no autorizado, expirar automáticamente y poder reconstruirse en menos de diez minutos.
>
> **DO:** El patrocinador registró previamente al consultor y sus necesidades. Recepción relacionó la laptop con un identificador de visita y redes emitió un voucher individual. La infraestructura asignó al equipo un rol de invitado con acceso solamente a Internet; cualquier excepción quedó limitada por destino, puerto y horario. Los sistemas de autenticación, Wi-Fi, DHCP y firewall enviaron registros sincronizados al repositorio central.
>
> **CHECK:** Se probaron un dispositivo desconocido, la reutilización del voucher, el cambio de MAC privada, el acceso a Internet, el bloqueo de recursos internos, la expiración y la reconstrucción de la sesión. El aislamiento funcionó, pero el voucher pudo utilizarse desde un teléfono, la MAC cambiante dificultó la correlación, los relojes estaban desalineados y una excepción no expiró.
>
> **ACT:** La unidad retiró la excepción, revocó la credencial y sincronizó los registros. Como acciones sistémicas, limitó las sesiones simultáneas, vinculó la trazabilidad con la credencial en vez de depender de la MAC y automatizó el vencimiento de reglas. El siguiente PLAN incorporará pruebas periódicas de correlación y de revocación automática.

## Criterios para calificar alternativas

No penalice una tecnología diferente si conserva estos resultados:

- identidad o atribución individual suficiente para el riesgo;
- autorización previa y vigencia limitada;
- mínimo privilegio y separación de redes;
- evidencia correlacionable;
- prueba real de eficacia; y
- corrección que modifica el sistema y vuelve a verificarse.

Respuestas como “ocultar la contraseña”, “anotar solamente la MAC”, “crear una VLAN por AP” o “unir toda laptop al dominio” requieren corrección porque confunden secreto compartido, atributo de red, ubicación aproximada y confianza institucional.

## Fuentes técnicas para el instructor

- [NIST Cybersecurity Framework 2.0](https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf): inventario, identidad, mínimo privilegio, logging, monitoreo y mejora.
- [NIST SP 800-171 Rev. 3](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/800-171r3/NIST.SP.800-171r3.html): identificación y autenticación de dispositivos; referencias a 802.1X, RADIUS y EAP-TLS.
- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final): autenticación y autorización de sujeto y dispositivo, mínimo privilegio y ausencia de confianza implícita por ubicación.
- [NIST SP 800-53A Rev. 5](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53Ar5.pdf): autorización, autenticación, cifrado y evaluación del acceso inalámbrico.
- [RFC 9797 — Randomized and Changing MAC Addresses](https://www.rfc-editor.org/rfc/rfc9797.html): límites de utilizar una MAC como identidad persistente y efectos sobre servicios de red.
