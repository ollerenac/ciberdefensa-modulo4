---
# Horas asignadas: 2 hrs
# Tipo: Teoría
---

# ISO 27000 — Generalidades (Parte 2)

> **Duración:** 2 horas | **Asignatura:** Capas SI | **Unidad:** La Familia ISO 27000

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Describir el ciclo PDCA y explicar cómo se aplica al Sistema de Gestión de la Seguridad de la Información.
- Distinguir entre postura de seguridad real y cumplimiento normativo (compliance vs. security posture).
- Identificar los beneficios concretos que aporta un SGSI a una organización o unidad militar.
- Seleccionar, entre controles previamente relacionados con un incidente, el control principal y proponer una implementación concreta y verificable.

---

> **Recapitulación:** En la clase anterior (Parte 1) cubrimos el origen de los estándares, la estructura de la familia (27000:2026 panorama y conceptos, 27001:2022 requisitos, 27002:2022 controles, 27005:2022 riesgo) y los cuatro componentes fundamentales de un SGSI mediante el ejemplo de la SC-RI8. Esta clase continúa con el motor que hace funcionar el SGSI: el ciclo PDCA y los beneficios reales que aporta.

---

## El Ciclo PDCA Aplicado al SGSI

Un SGSI no es un proyecto con fecha de inicio y fecha de fin. Es un ciclo permanente de mejora. La herramienta conceptual que describe ese ciclo es el modelo **PDCA** (Plan-Do-Check-Act), también llamado ciclo de Deming por el estadístico W. Edwards Deming, quien lo popularizó en la gestión de calidad en la década de 1950.

ISO 27001 adoptó el ciclo PDCA como el principio organizativo del SGSI porque captura una verdad fundamental: la seguridad de la información en el mundo real nunca está "completa". Las amenazas evolucionan, los sistemas cambian, el personal rota, surgen nuevas vulnerabilidades. Un sistema de gestión que no se revisa y mejora continuamente se vuelve obsoleto.

```
     ┌─────────────────────────────────────────────────────┐
     │                    CICLO PDCA                       │
     │                                                     │
     │   ┌──────────┐          ┌──────────┐               │
     │   │  PLAN    │ ──────▶  │   DO     │               │
     │   │ Planear  │          │ Ejecutar │               │
     │   └──────────┘          └──────────┘               │
     │        ▲                      │                     │
     │        │                      ▼                     │
     │   ┌──────────┐          ┌──────────┐               │
     │   │   ACT    │ ◀──────  │  CHECK   │               │
     │   │  Actuar  │          │ Verificar│               │
     │   └──────────┘          └──────────┘               │
     └─────────────────────────────────────────────────────┘
```

### Las cuatro fases aplicadas a la seguridad de la información

**PLAN — Planear (Cláusulas 4-7 de ISO 27001):**
En esta fase la organización define el alcance del SGSI, identifica sus activos de información, evalúa los riesgos, selecciona los controles que va a implementar y establece los objetivos de seguridad. Es la fase donde se toman las decisiones: ¿qué queremos proteger? ¿de qué amenazas? ¿qué controles son adecuados para nuestro contexto?

**DO — Ejecutar (Cláusula 8 de ISO 27001):**
En esta fase se implementan los controles planificados. Se instalan las herramientas técnicas, se redactan y distribuyen las políticas, se capacita al personal, se configuran los sistemas según los requisitos de seguridad decididos en la fase anterior. Es la fase de acción.

**CHECK — Verificar (Cláusula 9 de ISO 27001):**
En esta fase se mide si los controles funcionan como se esperaba. Incluye auditorías internas (¿se cumplen las políticas?), revisión de métricas de seguridad (¿cuántos incidentes hubo este mes?), y revisión por la dirección (¿el SGSI sigue siendo adecuado para el contexto actual?). Sin verificación, no hay forma de saber si la seguridad real mejoró o solo la documentación.

**ACT — Actuar (Cláusula 10 de ISO 27001):**
En esta fase se corrigen las no-conformidades encontradas en la fase CHECK, se implementan mejoras, y se actualiza el SGSI para reflejar los cambios en el contexto o en los riesgos. Al final de la fase ACT, el ciclo vuelve a comenzar — con un SGSI mejorado que inicia un nuevo ciclo PLAN.

!!! example "Aplicación en entorno castrense"
    La Jefatura de Comunicaciones del Batallón aplica PDCA a la gestión de su material criptográfico:
    **PLAN:** Define qué equipos usan cifrado, qué algoritmos se cargan, con qué frecuencia se renueva el material criptográfico, y qué procedimiento sigue si un equipo se pierde.
    **DO:** Carga el material criptográfico en los radios tácticos según el plan, documenta los equipos que lo recibieron, y capacita al personal en el procedimiento de pérdida de equipo.
    **CHECK:** Realiza una auditoría mensual para verificar que todos los equipos tienen el material correcto y que los registros están al día. Revisa si hubo incidentes de seguridad relacionados con comunicaciones.
    **ACT:** Si la auditoría detecta que un equipo tiene material criptográfico vencido, se corrige inmediatamente y se actualiza el procedimiento para evitar que vuelva a ocurrir.

