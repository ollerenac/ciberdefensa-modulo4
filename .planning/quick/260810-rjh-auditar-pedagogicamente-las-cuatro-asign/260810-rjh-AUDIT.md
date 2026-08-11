# Auditoría pedagógica y operativa del Módulo 4 — Defensiva

**Fecha de corte:** 10 de agosto de 2026 (America/Lima)

**Alcance:** cuatro asignaturas, 91 horas académicas, materiales de alumno y profesor, laboratorios, evaluaciones y plataforma de ejecución.

**Método:** inspección de todos los Markdown curriculares, navegación de MkDocs, planificación GSD y matriz oficial de ejecución; contraste de los puntos dependientes de versión con fuentes primarias. No se modificó contenido del curso.

## Dictamen ejecutivo

El curso tiene una base sustantiva y bastante mejor que la que sugieren sus portadas: contiene aproximadamente 83 000 palabras, objetivos explícitos, ejemplos militares frecuentes, siete soluciones de laboratorio y cinco evaluaciones. Sin embargo, **no debe considerarse todavía listo para publicación y dictado sin intervención**. La mayor parte de la deuda es corregible en tareas acotadas.

Los cuatro hallazgos que requieren atención inmediata son:

1. **P0 — confidencialidad de las evaluaciones:** las claves de respuesta están dentro de los mismos archivos que consume el sitio del alumno; además, las páginas bajo `docs/instructor/` se construyen como HTML y una persona que conozca la URL puede abrirlas. “No aparecer en la navegación” no equivale a control de acceso.
2. **P0 — laboratorios no reproducibles:** se mezclan WSL2, herramientas nativas de Windows, software de terceros y componentes obsoletos. HoneyPy está archivado y depende de Python 2; Snort 2.9 es una rama heredada; Squid para Windows depende de binarios no oficiales del proyecto. Una OVA estable eliminaría gran parte de esa variabilidad.
3. **P0 — señalización y horas contradictorias:** la portada y los índices dicen “En desarrollo” aunque el hito figura como terminado. ISO aparece como 24 h en el índice pero son 27 h en la matriz; Defensa en Profundidad aparece como 24 h pero son 21 h; las dos unidades de la Asignatura 1 también están desbalanceadas 20/12 frente a 19/13.
4. **P0/P1 — vigencia y preparación docente:** hay que incorporar ISO/IEC 27001:2022/Amd 1:2024, NIST SP 800-61 Rev. 3, CVSS v4.0 y el reglamento peruano vigente de protección de datos. El bloque ISO tiene buen material para el alumno, pero no una guía de conducción suficiente para un profesor que no domina la familia ISO 27000.

**Recomendación de plataforma:** usar **Ubuntu 24.04.4 LTS Desktop como OVA principal**, no Kali. El curso es defensivo, el público tiene conocimientos básicos y se necesita estabilidad. Kali puede conservarse como OVA opcional para un futuro rol atacante o una unidad de pentesting. La OVA no puede transportar el árbol de snapshots: se exporta un estado plano y cada alumno crea el snapshot base después de importarla.

## Rúbrica

Escala aplicada a cada dimensión:

| Puntaje | Interpretación operativa |
|---:|---|
| 0 | Ausente; impide alcanzar el resultado de aprendizaje. |
| 1 | Mención superficial o material no utilizable sin rediseño. |
| 2 | Parcial; el profesor debe completar una parte importante. |
| 3 | Suficiente y utilizable con preparación normal. |
| 4 | Rico, contextualizado, verificable y listo para ejecutar. |

Dimensiones: **F** fundamentos; **E** ejemplos contextualizados; **PG** práctica guiada o demostraciones; **EA** ejercicios del alumno; **EV** alineación de evaluación; **GD** guía/solución docente; **HA** ajuste a horas y audiencia; **VT** vigencia técnica; **RL** reproducibilidad de laboratorio. “N/A” no entra en el promedio.

| Asignatura | F | E | PG | EA | EV | GD | HA | VT | RL | Promedio | Condición actual |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|
| 1. Seguridad de la Información y Criptografía | 3.5 | 3.5 | 3.0 | 3.0 | 1.5 | 3.0 | 3.0 | 1.5 | 1.5 | 2.7 | Dictable solo después de sanear evaluaciones y validar laboratorios. |
| 2. Capas SI — Familia ISO 27000 | 3.5 | 3.5 | 3.0 | 3.5 | 1.5 | 2.5 | 2.5 | 2.5 | N/A | 2.8 | Buen núcleo; requiere guía docente, corrección horaria y actualización. |
| 2. Capas SI — Defensa en Profundidad | 3.5 | 4.0 | 2.5 | 2.5 | 1.5 | 1.5 | 2.5 | 2.0 | N/A | 2.5 | Rico en casos, débil en práctica formal y soporte al profesor. |
| 3. Gestión de Riesgos y Vulnerabilidades | 3.5 | 4.0 | 2.0 | 2.5 | 1.5 | 1.5 | 2.5 | 2.0 | N/A | 2.6 | Contenido fuerte pero denso; faltan guía y producto práctico integrador. |
| 4. Conferencias Magistrales | 2.5 | 3.5 | 1.5 | 1.5 | 2.0 | 1.0 | 3.0 | 2.0 | N/A | 2.1 | Requiere guion, dinámica y fuentes actuales antes del dictado. |

La puntuación baja de evaluación no significa que falten pruebas: significa que sus respuestas están expuestas al alumno, por lo que dejan de ser instrumentos confiables. La longitud de una página tampoco se tomó como equivalente a profundidad pedagógica.

## Hallazgos transversales

### T1. Estado editorial y de planificación inconsistente — severidad alta

- **Hecho observado:** `docs/index.md` presenta las cuatro asignaturas como “En desarrollo”; los índices de unidades conservan “Contenido en desarrollo — disponible en fases posteriores”; `docs/instructor/index.md` promete soluciones futuras aunque ya existen siete.
- **Hecho observado:** `.planning/STATE.md` declara el hito v1.0 al 100 %, mientras `.planning/REQUIREMENTS.md` conserva requisitos sin marcar y la integración OFFen usa el marcador `http://[ip-offen-server]`.
- **Impacto:** erosiona la confianza del alumno y dificulta saber qué está autorizado para dictarse.
- **Recomendación:** corregir portadas, horas y estado; después reconciliar los artefactos GSD con el producto real.

### T2. Material reservado sin separación real — severidad crítica

- **Hecho observado:** cinco archivos de examen incluyen bloques “Solo instructor — Clave de Respuestas”, por ejemplo `docs/capas-si/familia-iso27000/examen-parcial.md` y `docs/gestion-riesgos/mitigacion-controles/examen.md`.
- **Hecho observado:** las siete soluciones bajo `docs/instructor/`, entre ellas `docs/instructor/lab-auditoria-p1-solucion.md`, son parte del árbol que construye MkDocs. Ocultarlas del menú no las protege.
- **Impacto:** compromete calificaciones, rúbricas y respuestas antes de aplicar la evaluación.
- **Recomendación:** publicar una edición alumno sin claves y mantener banco, rúbricas y soluciones en un artefacto privado del instructor; añadir una prueba de build que falle si aparecen marcadores reservados en `site/`.

### T3. La “hora” oficial es académica — severidad media

- **Hecho observado:** la matriz oficial contabiliza horas de 45 minutos; por ejemplo, cuatro horas académicas equivalen a un bloque real de tres horas.
- **Inferencia:** parte de la elevada densidad aparente puede provenir de haber planificado como si cada hora fueran 60 minutos.
- **Recomendación:** todas las guías docentes deben expresar tanto horas académicas como minutos reales y reservar pausas, preguntas y evaluación formativa.

### T4. El nombre de la Asignatura 1 no coincide con sus temas — severidad media

- **Hecho observado:** la asignatura se llama “Seguridad de la Información y Criptografía”, pero sus contenidos son defensa de endpoint, firewall, antivirus, IDS/IPS, proxy y honeypots.
- **Hecho observado:** la matriz oficial tiene el mismo temario; por tanto, no es una omisión accidental del repositorio.
- **Recomendación:** no agregar criptografía fuera del programa sin aprobación. Aclarar el alcance en la portada o solicitar al responsable curricular que renombre la asignatura.

## Diagnóstico por asignatura

### 1. Seguridad de la Información y Criptografía — 32 h

**Fortalezas**

- **Hecho observado:** nueve páginas teóricas desarrollan controles, firewall, antivirus, proxy, IDS/IPS y honeypots con objetivos, ejemplos militares, advertencias y resúmenes. `docs/seguridad-criptografia/en-el-ordenador/cortafuegos-p1.md` construye fundamentos antes del laboratorio.
- **Hecho observado:** cinco componentes prácticos tienen procedimientos de alumno y siete soluciones de instructor en el conjunto del curso. Los dos laboratorios de iptables progresan desde reglas básicas hasta escenarios y diagnóstico; las soluciones incluyen tiempos, errores frecuentes y rúbricas.
- **Hecho observado:** `docs/seguridad-criptografia/en-la-red/honeypots-p2.md` intenta dar alternativas cuando la ruta principal falla, una señal pedagógica positiva.

**Brechas**

- **Crítica:** `docs/seguridad-criptografia/en-el-ordenador/examen-parcial.md` y `docs/seguridad-criptografia/en-la-red/examen-final.md` contienen las claves en el material publicable.
- **Alta:** `docs/seguridad-criptografia/en-el-ordenador/lab-iptables-p1.md` depende de WSL2; el firewall solo protege el espacio Linux, no Windows. Si se adopta VirtualBox, mantener WSL2 en paralelo añade conflicto operativo y dos plataformas distintas.
- **Alta:** el laboratorio de honeypot prioriza HoneyPy, repositorio archivado desde 2024 y basado en Python 2. La ruta descrita con Python 3 no es reproducible.
- **Alta:** el laboratorio y la solución rotulada como Suricata realmente usan Snort 2.9.x. Snort 2.9 es heredado y el nombre inconsistente impide saber qué motor se evaluará.
- **Media:** Squid en Windows depende de una compilación de terceros con limitaciones; la instalación soportada por distribución Linux es más estable.
- **Media:** los índices declaran 20 h para “En el ordenador” y 12 h para “En la red”; la ejecución oficial corresponde a 19 h y 13 h.