---

## Beneficios del SGSI

Implementar un SGSI tiene beneficios concretos — no solo para la organización en abstracto, sino para las personas que trabajan en ella.

**Para la dirección / el mando:**
El SGSI proporciona visibilidad del estado real de la seguridad. Sin él, el Comandante no sabe qué activos existen, qué tan vulnerables son, ni si los controles que se dicen implementados realmente funcionan. Con un SGSI, esa información está documentada, medida y reportada.

**Para el personal técnico:**
El SGSI define responsabilidades claras. Los Técnicos saben qué sistemas son de su responsabilidad, qué controles deben mantener, y qué procedimiento seguir cuando algo falla. No hay ambigüedad sobre quién hace qué en un incidente.

**Para la continuidad de la misión:**
Un SGSI incluye planes de continuidad para los activos más críticos. Si un sistema falla durante una operación, el plan de continuidad define cómo se mantiene la misión con recursos alternativos o en modo degradado. La improvisación bajo presión genera errores; el plan documentado reduce esa improvisación.

**Para la respuesta a incidentes:**
Cuando ocurre un incidente de seguridad, la organización tiene un inventario de activos (sabe qué fue afectado), un registro de controles implementados (sabe qué protecciones existían), y un proceso de gestión de incidentes (sabe cómo responder). Sin SGSI, el primer paso de cualquier respuesta es tratar de entender qué hay en el entorno — lo que consume tiempo crítico.

---

## Compliance vs. Security Posture

Esta distinción es una de las más importantes del curso, y una de las más frecuentemente mal entendidas.

**Compliance** (cumplimiento normativo) significa que la organización puede demostrar que cumple con los requisitos de un estándar o regulación. Es un estado documentado y verificable: "tenemos política de contraseñas documentada y aprobada" o "nuestros servidores tienen los parches al día". El compliance se demuestra ante auditores, clientes o reguladores.

**Security posture** (postura de seguridad) significa qué tan bien protegida está realmente la organización frente a amenazas actuales. Es el estado real de seguridad, no el estado documentado.

!!! warning "El peligro del cumplimiento aparente"
    Una organización puede exhibir una certificación vigente o abundante evidencia documental y tener una postura de seguridad débil. Si la política de contraseñas existe pero nadie la cumple, el plan de respuesta está redactado pero nadie lo conoce o las no conformidades no se corrigen, no existe “compliance total”: existe una brecha entre la conformidad declarada y la eficacia real. Demostrar conformidad ayuda, pero no basta por sí solo para sostener una buena postura de seguridad.

La relación correcta entre los dos conceptos es: el SGSI es el mecanismo que, cuando funciona bien, hace que el compliance y la postura de seguridad converjan. Un SGSI que solo genera papeles sin mejorar controles reales es burocracia, no seguridad.

Para los Técnicos: su trabajo diario — configurar equipos correctamente, actualizar parches, verificar que los controles funcionan — es lo que crea postura de seguridad real. La documentación del SGSI registra y formaliza ese trabajo, pero no lo sustituye.

---

## Trabajo por Equipos — Del Incidente a un Control Verificable

> **Duración sugerida:** 50 minutos | **Modalidad:** equipos de 2–4 alumnos | **Producto:** exposición de 2 minutos y ficha de trabajo

### Propósito de la actividad

El alumno todavía no conoce los 93 controles de ISO/IEC 27002:2022. Por eso, **no debe investigar todo el catálogo ni adivinar el control correcto**. Cada expediente ya contiene un conjunto reducido de controles razonablemente relacionados con las debilidades documentadas.

La tarea del equipo consiste en tomar una decisión profesional:

> **¿Cuál de los controles entregados debe ser el principal, cómo se implementaría y qué evidencia demostraría que funciona?**