**Conclusión:** los fundamentos y ejemplos son suficientes; el problema principal no es “falta de texto”, sino una plataforma fragmentada y la vigencia de la práctica. Tras migrar cada laboratorio a la OVA, hacer un ensayo limpio y separar las respuestas, puede quedar como la asignatura técnicamente más sólida.

### 2. Capas SI — 48 h

#### 2A. Familia ISO 27000 — 27 h

**Fortalezas**

- **Hecho observado:** ocho páginas recorren visión general, ISO/IEC 27001, ISO/IEC 27002 e ISO/IEC 27005 con el caso militar JC-BC3 como hilo conductor. `docs/capas-si/familia-iso27000/iso27001-requisitos-p1.md` conecta alcance, contexto, partes interesadas y liderazgo.
- **Hecho observado:** dos laboratorios de auditoría interna producen evidencia, hallazgos y acciones, con soluciones separadas en `docs/instructor/lab-auditoria-p1-solucion.md` y `docs/instructor/lab-auditoria-p2-solucion.md`.
- **Hecho observado:** el bloque combina conceptos, análisis de evidencia, discusión y evaluación; es una base apropiada para aprendizaje aplicado sin necesidad de una VM.

**Brechas**

- **Crítica:** el examen y las soluciones de laboratorio se publican en el mismo sitio estático del alumno.
- **Alta:** no existe una guía que le diga al profesor qué explicar, demostrar, preguntar y esperar minuto a minuto. Un experto puede dictarlo; un docente nuevo en ISO no tiene andamiaje suficiente.
- **Alta:** el índice declara 24 h, mientras la distribución oficial suma 27 h: 6 h generalidades, 5 h ISO/IEC 27001, 5 h ISO/IEC 27002, 5 h ISO/IEC 27005, 1 h de examen y 5 h de laboratorio de auditoría.
- **Media:** `docs/capas-si/familia-iso27000/iso27000-generalidades-p2.md` presenta PDCA como principio organizador de la norma. Es un modelo didáctico útil, pero debe rotularse como interpretación pedagógica y no como estructura normativa obligatoria de la edición 2022.
- **Media:** falta incorporar la Enmienda 1:2024 sobre cambio climático y distinguir con precisión requisitos certificables, orientación, controles de referencia y gestión de riesgos.

**Conclusión:** es el bloque con mejor combinación de teoría y ejercicios no técnicos, pero **no está listo para un profesor sin experiencia ISO**. La solución es una guía docente original apoyada en el caso JC-BC3, no reproducir las cláusulas protegidas de las normas.

#### 2B. Defensa en Profundidad — 21 h

**Fortalezas**

- **Hecho observado:** ocho páginas desarrollan capas humana, perimetral, red, endpoint, aplicaciones, datos y activos críticos. Los ejemplos militares son frecuentes y coherentes.
- **Hecho observado:** `docs/capas-si/defensa-profundidad/capa-activos-criticos-p2.md` incluye un ejercicio de respuesta a incidentes tipo tabletop con decisiones, funciones y preguntas de reflexión; es un buen patrón para replicar.
- **Hecho observado:** el contenido evita convertir defensa en profundidad en una simple lista de productos y enfatiza controles superpuestos.

**Brechas**

- **Crítica:** la clave del examen está en `docs/capas-si/defensa-profundidad/examen-final.md`.
- **Alta:** para 21 h no hay laboratorios formales ni soluciones de instructor; predominan lectura, preguntas y un tabletop. Faltan artefactos verificables, por ejemplo un mapa de capas, una matriz control-activo y un análisis de cobertura.
- **Alta:** la respuesta a incidentes sigue NIST SP 800-61 Rev. 2, retirada el 3 de abril de 2025 y sustituida por Rev. 3.
- **Media:** el índice declara 24 h en vez de las 21 h oficiales.

**Conclusión:** el contenido conceptual es rico y contextualizado, pero necesita dos ejercicios estructurados con rúbrica y una transición explícita de NIST Rev. 2 a Rev. 3. No requiere una VM para alcanzar sus resultados actuales.

### 3. Gestión de Riesgos y Vulnerabilidades — 8 h

**Fortalezas**

- **Hecho observado:** cuatro páginas teóricas cubren concepto, tipologías militares, métodos de identificación y tratamiento. `docs/gestion-riesgos/fundamentos-riesgo/tipos-riesgo-militar.md` aporta escenarios cercanos al público.
- **Hecho observado:** las páginas incluyen microejercicios, matrices, preguntas y decisiones, de modo que no son mera exposición narrativa.
- **Hecho observado:** los ejemplos vinculan activo, amenaza, vulnerabilidad, probabilidad, impacto y control, lo cual favorece una cadena causal comprensible.

**Brechas**

- **Crítica:** `docs/gestion-riesgos/mitigacion-controles/examen.md` incluye respuestas en el material publicable.
- **Alta:** unas 10 500 palabras en 8 horas académicas producen la mayor densidad del curso. Sin un guion, el profesor tenderá a exponer demasiado y practicar poco.
- **Alta:** no hay un taller integrador con plantilla y rúbrica que termine en registro de riesgos, valoración, propietario, tratamiento, riesgo residual y fecha de revisión.
- **Media:** `docs/gestion-riesgos/identificacion-vulnerabilidades/metodos-identificacion.md` enseña CVSS v3.1 sin transición a CVSS v4.0, la versión actual de FIRST.
- **Media:** no hay guía docente ni solucionario para los microejercicios; además existen referencias Markdown malformadas que deben pasar un comprobador de enlaces.

**Conclusión:** tiene buenos fundamentos y excelentes ejemplos para el tiempo disponible, pero está sobrecargada. Debe podarse la exposición y convertir el tiempo recuperado en un taller de registro de riesgos con un producto calificable.

### 4. Conferencias Magistrales — 3 h

**Fortalezas**

- **Hecho observado:** `docs/conferencias/operaciones-paz/operaciones-paz-globales.md` y `docs/conferencias/responsabilidad-social/impacto-responsabilidad-social.md` presentan escenarios éticos y militares apropiados para discusión.
- **Hecho observado:** la matriz no exige examen, por lo que una dinámica reflexiva breve es coherente con el carácter magistral.

**Brechas**

- **Alta:** no hay guion del conferencista, tiempos, diapositivas mínimas, preguntas detonantes, respuestas esperadas ni instrumento breve para comprobar aprendizaje.
- **Alta:** el contenido debe enlazar la estrategia vigente de transformación digital de UN Peacekeeping y su política actual de integridad de la información.
- **Alta:** en privacidad peruana no basta citar la Ley 29733 de 2011; el Decreto Supremo 016-2024-JUS entró en vigor el 31 de marzo de 2025.
- **Media:** 3 600 palabras en 3 horas académicas dejan poco espacio para discusión si se leen como clase expositiva.

**Conclusión:** es útil como lectura base, no todavía como experiencia magistral lista para facilitar. Requiere una ficha docente por conferencia y una evidencia mínima de salida, por ejemplo un análisis de decisión de una página.

## Plataforma Linux y OVA

### Decisión preliminar

| Opción | Ajuste al curso | Ventajas | Riesgos | Decisión |
|---|---|---|---|---|
| Kali Linux | Bajo/medio como plataforma principal | Muchas herramientas de seguridad; imágenes virtuales oficiales; familiar en pentesting. | Está dirigida a especialistas, presupone Linux, usa rama rolling y aumenta herramientas/superficie que el curso defensivo no necesita. | No usar como OVA principal. Reservar una OVA opcional futura para rol atacante controlado. |
| Ubuntu 24.04.4 LTS Desktop | Alto | Estable, soporte estándar hasta mayo de 2029, amigable para principiantes, paquetes defensivos mantenidos y cercana a los labs Ubuntu ya escritos. | Hay que instalar y fijar el conjunto de herramientas; requiere mantenimiento de la imagen. | **Recomendada para el piloto inmediato.** |
| Debian estable con escritorio | Medio/alto | Muy estable, liviana y reproducible. | Más fricción inicial para alumnos nuevos y menor continuidad con el material existente. | Alternativa si se prioriza austeridad sobre facilidad docente. |

**Hecho externo:** la propia documentación de Kali dice que no se recomienda como escritorio general para personas no familiarizadas con Linux. `kali-rolling` se actualiza continuamente y no garantiza el mismo nivel de prueba funcional que una instantánea.

**Inferencia:** una distribución excelente para pentesting no es automáticamente la mejor base para enseñar iptables, proxy, IDS y honeypots a principiantes.

**Recomendación:** construir la primera imagen sobre Ubuntu 24.04.4 LTS y revisar la decisión después de un ensayo completo de los laboratorios.

### Perfil de la VM principal

| Elemento | Recomendación |
|---|---|
| Hipervisor | Una versión 7.1.x de VirtualBox fijada para todos los hosts; probar exactamente la versión que se desplegará. |
| Guest | Ubuntu 24.04.4 LTS Desktop amd64, instalación mínima con GUI. |
| Recursos recomendados | 4 vCPU, 6 GB RAM, disco dinámico de 60 GB; ampliar a 80 GB si se almacenan PCAP, logs y Cowrie. |
| Recursos mínimos | 2 vCPU, 4 GB RAM, disco dinámico de 40 GB para contingencia. |
| Compatibilidad host | Windows 10/11 x86-64, VT-x/AMD-V y SLAT disponibles; validar con `systeminfo` y BIOS/UEFI. |
| Adaptador 1 | NAT, para actualizaciones y descarga controlada. No exponer servicios hacia la LAN. |
| Adaptador 2 | Host-only, deshabilitado por defecto; habilitar únicamente en ejercicios guiados que necesiten comunicación host-guest. |
| Prohibición por defecto | No usar Bridged; no apuntar a redes o sistemas reales; no incluir malware real. |
| Integración | Portapapeles, drag-and-drop, carpetas compartidas y USB deshabilitados por defecto. Guest Additions solo si una necesidad didáctica lo justifica. |
| Herramientas base | `iptables`/compatibilidad nftables, Squid, Wireshark/tcpdump, curl, netcat, Python 3 + venv, un IDS decidido por spike, Cowrie o simulador local seguro, PCAP y logs de práctica. |
| Credenciales | No distribuir una contraseña común permanente. Forzar cambio o individualización en la primera sesión; no incluir claves, tokens ni historial del constructor. |
| Actualizaciones | Manifestar fecha, versiones y SHA-256; congelar la imagen durante la cohorte y reconstruir entre cohortes. |