!!! info "Precisión terminológica"
    Los códigos **A.5.*, A.7.* y A.8.*** identifican controles del Anexo A de [ISO/IEC 27001:2022](https://www.iso.org/standard/27001). [ISO/IEC 27002:2022](https://www.iso.org/standard/75652.html) conserva esos identificadores y proporciona orientación para implementarlos. ISO/IEC 27000 no es un catálogo de controles.

!!! warning "Regla de interpretación"
    El ejercicio es un mapeo retrospectivo con fines didácticos. Relacionar una debilidad con un control no demuestra que la organización careciera de SGSI, que incumpliera ISO/IEC 27001 o que una certificación hubiera evitado el incidente. Un control puede no haber existido, haber tenido un alcance insuficiente, estar mal implementado o no haber funcionado eficazmente; las fuentes disponibles no siempre permiten distinguirlo.

### Organización del tiempo

- **5 minutos:** el docente explica la consigna, la regla de interpretación y asigna un expediente.
- **8 minutos:** el equipo identifica activo, efecto sobre la tríada CID y debilidad prioritaria.
- **15 minutos:** el equipo selecciona controles y diseña una implementación verificable.
- **12 minutos:** seis exposiciones de dos minutos; si hay menos equipos, cada equipo puede analizar una segunda alternativa.
- **10 minutos:** comparación de decisiones y retroalimentación del docente.

No se requiere investigar en Internet durante la clase: los hechos y las fuentes institucionales necesarias están incluidos en cada expediente.

### Consigna para todos los equipos

1. Identificar el **activo o proceso afectado** y el efecto principal sobre confidencialidad, integridad o disponibilidad.
2. Elegir una **debilidad documentada prioritaria**. No agregar causas que no aparezcan en el expediente.
3. Seleccionar **un control principal** de la lista entregada y justificar por qué trata más directamente la debilidad prioritaria.
4. Seleccionar hasta **dos controles complementarios** que ayuden a prevenir, detectar o reducir el impacto.
5. Diseñar la implementación utilizando esta fórmula:

    > **Responsable + acción + alcance + frecuencia o evento disparador + evidencia producida.**

6. Indicar cómo se comprobaría la **eficacia** del control. Tener un procedimiento o una herramienta no basta: debe probarse que produce el resultado esperado.
7. Completar la conclusión prudente:

    > “La debilidad documentada se relaciona principalmente con ___; una aplicación eficaz habría podido ___, pero la evidencia disponible no demuestra ___”.

!!! tip "Cómo reconocer el control principal"
    El control principal trata de forma más directa la debilidad que el equipo decidió priorizar. Los controles complementarios actúan sobre causas secundarias, mejoran la detección o limitan el impacto. Como varios expedientes contienen más de una debilidad, pueden existir elecciones diferentes siempre que estén sustentadas en los hechos.

### Ficha de respuesta

| Elemento | Respuesta del equipo |
|---|---|
| Expediente asignado | |
| Activo o proceso afectado | |
| Efecto principal sobre CID | |
| Debilidad documentada prioritaria | |
| Control principal y justificación | |
| Hasta dos controles complementarios | |
| Implementación: responsable, acción, alcance y frecuencia | |
| Evidencia producida | |
| Prueba o indicador de eficacia | |
| Conclusión prudente | |

### Expediente 1 — Equifax (2017)

**Hechos documentados:** atacantes explotaron una vulnerabilidad de Apache Struts que no fue identificada correctamente en el portal afectado. La lista de distribución del aviso de parcheo estaba desactualizada, el escaneo posterior no encontró el sistema vulnerable, un certificado vencido impidió inspeccionar tráfico cifrado y las bases de datos no estaban suficientemente segmentadas. Se accedió a información personal de al menos 145,5 millones de personas. Fuente: [informe GAO-18-559](https://www.gao.gov/products/gao-18-559).

**Controles relacionados entregados al equipo:**

| Control | Descripción simplificada |
|---|---|
| **A.8.8 — Gestión de vulnerabilidades técnicas** | Identificar, evaluar, corregir y verificar vulnerabilidades dentro de plazos definidos. |
| **A.5.9 — Inventario de información y otros activos asociados** | Conocer los activos, software, versiones, ubicación y responsables. |
| **A.8.16 — Actividades de monitoreo** | Observar sistemas y redes para detectar actividad anómala. |
| **A.8.22 — Segregación de redes** | Separar redes y sistemas para limitar accesos y movimiento entre zonas. |

### Expediente 2 — Target (2013)

**Hechos documentados:** el informe del Senado reconstruyó un acceso inicial con credenciales robadas a un proveedor, desplazamiento desde áreas menos sensibles hacia sistemas de punto de venta y falta de respuesta ante varias alertas del software de detección. Se expusieron aproximadamente 40 millones de cuentas de tarjetas y datos personales de hasta 70 millones de clientes. Fuente: [informe del Comité de Comercio del Senado de EE. UU.](https://www.commerce.senate.gov/public/_cache/files/24d3c229-4f2f-405d-b8db-a3a67f183883/23E30AA955B5C00FE57CFD709621592C.2014-0325-target-kill-chain-analysis.pdf).

**Controles relacionados entregados al equipo:**

| Control | Descripción simplificada |
|---|---|
| **A.5.19 — Seguridad en las relaciones con proveedores** | Identificar y gestionar riesgos derivados del acceso o servicio de terceros. |
| **A.5.20 — Seguridad en acuerdos con proveedores** | Establecer requisitos y responsabilidades de seguridad en los acuerdos. |
| **A.5.22 — Monitoreo y cambios de servicios de proveedores** | Supervisar periódicamente el servicio, los accesos y los cambios del proveedor. |
| **A.8.22 — Segregación de redes** | Impedir que un acceso de menor confianza alcance directamente sistemas críticos. |
| **A.5.25 — Evaluación de eventos de seguridad** | Analizar alertas y decidir oportunamente si deben tratarse como incidentes. |

### Expediente 3 — Maersk / NotPetya (2017)

**Hechos documentados:** la acusación del Departamento de Justicia describe la distribución de NotPetya mediante el mecanismo de actualización del software contable ucraniano M.E.Doc; el ataque provocó una interrupción global. Maersk estimó pérdidas de entre USD 250 y 300 millones por pérdida de ingresos, restauración de TI y costos operacionales extraordinarios. Fuentes: [acusación del Departamento de Justicia de EE. UU.](https://www.justice.gov/d9/press-releases/attachments/2020/10/19/2020_10_19_unsealed_indictment_0.pdf) e [Informe Anual 2017 de Maersk](https://investor.maersk.com/system/files-encrypted/nasdaq_kms/assets/2018/04/25/13-00-21/A.P._Moller_-_Maersk_Annual_Report_2017.pdf).

**Controles relacionados entregados al equipo:**

| Control | Descripción simplificada |
|---|---|
| **A.5.21 — Seguridad en la cadena de suministro TIC** | Gestionar riesgos introducidos por productos, componentes y actualizaciones de terceros. |
| **A.5.29 — Seguridad durante una interrupción** | Mantener un nivel apropiado de protección mientras la organización opera bajo disrupción. |
| **A.5.30 — Preparación de las TIC para la continuidad** | Preparar, probar y mantener capacidades tecnológicas de recuperación. |
| **A.8.13 — Respaldo de información** | Mantener copias protegidas y comprobar que pueden restaurarse. |

### Expediente 4 — Capital One (2019)

**Hechos documentados:** una configuración incorrecta del firewall de aplicación web permitió ejecutar solicitudes que obtuvieron credenciales de roles y acceder, dentro de los permisos disponibles, a datos almacenados en la nube. La actividad de marzo fue comunicada a Capital One por una persona externa en julio. El incidente afectó aproximadamente a 100 millones de personas en EE. UU. y 6 millones en Canadá; la OCC impuso una penalidad de USD 80 millones por deficiencias de evaluación de riesgo y controles en la nube. Fuentes: [Departamento de Justicia de EE. UU.](https://www.justice.gov/usao-wdwa/pr/seattle-tech-worker-arrested-data-theft-involving-large-financial-services-company), [comunicado de Capital One](https://www.capitalone.com/about/newsroom/capital-one-announces-data-security-incident/) y [resolución de la OCC](https://www.occ.gov/news-issuances/news-releases/2020/nr-occ-2020-101.html).

**Controles relacionados entregados al equipo:**

| Control | Descripción simplificada |
|---|---|
| **A.5.23 — Seguridad para el uso de servicios en la nube** | Definir procesos para adquirir, usar, administrar y finalizar servicios cloud de forma segura. |
| **A.8.9 — Gestión de la configuración** | Definir configuraciones seguras, aplicarlas y detectar desviaciones. |
| **A.8.2 — Derechos de acceso privilegiado** | Restringir y revisar los privilegios de cuentas y roles con capacidades elevadas. |
| **A.8.16 — Actividades de monitoreo** | Detectar consultas, accesos o transferencias anómalas. |

### Expediente 5 — Marriott / Starwood (2014–2018)

**Hechos documentados:** el atacante entró al entorno de Starwood en 2014; Marriott adquirió la empresa en 2016 y detectó la actividad en 2018. El ICO documentó, entre otras deficiencias, monitoreo insuficiente de cuentas privilegiadas y bases de datos. Se vieron afectados alrededor de 339 millones de registros de huéspedes y la sanción fue de GBP 18,4 millones. El ICO no atribuyó la sanción a una infracción durante la adquisición anterior al GDPR. Fuente: [resolución sancionadora del ICO](https://ico.org.uk/media2/migrated/2618524/marriott-international-inc-mpn-20201030.pdf).

**Controles relacionados entregados al equipo:**

| Control | Descripción simplificada |
|---|---|
| **A.8.15 — Registro de eventos** | Generar, proteger y analizar registros útiles de sistemas, aplicaciones y accesos. |
| **A.8.16 — Actividades de monitoreo** | Observar los sistemas para detectar comportamientos anómalos y actuar ante ellos. |
| **A.8.2 — Derechos de acceso privilegiado** | Limitar, autorizar y revisar accesos con privilegios elevados. |
| **A.5.9 — Inventario de información y otros activos asociados** | Conocer los sistemas, bases de datos, información y responsables incluidos en el entorno. |

### Expediente 6 — Morgan Stanley (2016–2019)

**Hechos documentados:** durante el retiro de centros de datos y otros equipos, la organización no evaluó adecuadamente el riesgo, seleccionó y supervisó deficientemente a proveedores, y no mantuvo un inventario apropiado de los datos en los dispositivos. Parte del hardware fue revendida sin eliminar toda la información. La OCC impuso USD 60 millones y la SEC USD 35 millones; esta última acción abarcó información de aproximadamente 15 millones de clientes. Fuentes: [resolución de la OCC](https://www.occ.gov/news-issuances/news-releases/2020/nr-occ-2020-134.html) y [resolución de la SEC](https://www.sec.gov/newsroom/press-releases/2022-168).

**Controles relacionados entregados al equipo:**

| Control | Descripción simplificada |
|---|---|
| **A.7.14 — Eliminación o reutilización segura de equipos** | Comprobar que equipos y soportes no conservan información antes de desecharlos o reutilizarlos. |
| **A.8.10 — Eliminación de información** | Eliminar información cuando ya no se requiere, usando métodos apropiados y verificables. |
| **A.5.9 — Inventario de información y otros activos asociados** | Mantener trazabilidad sobre dispositivos, información, custodios y estado. |
| **A.5.19 — Seguridad en las relaciones con proveedores** | Evaluar y gestionar los riesgos del tercero que manipula los equipos. |
| **A.5.22 — Monitoreo y cambios de servicios de proveedores** | Supervisar el desempeño, cumplimiento y cambios del servicio contratado. |

### Criterios de evaluación — 10 puntos

| Criterio | Puntaje |
|---|---:|
| Identifica correctamente activo, efecto CID y debilidad documentada | 2 |
| Justifica por qué el control elegido es principal | 2 |
| Propone una implementación específica, viable y con responsable | 3 |
| Define evidencia y una prueba o indicador de eficacia | 2 |
| Formula una conclusión prudente sin afirmar incumplimiento no demostrado | 1 |

### Entrega de las diapositivas

Cada equipo entregará **un solo archivo de PowerPoint (`.pptx`)** con su análisis. El instructor indicará cuándo está habilitado el servidor de recepción y comunicará verbalmente la contraseña temporal.

#### 1. Guardar el archivo con el nombre obligatorio

Guarden y cierren la presentación en la carpeta **Documentos** del usuario conectado en Windows. Usen este formato:

```text
G##-ATAQUE.pptx
```

- Reemplacen `##` por el número del grupo con dos dígitos.
- Usen uno de estos nombres de ataque: `EQUIFAX`, `TARGET`, `MAERSK`, `CAPITAL-ONE`, `MARRIOTT` o `MORGAN-STANLEY`.
- No agreguen espacios, tildes ni nombres de integrantes al nombre del archivo.

Ejemplos válidos:

```text
G01-EQUIFAX.pptx
G04-CAPITAL-ONE.pptx
G06-MORGAN-STANLEY.pptx
```

#### 2. Verificar la conexión y el comando SCP

Abrir **PowerShell** y ejecutar:

```powershell
Test-NetConnection 192.168.22.250 -Port 22
Get-Command scp -ErrorAction SilentlyContinue
```

Continuar solamente si `TcpTestSucceeded` muestra `True` y el segundo comando muestra la ubicación de `scp.exe`. Si alguna comprobación falla, avisar al instructor.

#### 3. Enviar la presentación

Copiar y pegar en PowerShell el siguiente bloque completo:

```powershell
$nombre = (Read-Host "Nombre exacto del archivo, incluido .pptx").Trim()

if ($nombre -notmatch '(?i)^G\d{2}-(EQUIFAX|TARGET|MAERSK|CAPITAL-ONE|MARRIOTT|MORGAN-STANLEY)\.pptx$') {
    throw "Nombre incorrecto. Ejemplo válido: G01-EQUIFAX.pptx"
}

$documentos = [Environment]::GetFolderPath('MyDocuments')
$archivo = Join-Path $documentos $nombre

if (-not (Test-Path -LiteralPath $archivo -PathType Leaf)) {
    throw "No se encontró el archivo en Documentos: $archivo"
}

scp $archivo "entregas@192.168.22.250:$nombre"
```

Cuando el bloque lo solicite, escribir el nombre exacto del archivo. En la primera conexión puede aparecer una pregunta sobre la autenticidad del servidor: responder `yes` **solo después de que el instructor confirme la huella mostrada**. Luego ingresar la contraseña temporal; PowerShell no muestra caracteres mientras se escribe.

#### 4. Confirmar la entrega

La transferencia terminó correctamente cuando `scp` muestra el nombre del archivo, una barra de progreso y `100%`. No cerrar PowerShell antes de ver esa confirmación.

!!! warning "Antes de retirarse"
    Cada grupo debe comprobar que entregó el archivo correcto. Si necesita reemplazar una entrega, debe comunicarlo al instructor antes de volver a ejecutar el envío.

??? success "Guía para el instructor — abrir después de las exposiciones"

    No existe necesariamente una sola respuesta correcta: el control principal depende de la debilidad que el equipo haya priorizado. Las siguientes son rutas sólidas para orientar la retroalimentación.

    | Caso | Elección principal defendible | Ejemplo de implementación verificable |
    |---|---|---|
    | Equifax | **A.8.8** si se prioriza la vulnerabilidad; **A.5.9** si se prioriza la falta de visibilidad del sistema | Inventario de software y responsables, recepción de avisos, plazo para vulnerabilidades críticas, ticket de corrección y escaneo autenticado que confirme el cierre. |
    | Target | **A.5.19/A.5.20** si se prioriza el acceso del proveedor; **A.8.22** si se prioriza el desplazamiento; **A.5.25** si se priorizan las alertas desatendidas | Cuenta individual para cada tercero, privilegio y horario mínimos, segmento aislado, revisión trimestral y alerta con responsable y plazo de escalamiento probado. |
    | Maersk | **A.5.21** si se prioriza la actualización comprometida; **A.5.30** si se prioriza la continuidad | Validar origen e integridad de actualizaciones, probarlas en un entorno aislado, mantener respaldos desconectados y ejecutar ejercicios contra objetivos de recuperación. |
    | Capital One | **A.8.9** o **A.5.23** si se prioriza la configuración cloud; **A.8.16** si se prioriza la detección tardía | Configuración base versionada, revisión automática antes del despliegue, roles de mínimo privilegio, registro centralizado y alerta de consultas o transferencias anómalas. |
    | Marriott | **A.8.16** si se prioriza el monitoreo; **A.8.15** si se prioriza la falta de registros útiles; **A.8.2** si se priorizan los privilegios | Registrar actividad administrativa y de base de datos, centralizarla, alertar por patrones definidos, asignar responsable de respuesta y probar periódicamente la detección. |
    | Morgan Stanley | **A.7.14/A.8.10** si se prioriza la información residual; **A.5.19/A.5.22** si se prioriza al proveedor | Conciliar inventario y cadena de custodia, usar borrado verificable o destrucción, conservar certificado, muestrear equipos y auditar al proveedor contra requisitos contractuales. |

    **Diferencia que debe aparecer en una buena respuesta:**

    - **Evidencia de ejecución:** existe un ticket que afirma que se instaló el parche.
    - **Evidencia de eficacia:** un escaneo posterior confirma que la vulnerabilidad ya no está presente.

    **Ejemplos de respuestas insuficientes:** “poner un firewall”, “capacitar al personal”, “hacer respaldos” o “monitorear la red”. Les falta responsable, alcance, frecuencia, evidencia o criterio de éxito.

El patrón común de los seis expedientes no es solamente “faltó una herramienta”. Aparecen inventario, decisiones de riesgo, responsabilidades, proveedores, monitoreo, continuidad y verificación de eficacia: los elementos que convierten controles aislados en un sistema de gestión.

---

## Aplicación en Contexto Castrense

**Ejemplo 1 — PDCA y el puesto de mando avanzado:**
Un puesto de mando avanzado desplegado en campo aplica el ciclo PDCA a sus comunicaciones seguras aunque no lo llame "SGSI". PLAN: antes del despliegue, define qué canales serán cifrados, quién tiene qué radio, y cuáles son los procedimientos de comunicación en caso de interferencia. DO: despliega con los equipos configurados según el plan. CHECK: cada 24 horas, el Técnico de comunicaciones verifica que todos los equipos funcionan correctamente y que no hay señales de interferencia activa. ACT: si detecta que un canal está comprometido o interferido, ejecuta el procedimiento de cambio de frecuencia y actualiza el plan para los días siguientes. Este ciclo no tiene nombre formal, pero es PDCA aplicado a la seguridad operacional.

**Ejemplo 2 — Compliance vs. postura en sistemas C2:**
Una unidad puede tener documentado en su SOP (Standard Operating Procedure) que todos los sistemas C2 deben tener contraseña de acceso de 12 caracteres mínimo — eso es compliance con una política interna. Pero si en la práctica los operadores usan contraseñas simples para no tener que reescribleas durante operaciones largas, la postura de seguridad real es deficiente pese al cumplimiento formal. Un SGSI que funciona detecta esta brecha en la fase CHECK mediante auditorías reales, y la corrige en la fase ACT.

---

## Resumen

1. El ciclo **PDCA** (Plan-Do-Check-Act) es el principio organizativo del SGSI: es un ciclo permanente, no un proyecto con fecha de fin.
2. Cada fase del PDCA corresponde a cláusulas de ISO 27001: PLAN (cláusulas 4-7), DO (8), CHECK (9), ACT (10).
3. Los beneficios del SGSI incluyen visibilidad para el mando, responsabilidades claras para el personal técnico, continuidad de misión, y respuesta a incidentes más efectiva.
4. **Compliance** es cumplir con los requisitos documentados; **security posture** es el nivel de protección real. El SGSI que funciona bien hace que ambos converjan.
5. Ante un incidente, el análisis correcto parte de hechos documentados, prioriza una debilidad, selecciona controles relacionados y exige evidencia de ejecución y de eficacia; no convierte el mapeo en una acusación de incumplimiento.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **Ciclo PDCA — Wikipedia:** https://es.wikipedia.org/wiki/Ciclo_de_Deming — Historia del modelo y sus aplicaciones en gestión de calidad y seguridad.
- **ISO 27001 y PDCA — ISO.org:** https://www.iso.org/isoiec-27001-information-security.html — Descripción oficial del estándar con referencia al ciclo de mejora continua.
- **Compliance vs. Security Posture (SANS):** https://www.sans.org/blog/compliance-vs-security/ — Artículo técnico en inglés que profundiza en la distinción con casos de estudio.

---

## Ejercicio integrador — Mejorar el control de equipos conectados a la red

> **Duración sugerida:** 50 minutos | **Modalidad:** equipos de 3–4 alumnos | **Producto:** 12 respuestas, un diagrama y una explicación final

!!! info "Uso dentro de la sesión"
    La unidad ya cuenta con un SGSI. El ejercicio no pide diseñarlo desde cero, sino mejorar una parte que presenta una debilidad.

    Para mantener las dos horas de clase, esta actividad puede reemplazar el trabajo por incidentes cuando se quiera reforzar el ciclo PDCA.

### Propósito

Aplicar PDCA para mejorar la forma en que la unidad registra, autoriza y supervisa los equipos que se conectan a su red.

Al finalizar, la unidad debería poder responder:

- ¿Quién autorizó la conexión?
- ¿Qué persona y qué equipo la utilizaron?
- ¿Cuándo y desde dónde se conectaron?
- ¿Qué acceso recibieron?
- ¿Cuándo terminó la autorización?

Poder responder estas preguntas se denomina **trazabilidad**. No significa vigilar todo el contenido del equipo, sino conservar evidencia suficiente sobre su acceso a la red.

### Escenario

La unidad ya opera un SGSI. Entre otras medidas, registra visitantes, administra sus equipos institucionales, mantiene una red de invitados y conserva registros de red.

Durante una revisión periódica —CHECK del ciclo anterior— se encontró una conexión que no pudo relacionarse con una visita, una persona ni un equipo registrado.

Los registros mostraban una dirección IP y una dirección MAC, pero todos los visitantes utilizaban la misma contraseña Wi-Fi. Tampoco existía un registro común que relacionara la visita con la conexión.

La próxima semana llegará un consultor externo. El Oficial de Operaciones será responsable de la visita. El consultor llevará una laptop para mostrar una presentación almacenada en Internet.

En principio solo necesita navegar por la web. Si requiere otro servicio, deberá solicitarlo y obtener autorización antes de la visita.

La situación actual es la siguiente:

- recepción registra al visitante y al oficial responsable, pero no registra el equipo;
- la contraseña de invitados es compartida y no vence al terminar cada visita;
- el punto de acceso registra hora y MAC;
- DHCP registra qué dirección IP entregó;
- el firewall registra las conexiones, pero esos datos no están unidos al registro de la visita;
- algunas salas tienen Wi-Fi y puertos de red cableados; y
- nadie comprueba formalmente que las credenciales y permisos temporales hayan sido retirados.

El hallazgo se convierte en una oportunidad de mejora. El mando solicita actualizar el SGSI para reducir las conexiones no registradas sin impedir el trabajo legítimo.

### Forma de trabajo

Respondan las tres preguntas de cada fase antes de continuar. Cada respuesta debe indicar:

- qué decisión tomaron;
- por qué la tomaron;
- quién sería responsable; y
- qué evidencia quedaría.

Si necesitan asumir algo que no aparece en el escenario, indíquenlo claramente.

### Organización del tiempo

- **10 minutos:** PLAN.
- **10 minutos:** DO.
- **8 minutos:** CHECK.
- **10 minutos:** analizar el piloto y responder ACT.
- **12 minutos:** preparar la explicación final.

---

### PLAN — Comprender el problema y decidir qué se necesita

1. **¿Qué revisarían para entender la debilidad actual?**

    Elijan al menos cuatro fuentes: registro de visitas, inventario, diagrama de red, configuración Wi-Fi, DHCP, firewall o autenticación.

    Para cada fuente, indiquen qué información buscarían y qué duda les permitiría resolver.

2. **¿Qué datos mínimos permitirían relacionar la visita, el equipo y la conexión?**

    Distingan entre datos declarados por el visitante y datos observados por la red. Indiquen quién autoriza la visita, cuánto dura y cuándo deben eliminarse sus datos.

3. **¿Qué riesgo tratarían y qué resultado esperan obtener?**

    Completen: “Si [actor] aprovecha [debilidad], podría afectar [activo] y provocar [consecuencia]”.

    Después definan reglas para cuatro casos: equipo institucional, visitante común, visitante con permiso especial y equipo desconocido. Cierren con un objetivo medible y un plazo.

---

### DO — Poner en funcionamiento lo planificado

1. **¿Cómo funcionaría el procedimiento de principio a fin?**

    Describan qué ocurre desde la solicitud de la visita hasta el retiro del acceso. Asignen responsables y señalen qué registro o evidencia deja cada paso.

2. **¿Cómo identificarían a los equipos cuando intentan conectarse?**

    Comparen estas opciones: contraseña compartida, código temporal individual, clave individual por equipo y certificado digital.

    Elijan una opción para equipos institucionales y otra para visitantes. Expliquen cómo evitarían que la misma credencial se use también en un teléfono.

3. **¿Qué acceso recibiría cada equipo y qué registros deberían unirse?**

    Apliquen **mínimo privilegio**: cada equipo recibe solo el acceso que necesita.

    Indiquen cómo separarían los equipos, cómo registrarían el punto de acceso (AP) o puerto utilizado y cómo relacionarían visita, autenticación, IP y firewall.

    Si el consultor solicita RDP, SSH u otro servicio, expliquen cómo autorizarían ese acceso sin abrirlo para todos.

---

### CHECK — Comprobar que la mejora funciona

1. **¿Cómo probarían el registro y autorización de los equipos?**

    Prueben un equipo desconocido, el uso de la credencial en un teléfono y una reconexión con una MAC privada diferente. Indiquen qué debería ocurrir en cada caso.

2. **¿Cómo comprobarían que cada equipo recibe solamente el acceso necesario?**

    Verifiquen que el consultor puede usar Internet, que no llega a recursos internos sin permiso y que un permiso especial funciona solo para el destino, servicio y horario aprobados.

3. **¿Cómo comprobarían la trazabilidad y el cierre?**

    Partan del identificador de la visita y reconstruyan quién se conectó, cuándo, desde qué AP o puerto, con qué IP y qué acceso recibió.

    Definan al menos tres medidas y el valor que indicaría “cumple” o “no cumple”. Incluyan la expiración de la credencial y de todo permiso temporal.

### Resultado del primer piloto

Después de diseñar las pruebas de CHECK, analicen estos resultados del primer piloto:

- la laptop registrada tuvo acceso a Internet y no pudo alcanzar los servidores internos;
- la misma credencial temporal pudo utilizarse simultáneamente desde un teléfono;
- después de reconectarse, la laptop utilizó otra MAC privada y apareció como un segundo equipo;
- el controlador Wi-Fi y DHCP tenían siete minutos de diferencia, por lo que reconstruir la conexión tomó 40 minutos; y
- la credencial venció correctamente, pero un permiso temporal del firewall continuó activo al día siguiente.

Si su diseño ya habría evitado alguno de estos resultados, expliquen qué decisión lo impediría y qué evidencia lo demostraría.

---

### ACT — Corregir y comenzar un nuevo ciclo

1. **¿Qué corregirían de inmediato?**

    Ordenen los hallazgos por prioridad. Para cada corrección indiquen qué riesgo reduce, quién la realizará y cuándo debe terminarla.

2. **¿Qué cambiarían para que el problema no se repita?**

    No se limiten a reparar el caso. Propongan cambios al procedimiento o al sistema para evitar la reutilización de credenciales, registros con horas distintas y permisos que no vencen.

3. **¿Qué llevarían al siguiente PLAN?**

    Indiquen qué regla, procedimiento, configuración o medida actualizarían. Señalen qué riesgo todavía quedaría y qué prueba repetirían para confirmar la mejora.

---

### Producto final — Explicación completa del ciclo PDCA

Dibujen primero cómo relacionarían la visita, el equipo, la conexión y el cierre del acceso.

Después redacten una explicación de **200 a 300 palabras**, organizada en cuatro párrafos:

> **PLAN:** “Examinamos [fuentes], identificamos el riesgo de [riesgo] y establecimos el objetivo de [objetivo].”
>
> **DO:** “Implementamos [medidas], bajo responsabilidad de [responsable], y produjimos como evidencia [evidencia].”
>
> **CHECK:** “Probamos [controles] y los resultados demostraron [resultado]; sin embargo, encontramos [hallazgo].”
>
> **ACT:** “Corregiremos [problema], mejoraremos [proceso] y en el siguiente PLAN revisaremos [aspecto].”

La explicación debe mantener esta relación:

| Riesgo identificado | Objetivo | Medida implementada | Evidencia de ejecución | Prueba de eficacia | Resultado | Mejora |
|---|---|---|---|---|---|---|
| PLAN | PLAN | DO | DO | CHECK | CHECK | ACT |

### Criterios de evaluación — 10 puntos

| Criterio | Puntaje |
|---|---:|
| PLAN identifica fuentes, riesgos, categorías y un objetivo verificable | 2 |
| DO integra procedimiento, responsables, autenticación, mínimo privilegio y evidencia | 2 |
| CHECK define pruebas reproducibles, resultados esperados e indicadores | 2 |
| ACT corrige hallazgos, trata causas y alimenta el siguiente ciclo | 2 |
| La explicación conecta claramente las cuatro fases | 2 |

---

*Siguiente: [ISO 27001 — Requisitos (Parte 1)](iso27001-requisitos-p1.md)*