Con 16 GB de RAM en el host, 6 GB para el guest deja margen razonable para Windows, navegador y herramientas del aula. Los 20 vCPU anunciados no justifican asignarlos a la VM: 4 vCPU reducen contención y son suficientes para estos laboratorios.

### Red, WSL2 y aislamiento

- **Hecho externo:** VirtualBox usa NAT como modo simple y aislado por defecto; host-only permite comunicación host-guest sin acceso directo a la red física; bridged conecta la VM a la LAN.
- **Hecho externo:** WSL2 se ejecuta en una VM administrada y Microsoft advierte dificultades de convivencia con soluciones de virtualización de terceros. Oracle también documenta problemas cuando Hyper-V/Virtual Machine Platform/Windows Hypervisor Platform están activos.
- **Recomendación:** elegir **una sola ruta oficial para el aula: VirtualBox + OVA**. En el ensayo del laboratorio, comprobar el estado de Hyper-V, WSL2, VBS/Memory Integrity y BIOS. Documentar una ruta aprobada por TI; no pedir a cada alumno que improvise cambios de seguridad del host.
- **Seguridad:** cualquier honeypot o servicio vulnerable debe escuchar solo en NAT/host-only y recibir tráfico sintético del propio laboratorio. El profesor debe leer una regla de autorización antes de cada práctica.

### OVA y política real de snapshots

**Hecho externo:** OVF/OVA empaqueta discos y configuración, pero no representa snapshots; al exportar una VM con snapshots se aplana el estado actual. Por tanto, no es posible entregar “la OVA con snapshots restaurables” como árbol preconstruido.

Flujo recomendado:

1. Construir la imagen dorada en un host limpio y registrar ISO, paquetes, versiones y scripts.
2. Instalar las herramientas elegidas y ejecutar un autotest de cada laboratorio.
3. Eliminar secretos, descargas personales, historial sensible, identificadores y llaves SSH reutilizables; apagar limpiamente.
4. Exportar OVA con manifiesto, calcular SHA-256 y guardar ambos en el medio de distribución.
5. Importar esa OVA en al menos dos equipos que reproduzcan la imagen Windows del laboratorio y ejecutar todos los ejercicios desde cero.
6. Cada alumno importa la OVA y crea localmente `00-BASE-IMPORTADA` antes de tocarla.
7. Antes de cada práctica crea `01-ANTES-IPTABLES`, `02-ANTES-PROXY`, etc.; al finalizar elimina snapshots innecesarios para no agotar disco.
8. El profesor conserva la OVA prístina, el checksum y una copia de recuperación en servidor/USB.

### Criterios de aceptación de la futura OVA

- Importa y arranca sin error en la versión fijada de VirtualBox sobre la imagen Windows 10/11 real del laboratorio.
- La suma SHA-256 coincide y existe un manifiesto legible de procedencia y versiones.
- Todos los laboratorios técnicos pasan con una cuenta de alumno, sin acceso del constructor y sin depender de WSL2.
- Los adaptadores, servicios y puertos cumplen la política de aislamiento; ningún honeypot queda expuesto a la LAN.
- El snapshot base se crea y restaura después de importar; se demuestra una recuperación completa ante un firewall mal configurado.
- El procedimiento de importación, primer arranque, cambio de contraseña y recuperación cabe en una hoja para el alumno.

## Guía docente ISO 27000

### Alcance que necesita el profesor

La guía debe ser un documento privado y original, complementario a las páginas del alumno. Debe permitir que una persona con experiencia general en ciberseguridad, pero no en ISO, conduzca las 27 horas académicas con seguridad conceptual.

Cada sesión debe contener:

1. propósito, prerrequisitos y resultados observables;
2. duración en horas académicas de 45 minutos y minutos reales;
3. mapa de conceptos y relación con el caso JC-BC3;
4. guion del profesor por bloques, ejemplos y preguntas de comprobación;
5. ejercicio, material entregable, respuesta modelo o criterios de buena respuesta;
6. errores frecuentes —por ejemplo confundir riesgo con vulnerabilidad, control con requisito, auditoría con inspección o SoA con catálogo completo—;
7. evidencia que debe recoger el alumno y rúbrica;
8. transición hacia la siguiente sesión y lectura oficial/licenciada necesaria.

### Secuencia propuesta de 27 h académicas

| Bloque | Horas académicas | Minutos reales | Producto del alumno | Intervención docente clave |
|---|---:|---:|---|---|
| Generalidades familia ISO 27000 | 6 | 270 | Mapa de familia, alcance preliminar y partes interesadas de JC-BC3. | Separar vocabulario, requisitos, controles y riesgo; introducir SGSI como sistema de gestión. |
| ISO/IEC 27001:2022 | 5 | 225 | Alcance, política breve, objetivos y evidencia esperada. | Explicar qué es certificable y cómo se demuestra, sin leer cláusulas como receta. |
| ISO/IEC 27002:2022 | 5 | 225 | Selección justificada de controles y borrador de SoA. | Exigir vínculo riesgo-control, aplicabilidad y evidencia; no elegir controles por intuición. |
| ISO/IEC 27005:2022 | 5 | 225 | Registro de riesgos con criterios, propietario, tratamiento y riesgo residual. | Mantener trazabilidad activo-amenaza-vulnerabilidad-impacto y apetito de riesgo. |
| Evaluación parcial | 1 | 45 | Respuestas sin acceso a la clave. | Aplicar una versión segura y registrar errores para retroalimentación. |
| Laboratorio de auditoría interna | 5 | 225 | Plan, lista de evidencia, hallazgos, no conformidad/observación y acción correctiva. | Separar evidencia objetiva de opinión; moderar severidad y calidad de redacción. |

### Ejercicios mínimos que deben quedar formalizados

1. **Contexto y alcance:** decidir qué entra y sale del SGSI de JC-BC3, con interfaces y justificación.
2. **Partes interesadas:** distinguir requisito legal, contractual, operacional y expectativa no obligatoria.
3. **Riesgo a control:** recibir cinco riesgos y justificar tratamiento, control, propietario y evidencia.
4. **Declaración de Aplicabilidad:** completar un extracto original sin reproducir el Anexo A, justificando inclusión/exclusión.
5. **Registro de riesgos:** valorar con una escala definida, documentar riesgo residual y criterio de aceptación.
6. **Auditoría basada en evidencia:** clasificar evidencia suficiente/insuficiente y redactar hallazgo, criterio, condición y consecuencia.
7. **Revisión por la dirección:** role-play breve con comandante, responsable del SGSI, propietario de riesgo y auditor.

Cada ejercicio necesita versión alumno, versión instructor, rúbrica de 4 niveles y ejemplo de respuesta razonable; ISO admite decisiones contextuales, por lo que no todo debe reducirse a una única frase “correcta”.

### Límite de copyright y rigor normativo

- **Hecho externo:** ISO protege sus publicaciones. La guía puede resumir, explicar y crear escenarios propios, pero no debe copiar tablas de controles ni texto extenso de cláusulas.
- **Recomendación:** disponer de una copia autorizada de las normas para el profesor; enlazar páginas oficiales; redactar todas las plantillas y casos desde cero; marcar claramente qué afirmaciones son explicación didáctica.
- **Recomendación:** incorporar ISO/IEC 27001:2022/Amd 1:2024 y verificar si el contexto de la organización considera relevante el cambio climático, sin inventar obligaciones más allá del texto autorizado.

## Trazabilidad de hechos externos

Todas las URL siguientes son fuentes primarias consultadas el 10 de agosto de 2026.

| Afirmación | Edición/versión | Organismo | URL oficial | Fecha de consulta |
|---|---|---|---|---|
| ISO/IEC 27001 define requisitos de un SGSI y la edición publicada es la tercera. | ISO/IEC 27001:2022 | ISO | https://www.iso.org/standard/27001 | 2026-08-10 |
| Existe una enmienda sobre cambios de acción climática. | ISO/IEC 27001:2022/Amd 1:2024 | ISO | https://www.iso.org/standard/88435.html | 2026-08-10 |
| ISO/IEC 27002 ofrece orientación sobre controles. | ISO/IEC 27002:2022 | ISO | https://www.iso.org/standard/75652.html | 2026-08-10 |
| ISO/IEC 27005 orienta la gestión de riesgos de seguridad de la información. | ISO/IEC 27005:2022 | ISO | https://www.iso.org/standard/80585.html | 2026-08-10 |
| Kali está orientada a pentesting/especialistas y no se recomienda como escritorio general para principiantes. | Documentación vigente | Kali Linux | https://www.kali.org/docs/introduction/should-i-use-kali-linux/ | 2026-08-10 |
| `kali-rolling` cambia continuamente; `kali-last-snapshot` prioriza una instantánea más estable. | Ramas vigentes | Kali Linux | https://www.kali.org/docs/general-use/kali-branches/ | 2026-08-10 |
| Las imágenes Kali oficiales deben verificarse con SHA-256. | Imágenes oficiales | Kali Linux | https://www.kali.org/docs/introduction/download-official-kali-linux-images/ | 2026-08-10 |
| Ubuntu 24.04 LTS tiene soporte estándar hasta mayo de 2029. | Ubuntu 24.04 LTS | Canonical | https://ubuntu.com/about/release-cycle | 2026-08-10 |
| VirtualBox es el hipervisor elegido para el piloto. | Portal oficial | Oracle VirtualBox | https://www.virtualbox.org/ | 2026-08-10 |
| OVF/OVA empaqueta el estado actual, pero OVF no describe snapshots. | VirtualBox 7.1 | Oracle | https://docs.oracle.com/en/virtualization/virtualbox/7.1/user/Introduction.html | 2026-08-10 |
| NAT, host-only y bridged tienen alcances de red diferentes. | VirtualBox 7.1 | Oracle | https://docs.oracle.com/en/virtualization/virtualbox/7.1/user/networkingdetails.html | 2026-08-10 |
| Hyper-V y componentes relacionados pueden afectar a VirtualBox. | VirtualBox 7.1 | Oracle | https://docs.oracle.com/en/virtualization/virtualbox/7.1/user/KnownIssues.html | 2026-08-10 |
| Windows requiere virtualización habilitada, SLAT y memoria suficiente para virtualización. | Windows 10/11 | Microsoft | https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/host-hardware-requirements | 2026-08-10 |
| WSL2 usa una VM administrada y su convivencia con terceros debe validarse. | WSL 2 | Microsoft | https://learn.microsoft.com/en-us/windows/wsl/compare-versions | 2026-08-10 |
| NIST SP 800-61 Rev. 2 fue retirada y sustituida por Rev. 3. | Rev. 2 / Rev. 3 | NIST | https://csrc.nist.gov/pubs/sp/800/61/r2/final | 2026-08-10 |
| La guía vigente de respuesta a incidentes se alinea con CSF 2.0. | NIST SP 800-61 Rev. 3, abril 2025 | NIST | https://csrc.nist.gov/pubs/sp/800/61/r3/final | 2026-08-10 |
| CVSS v4.0 es el estándar actual publicado por FIRST. | CVSS v4.0 | FIRST | https://www.first.org/cvss/v4.0/ | 2026-08-10 |
| HoneyPy está archivado, usa Python 2 y ya no está en desarrollo activo. | Repositorio archivado | HoneyPy | https://github.com/foospidy/HoneyPy | 2026-08-10 |
| Cowrie es una alternativa mantenida para honeypot SSH/Telnet con Python moderno. | Repositorio vigente | Cowrie | https://github.com/cowrie/cowrie | 2026-08-10 |
| El proyecto Squid no distribuye binarios propios para Windows; son de terceros. | FAQ vigente | Squid | https://wiki.squid-cache.org/KnowledgeBase/Windows | 2026-08-10 |
| El Reglamento de la Ley 29733 fue aprobado por DS 016-2024-JUS y entró en vigor el 31-03-2025. | DS 016-2024-JUS | Gobierno del Perú | https://www.gob.pe/institucion/anpd/campa%C3%B1as/128319-nuevo-reglamento-de-proteccion-de-datos-personales | 2026-08-10 |
| UN Peacekeeping mantiene una estrategia vigente de transformación digital. | Estrategia actualizada 2026 | Naciones Unidas | https://peacekeeping.un.org/en/strategy-for-the-digital-transformation-of-un-peacekeeping | 2026-08-10 |

## Backlog priorizado de gsd-quick

Cada fila está deliberadamente acotada. “Archivos probables” orienta la planificación de la quick; no autoriza cambios fuera de su alcance.

### P0 — antes de publicar o dictar

| ID | gsd-quick propuesta | Alcance y archivos probables | Dependencias | Criterio de aceptación |
|---|---|---|---|---|
| P0-1 | Separar material alumno/instructor | Cinco exámenes, `docs/instructor/`, `mkdocs.yml`, prueba de build. | Ninguna. | `site/` público no contiene claves, rúbricas reservadas ni soluciones; el profesor recibe un artefacto privado verificable. |
| P0-2 | Corregir estado, horas y alcance visible | `docs/index.md` y todos los `index.md`; aclarar 19/13, 27/21 y título de Asignatura 1. | P0-1 aconsejable, no obligatoria. | La portada coincide con la matriz oficial y no queda ningún “En desarrollo” falso. |
| P0-3 | Especificar OVA v0.1 y ensayo de hipervisor | ADR/especificación nueva bajo documentación del proyecto; checklist del host. | Aprobación de TI sobre VirtualBox y conflicto Hyper-V/WSL2. | Quedan fijados guest, hipervisor, recursos, red, seguridad, herramientas, checksum, snapshots y matriz de prueba. |
| P0-4 | Construir y probar OVA Ubuntu v0.1 | Scripts/manifiesto de imagen, instructivo de importación y autotest; no editar aún todas las lecciones. | P0-3. | OVA importada en dos hosts limpios, SHA-256 verificado, snapshot restaurado y pruebas base aprobadas. |
| P0-5 | Migrar iptables a la OVA | Dos labs y dos soluciones iptables. | P0-4. | Ambos labs pasan desde snapshot limpio sin WSL2 y explican qué protege cada regla. |
| P0-6 | Migrar proxy a la OVA | Páginas Proxy Squid y solución. | P0-4. | Squid de repositorio Ubuntu funciona en ensayo limpio, genera evidencia y se restaura. |
| P0-7 | Decidir y migrar IDS | Páginas IDS/IPS, lab/solución hoy rotulados Snort/Suricata. | P0-4; spike Snort 3 vs Suricata. | Un solo motor y nombre en alumno/profesor; reglas, PCAP, alertas y rúbrica reproducibles. |
| P0-8 | Reemplazar HoneyPy | Páginas honeypot y solución; Cowrie o simulador local seguro. | P0-4; decisión de aislamiento. | No hay Python 2 ni repositorio archivado; servicio solo en red de laboratorio y evidencia reproducible. |
| P0-9 | Crear guía docente ISO, tramo 1 | Generalidades + ISO/IEC 27001; documento privado de profesor. | Copia autorizada o acceso oficial a la norma. | Guion para 11 h académicas con tiempos, preguntas, ejercicios, respuestas esperadas y errores frecuentes. |
| P0-10 | Crear guía docente ISO, tramo 2 | ISO/IEC 27002 + 27005 + examen. | P0-9. | Guion para 11 h, ejercicio SoA original, registro de riesgos, rúbricas y examen seguro. |
| P0-11 | Completar paquete de auditoría ISO | Dos labs y soluciones existentes. | P0-9 y P0-10. | Guion de 5 h, evidencia modelo, calibración de hallazgos y rúbrica de cuatro niveles. |
| P0-12 | Actualizar referencias urgentes | ISO Amd 1:2024, NIST 800-61r3, CVSS 4.0, DS 016-2024-JUS y UN Peacekeeping. | Ninguna. | Cada concepto vigente se explica con fuente primaria y fecha; lo heredado queda rotulado como tal. |

El orden temporal debe respetar la ejecución oficial: Asignatura 1 inicia el **13 de agosto de 2026**, laboratorios técnicos el **17–18 de agosto**, ISO el **19–24 de agosto**, Defensa el **24–26**, Riesgos el **27** y Conferencias el **28**. Si esas fechas siguen vigentes, P0-1, P0-3 y los laboratorios necesarios son una contingencia inmediata; la guía ISO debe producirse por tramos antes del 19 de agosto.

### P1 — calidad pedagógica de la cohorte

| ID | gsd-quick propuesta | Alcance y archivos probables | Dependencias | Criterio de aceptación |
|---|---|---|---|---|
| P1-1 | Añadir evaluación formativa uniforme | Páginas teóricas de Asignatura 1. | Labs P0-5 a P0-8 estabilizados. | Cada bloque tiene comprobación breve, respuesta docente y vínculo al objetivo. |
| P1-2 | Crear dos talleres de Defensa en Profundidad | Capas red/endpoint y activos críticos; nuevas versiones alumno/instructor. | P0-1 y P0-12. | Mapa de capas y tabletop producen artefactos con rúbrica, ejecutables dentro de 21 h. |
| P1-3 | Taller integrador de riesgos | Asignatura 3; plantilla de registro y solución privada. | P0-1 y actualización CVSS. | Cada equipo entrega riesgo inherente/residual, tratamiento, propietario, evidencia y revisión; rúbrica validada. |
| P1-4 | Guías de conferencias | Dos conferencias, fichas privadas del docente. | P0-12. | Cada conferencia tiene guion, fuentes actuales, dinámica y ticket de salida dentro de 3 h. |
| P1-5 | Higiene de enlaces y referencias | Todos los Markdown, configuración CI o script local. | Ninguna. | Cero enlaces Markdown malformados; enlaces internos y fuentes primarias pasan comprobación automatizada. |
| P1-6 | Calibrar horas y carga cognitiva | Todas las guías/índices y matriz de sesiones. | P0-2; guías ISO. | Cada sesión diferencia horas de 45 min, práctica, pausa y evaluación; ninguna exige leer todo el texto en clase. |
| P1-7 | Versionar banco de evaluaciones | Cinco exámenes y repositorio privado del profesor. | P0-1. | Existen al menos dos formas equivalentes, blueprint por objetivos y procedimiento de rotación. |

### P2 — consolidación y extensiones

| ID | gsd-quick propuesta | Alcance y archivos probables | Dependencias | Criterio de aceptación |
|---|---|---|---|---|
| P2-1 | Reconciliar estado GSD | `.planning/STATE.md`, `.planning/REQUIREMENTS.md`, roadmap/handoff. | P0 y P1 relevantes cerradas. | Estado y requisitos describen lo realmente entregado; no se marca OFFen sin prueba. |
| P2-2 | Integración OFFen real | Labs seleccionados y configuración del entorno. | OVA estable y endpoint oficial disponible. | No hay IP de marcador; conexión, caída controlada y alternativa offline están probadas. |
| P2-3 | OVA Kali opcional de rol atacante | Imagen separada, `kali-last-snapshot`, red aislada e instructivo. | OVA defensiva estable; necesidad curricular aprobada. | Solo herramientas necesarias, checksum, credenciales individualizadas y escenario autorizado; nunca sustituye la OVA base. |
| P2-4 | UAT docente y estudiantil | Recorrido completo con un profesor no autor y 2–3 alumnos representativos. | P0/P1. | Se registran tiempo real, errores, abandonos y mejoras; no quedan bloqueos severos antes de la cohorte siguiente. |

## Siguiente paso recomendado

Ejecutar primero una **gsd-quick P0-1: separar material alumno/instructor**, porque es independiente, pequeña y corrige un riesgo crítico de integridad académica. En paralelo operativo —sin mezclar los cambios en la misma quick—, TI debe responder el checklist de P0-3 sobre VirtualBox/Hyper-V debido a la cercanía de los laboratorios. Después conviene abordar P0-9, la primera mitad de la guía ISO, porque su fecha de uso es inmediatamente posterior.

La secuencia recomendada de las próximas tres quicks es:

1. P0-1 — saneamiento de evaluaciones y soluciones;
2. P0-3 — especificación cerrada de OVA y compatibilidad del laboratorio;
3. P0-9 — guía docente de Generalidades + ISO/IEC 27001.

Con esas tres decisiones cerradas, la construcción de la OVA y la mejora curricular pueden avanzar sin volver a discutir arquitectura, confidencialidad ni alcance docente.
