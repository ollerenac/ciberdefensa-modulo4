---
# Horas asignadas: 1 hr
# Tipo: Examen
---

# Examen Final — Capas SI (Defensa en Profundidad)

> **Duración:** 60 minutos | **Preguntas:** 15 | **Valor:** 15 puntos (1 punto por pregunta)

!!! warning "Instrucciones"
    - Responder de forma individual. No consultar apuntes, libros ni dispositivos electrónicos.
    - Para las preguntas de opción múltiple (Sección A), marcar **una sola respuesta** por pregunta en la hoja de respuestas.
    - Para las preguntas de análisis de escenario (Sección B), leer el **Escenario de Análisis** antes de responder. Las preguntas referencian el mapa de red y la lista de amenazas.
    - Para las preguntas de respuesta corta (Sección C), escribir en el espacio indicado en la hoja de examen.
    - Escribir nombre completo y número de identificación en la hoja de respuestas antes de comenzar.
    - Al terminar, entregar la hoja de respuestas al instructor. Conservar el enunciado.

---

## Escenario de Análisis

### Contexto

La **Jefatura de Comunicaciones del Batallón de Comunicaciones N°3** (JC-BC3) opera la infraestructura de comunicaciones e información del batallón. El siguiente mapa de red representa el estado actual de su arquitectura. Analizarlo cuidadosamente antes de responder la Sección B.

### Mapa de Red — JC-BC3

```
                           INTERNET
                               │
                         ┌─────┴─────┐
                         │  ROUTER   │  ← sin restricción outbound
                         └─────┬─────┘
                               │
                         ┌─────┴─────┐
                         │ FIREWALL  │  ← reglas: solo bloquea puerto 23 (Telnet)
                         └─────┬─────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
        ┌─────┴─────┐   ┌──────┴──────┐   ┌────┴─────┐
        │  SERVIDOR │   │  SERVIDOR   │   │  DMZ     │
        │    C2     │   │  ARCHIVOS   │   │  VACÍA   │
        │  (SCIF)   │   │(datos CONF.)│   │(sin uso) │
        └─────┬─────┘   └──────┬──────┘   └──────────┘
              │                │
       ┌──────┴──────┐  ┌──────┴──────┐
       │ PC_ADMIN_01 │  │  PC_OPS_02  │
       │(administra  │  │ (sin parche │
       │ SCIF + C2)  │  │ desde 2024) │
       └─────────────┘  └─────────────┘

       Red interna: sin segmentación de VLANs
       Protocolo SMBv1: habilitado en todos los equipos
       Copias de seguridad: unidad USB conectada permanentemente a SERVIDOR_ARCHIVOS
       Contraseñas: política "sin caducidad", longitud mínima 6 caracteres
       Acceso físico SCIF: tarjeta magnética (sin PIN ni biometría)
       Cifrado en reposo: deshabilitado en SERVIDOR_ARCHIVOS
       Logs de sistema: centralizados en PC_OPS_02 (sin SIEM)
       DNS interno: resuelve nombres; sin filtrado de dominios maliciosos
```

### Amenazas Identificadas

1. **A-01** — Un técnico recibe un correo con asunto "Actualización firmware radio táctica" y un adjunto ejecutable. Lo abre sin verificar el remitente.
2. **A-02** — Un atacante externo descubre que SMBv1 está habilitado y lanza una herramienta de propagación automática de ransomware por la red interna.
3. **A-03** — Un agente adversario accede físicamente al área del SCIF usando una tarjeta magnética clonada obtenida de un técnico durante una operación de pretexting.
4. **A-04** — El ransomware cifra SERVIDOR_ARCHIVOS y también cifra la copia de seguridad conectada permanentemente por USB.
5. **A-05** — Una estación de trabajo con usuario de dominio accede directamente a SERVIDOR_C2 porque no hay segmentación de red.

---

## Sección A: Opción Múltiple (Preguntas 1-3)

*Cada pregunta vale 1 punto. Marcar una sola respuesta.*

---

**P1.** En el modelo de defensa en profundidad, ¿qué principio fundamental distingue esta estrategia del modelo de "castillo con foso"?

- (A) El firewall perimetral es más potente y analiza el contenido de todos los paquetes
- (B) Cada capa de controles opera de forma independiente, de modo que el fallo de una capa no compromete la seguridad completa
- (C) Los controles técnicos reemplazan a los controles humanos, eliminando el error humano como vector
- (D) Toda la protección se concentra en el perímetro externo para detener al atacante antes de que entre

---

**P2.** ¿Cuál de las siguientes opciones describe correctamente la relación entre las fases del NIST SP 800-61?

- (A) Las cuatro fases (Preparación, Detección, Contención/Erradicación/Recuperación, Post-incidente) son secuenciales y no pueden solaparse
- (B) La Fase de Preparación es opcional si la organización tiene un firewall perimetral actualizado
- (C) La Fase de Preparación es la más importante porque define el equipo, los recursos y los procedimientos antes de que ocurra el incidente
- (D) La Fase Post-incidente solo aplica a incidentes de alto impacto; para incidentes menores se omite

---

**P3.** Una organización decide cifrar todos sus datos con BitLocker en los portátiles del personal, pero no implementa ningún control en la Capa 2 (Perimetral) ni en la Capa 5 (Aplicaciones). ¿Qué describe mejor este enfoque desde la perspectiva de la defensa en profundidad?

- (A) Es un enfoque correcto: BitLocker en Capa 6 protege los datos aunque fallen las capas anteriores
- (B) Es un enfoque incompleto: la defensa en profundidad requiere controles en múltiples capas; un atacante que pasa las Capas 2 y 5 puede acceder a datos en uso aunque el disco esté cifrado
- (C) Es un enfoque correcto si los portátiles nunca se conectan a internet
- (D) Es un enfoque incompleto solo si la organización no tiene antivirus instalado

---

## Sección B: Análisis de Escenario JC-BC3 (Preguntas 4-11)

*Basarse en el Mapa de Red y la Lista de Amenazas del Escenario de Análisis. Cada pregunta vale 1 punto.*

---

**P4.** La amenaza A-01 (técnico abre adjunto malicioso) explota una vulnerabilidad en la Capa 1 del modelo de defensa en profundidad. Identificar **qué control de la Capa 1 está ausente o deficiente** en la JC-BC3 según el mapa de red.

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P5.** El mapa muestra que el firewall solo bloquea el puerto 23 (Telnet) y no hay filtrado de tráfico saliente. En el contexto de la amenaza A-01, ¿qué riesgo específico crea esta deficiencia en la Capa 2 (Perimetral)?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P6.** La amenaza A-02 describe propagación de ransomware por SMBv1. ¿Qué dos controles — uno de la Capa 3 (Red Interna) y uno de la Capa 4 (Endpoint) — habrían mitigado o contenido esta amenaza? Nombrar el control y la capa correspondiente para cada uno.

*Respuesta:*

Control de Capa 3: ________________________________________________________________

Control de Capa 4: ________________________________________________________________

---

**P7.** La amenaza A-03 describe acceso físico no autorizado al SCIF con tarjeta magnética clonada. Esto representa un fallo en la Capa 1 (ingeniería social / pretexting) **y** en la Capa 2 (controles físicos del perímetro). ¿Qué control de acceso físico debería agregar la JC-BC3 para reducir el riesgo incluso si la tarjeta es clonada?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P8.** La amenaza A-04 muestra que el ransomware cifró tanto SERVIDOR_ARCHIVOS como su copia de seguridad USB. Esto representa un fallo en la Capa 7 (Activos Críticos). ¿Qué principio de continuidad operacional (BCP/DRP) se violó y cómo debería estar configurada la copia de seguridad para resistir este tipo de ataque?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P9.** La red interna de la JC-BC3 no tiene segmentación de VLANs. ¿Cómo permitió esta deficiencia que ocurriera la amenaza A-05 (estación de trabajo con acceso directo al SERVIDOR_C2) y qué control de la Capa 3 corregiría esto?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P10.** El mapa muestra que SERVIDOR_ARCHIVOS almacena datos Confidenciales pero el cifrado en reposo está deshabilitado. Identificar la capa del modelo de defensa en profundidad afectada y explicar el impacto de esta deficiencia en la confidencialidad de los datos si un atacante obtiene acceso físico al disco del servidor.

*Respuesta:*

Capa afectada: ________________________________________________________________

Impacto: ________________________________________________________________

________________________________________________________________

---

**P11.** Los logs del sistema están centralizados en PC_OPS_02 (una estación de trabajo, no un SIEM separado). Considerando el escenario de la amenaza A-02 (ransomware propagándose por SMBv1), ¿qué riesgo específico representa almacenar los logs en esa estación de trabajo durante un incidente activo?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

## Sección C: Respuesta Corta (Preguntas 12-15)

*Responder en el espacio indicado. Cada pregunta vale 1 punto. Se evalúa precisión del concepto, no extensión de la respuesta.*

---

**P12.** Después de contener el ransomware de la amenaza A-02, el Técnico de Sistemas propone restaurar los sistemas directamente desde la última copia de seguridad disponible (sin analizar el vector de entrada) para minimizar el tiempo de inactividad. ¿Por qué esta acción puede ser contraproducente según las fases NIST SP 800-61? ¿Qué fase se está omitiendo?

*Respuesta:* ________________________________________________________________

________________________________________________________________

________________________________________________________________

---

**P13.** Explicar la diferencia entre una **DMZ** (zona desmilitarizada) y la **red interna** en una arquitectura de seguridad perimetral. En el mapa de red de la JC-BC3, la DMZ está vacía (sin uso). ¿Qué tipo de servidor o servicio debería ubicarse en la DMZ y por qué?

*Respuesta:*

Diferencia DMZ / red interna: ________________________________________________________________

________________________________________________________________

Qué debería ir en la DMZ de la JC-BC3: ________________________________________________________________

________________________________________________________________

---

**P14.** La política de contraseñas de la JC-BC3 tiene longitud mínima de 6 caracteres y sin caducidad. Señalar **dos problemas** de seguridad que plantea esta política en relación con la Capa 1 (Humana) y la Capa 5 (Aplicaciones) del modelo de defensa en profundidad.

*Respuesta:*

Problema 1: ________________________________________________________________

________________________________________________________________

Problema 2: ________________________________________________________________

________________________________________________________________

---

**P15.** Una vez controlado el incidente de ransomware en la JC-BC3, el Comandante del batallón solicita un informe post-incidente. Mencionar **tres elementos** que debe contener ese informe según la Fase 4 (Post-incidente) del NIST SP 800-61.

*Respuesta:*

Elemento 1: ________________________________________________________________

Elemento 2: ________________________________________________________________

Elemento 3: ________________________________________________________________

---

!!! danger "Solo instructor — Clave de Respuestas"

    ## Clave de Respuestas

    ### Sección A: Opción Múltiple (P1-P3)

    | Pregunta | Respuesta | Justificación |
    |----------|-----------|---------------|
    | P1 | **B** | La defensa en profundidad se distingue del modelo castillo-con-foso precisamente porque cada capa opera de forma **independiente** — si el perímetro cae, las capas internas siguen activas. (A) describe inspección profunda de paquetes, no el principio DiD. (C) es incorrecto — la capa humana es la primera y más crítica, no se elimina. (D) describe exactamente el modelo castillo-con-foso que DiD reemplaza. |
    | P2 | **C** | NIST SP 800-61 establece explícitamente que la Preparación es la fase más importante: los incidentes no se gestionan bien si no se prepararon de antemano. (A) es incorrecto — las fases pueden solaparse (ej. contención y erradicación ocurren casi simultáneamente en algunos incidentes). (B) es incorrecto — la Preparación es obligatoria e independiente de los controles técnicos. (D) es incorrecto — el post-incidente (lessons learned) aplica a todos los incidentes para mejorar la postura de seguridad. |
    | P3 | **B** | La defensa en profundidad requiere controles en múltiples capas. BitLocker protege datos **en reposo** en el disco, pero si un atacante compromete la aplicación (Capa 5) con credenciales válidas, los datos se le presentan descifrados. Si pasa el perímetro (Capa 2), puede explotar vulnerabilidades en las aplicaciones. (A) y (C) son incorrectos por la misma razón: cada capa protege un vector diferente. (D) es incorrecto — el antivirus es Capa 4, y la deficiencia en Capas 2 y 5 es independiente del AV. |

    ---

    ### Sección B: Respuestas Esperadas para Análisis de Escenario (P4-P11)

    **P4 — Capa 1 ausente / deficiente (amenaza A-01):**

    Respuesta completa: La JC-BC3 carece de **formación y concienciación en seguridad** para el personal técnico. El técnico abrió un adjunto ejecutable sin verificar el remitente — comportamiento característico de personal sin entrenamiento en identificación de phishing. También puede mencionarse la ausencia de un **procedimiento claro de reporte** de correos sospechosos. El mapa no muestra ningún control organizativo visible (política de uso aceptable, sesiones de concienciación, simulaciones de phishing).

    *Criterio mínimo:* Identificar formación/concienciación en seguridad como control ausente + señalar que el vector fue phishing (ingeniería social). Mencionar "procedimiento de reporte" se acepta como respuesta adicional correcta.

    **P5 — Riesgo de firewall sin filtrado outbound (amenaza A-01 + Capa 2):**

    Respuesta completa: Si el adjunto malicioso instala un troyano o RAT (Remote Access Trojan), el malware puede establecer comunicación **saliente** con su servidor de Comando y Control (C2) externo usando el puerto 443 (HTTPS), que no está bloqueado. El firewall que solo bloquea Telnet (puerto 23) permite todo el tráfico saliente, incluyendo la exfiltración de datos y la recepción de instrucciones del atacante. El tráfico C2 sobre HTTPS es indistinguible del tráfico web normal para un firewall sin inspección de contenido.

    *Criterio mínimo:* Mencionar que el malware puede comunicarse hacia afuera (C2 / exfiltración) a través de un puerto permitido (443/80) porque no hay filtrado outbound.

    **P6 — Controles Capa 3 y Capa 4 para mitigar A-02:**

    Respuesta correcta:

    - **Control de Capa 3 (Red Interna):** Segmentación de red mediante VLANs. Con VLANs, el ransomware que se propaga por SMBv1 no puede saltar automáticamente de una VLAN a otra — necesitaría atravesar el firewall interno. La red plana (sin segmentación) permite propagación lateral sin restricciones.
    - **Control de Capa 4 (Endpoint):** Deshabilitar SMBv1 en todos los equipos (gestión de parches / hardening de protocolos). WannaCry utilizó exactamente este vector. Con SMBv1 deshabilitado, el exploit de propagación automática no funciona. También es válido mencionar EDR (detección de comportamiento de propagación lateral).

    *Criterio mínimo:* Un control de Capa 3 correcto (VLANs / segmentación) Y un control de Capa 4 correcto (deshabilitar SMBv1 / parches / EDR).

    **P7 — Control de acceso físico adicional para el SCIF (amenaza A-03, Capas 1 y 2):**

    Respuesta completa: Implementar **autenticación multifactor (MFA) física** para el acceso al SCIF — por ejemplo, tarjeta magnética + PIN o tarjeta magnética + biometría (huella dactilar). Con dos factores, clonar la tarjeta no es suficiente: el atacante también necesita el PIN o la biometría del usuario legítimo. Esto aplica el principio "algo que tienes + algo que eres/sabes" de la autenticación en Capa 5, aplicado al control físico de la Capa 2. También es válido mencionar cámara de seguridad + registro de acceso como control compensatorio.

    *Criterio mínimo:* Mencionar autenticación multifactor física (PIN, biometría, o cualquier segundo factor) como control que resiste la clonación de la tarjeta.

    **P8 — Fallo de BCP/DRP: backup USB permanente cifrado (amenaza A-04, Capa 7):**

    Respuesta completa: Se violó el principio de **aislamiento de las copias de seguridad** (también conocido como la regla 3-2-1: 3 copias, 2 medios distintos, 1 offsite / desconectado). Una copia de seguridad conectada permanentemente a la misma red que los sistemas de producción es vulnerable al mismo ransomware que afecta a esos sistemas. La copia debería estar en uno de estos formatos: (1) **offline** (desconectada tras la copia), (2) **offsite** (ubicación física separada), o (3) **inmutable** (almacenamiento que no admite sobrescritura durante un período fijo). En términos de RTO/RPO: perder la copia de seguridad extiende drásticamente el RTO y puede hacer el RPO irrecuperable.

    *Criterio mínimo:* Identificar que el backup debe estar aislado / desconectado (offline u offsite) para resistir el mismo ataque que afecta a los sistemas productivos.

    **P9 — Sin segmentación VLANs y amenaza A-05 (Capa 3):**

    Respuesta completa: Sin segmentación de VLANs, todos los dispositivos de la red interna comparten el mismo dominio de broadcast y pueden comunicarse directamente entre sí sin pasar por ningún control. Una estación de trabajo de usuario operacional (PC con usuario de dominio) puede abrir una conexión SMB, RDP o cualquier protocolo de gestión directamente al SERVIDOR_C2 porque no hay firewall interno ni ACL que separe los segmentos. El control corrector es implementar **VLANs** que separen: VLAN para estaciones de usuario, VLAN para servidores críticos (C2, SCIF), VLAN para administración de red — con un firewall interno (o ACL en capa 3 del switch) que controle el tráfico entre VLANs.

    *Criterio mínimo:* Explicar que sin VLANs no hay barrera de red entre usuario y servidor crítico + mencionar VLANs (y/o firewall interno / ACL) como control corrector.

    **P10 — Cifrado en reposo deshabilitado, Capa 6 (Datos):**

    Respuesta completa:

    - Capa afectada: **Capa 6 — Datos** (cifrado en reposo).
    - Impacto: Si un atacante obtiene acceso físico al disco del SERVIDOR_ARCHIVOS (extracción del disco duro, arranque con un sistema operativo externo desde USB, o robo del servidor), puede leer directamente los archivos Confidenciales sin necesidad de conocer credenciales del sistema operativo. El cifrado en reposo (por ejemplo, BitLocker, VeraCrypt, o cifrado a nivel de sistema de archivos) garantiza que el disco sea ilegible sin la clave de cifrado, incluso si el disco se extrae y se monta en otro equipo.

    *Criterio mínimo:* Identificar Capa 6 (Datos) + explicar que sin cifrado en reposo el acceso físico al disco equivale a acceso directo a los datos.

    **P11 — Logs en estación de trabajo durante incidente activo (A-02):**

    Respuesta completa: Si el ransomware de la amenaza A-02 se propaga por toda la red interna (que está sin segmentar), alcanzará PC_OPS_02 como cualquier otro endpoint — cifrando o destruyendo los logs almacenados allí. Esto significa que durante la Fase de Detección y Análisis del NIST SP 800-61, el equipo de respuesta no tendrá acceso a los logs que necesita para entender el vector de entrada, el alcance de la propagación y los sistemas afectados. Los logs deben almacenarse en un sistema **separado y protegido** (SIEM, servidor de logs offline, o almacenamiento inmutable en la nube) para garantizar su disponibilidad durante el incidente.

    *Criterio mínimo:* Identificar que el ransomware puede cifrar PC_OPS_02 y destruir los logs + señalar que esto impide el análisis forense del incidente.

    ---

    ### Sección C: Respuestas Modelo para Respuesta Corta (P12-P15)

    **P12 — Omitir erradicación antes de restaurar (fases NIST SP 800-61):**

    Respuesta modelo: Se está omitiendo la **Fase de Erradicación** (parte de la Fase 3 del NIST SP 800-61). Restaurar desde backup sin identificar y eliminar el vector de entrada (ej. SMBv1 habilitado, vulnerabilidad sin parchear, acceso del atacante persistente) permite que el ransomware vuelva a cifrar los sistemas restaurados. El ciclo se repite. La erradicación incluye: identificar el vector de entrada, eliminarlo (parchear la vulnerabilidad, revocar credenciales comprometidas, deshabilitar SMBv1), y verificar que no hay puertas traseras antes de restaurar. Solo después viene la Recuperación con la restauración del sistema limpio.

    *Criterio mínimo:* Identificar que se omite la erradicación (eliminación del vector) Y explicar que restaurar sin erradicar permite que el ataque se repita.

    **P13 — Diferencia DMZ / red interna y uso correcto de la DMZ de JC-BC3:**

    Respuesta modelo:

    - Diferencia: La **DMZ** (zona desmilitarizada) es un segmento de red semipúblico ubicado entre el firewall externo e internet, diseñado para alojar servicios que necesitan ser accesibles desde el exterior (web, correo, VPN concentrator) pero que no deben tener acceso directo a la red interna. Si un servicio en DMZ es comprometido, el atacante no llega automáticamente a la red interna. La **red interna** aloja sistemas confidenciales (C2, servidores de archivos, directorio activo) que solo deben ser accesibles desde dentro de la organización.
    - En la JC-BC3: el concentrador VPN (si el personal accede remotamente), el servidor proxy inverso, o el servidor DNS externo son candidatos para la DMZ. No deben estar en la red interna porque exponen la red interna si son comprometidos.

    *Criterio mínimo:* Explicar que la DMZ separa servicios expuestos al exterior de los sistemas internos + mencionar al menos un servicio que debería estar en la DMZ de la JC-BC3 (VPN concentrator / proxy / servidor de correo externo).

    **P14 — Dos problemas con la política de contraseñas de la JC-BC3:**

    Respuesta modelo:

    - **Problema 1 (Capa 1 — Humana / políticas):** Contraseñas de 6 caracteres son vulnerables a ataques de fuerza bruta o de diccionario. Una contraseña de 6 caracteres alfanumérica puede romperse en minutos con herramientas modernas. La política debería exigir un mínimo de 12-16 caracteres con complejidad (mayúsculas, números, símbolos) o, preferiblemente, frases de contraseña.
    - **Problema 2 (Capa 5 — Aplicaciones / autenticación):** La ausencia de caducidad significa que una contraseña comprometida (ej. robada por phishing en A-01) permanece válida indefinidamente. El atacante mantiene acceso persistente sin necesidad de volver a explotar el vector. Las políticas modernas recomiendan cambio de contraseña cuando hay evidencia de compromiso + MFA como control complementario que mitiga incluso contraseñas débiles.

    *Criterio mínimo:* Un problema relacionado con longitud/complejidad (fuerza bruta) Y un problema relacionado con la no caducidad (acceso persistente con credenciales comprometidas).

    **P15 — Tres elementos del informe post-incidente (Fase 4 NIST SP 800-61):**

    Respuesta modelo (aceptar cualquier 3 de los siguientes elementos):

    1. **Cronología del incidente:** Cuándo se detectó, cuándo se contuvo, cuándo se erradicó y cuándo se recuperó — con horas específicas.
    2. **Vector de entrada identificado:** Cómo entró el atacante (ej. phishing + adjunto malicioso que explotó SMBv1).
    3. **Sistemas afectados y alcance del daño:** Qué sistemas fueron cifrados, qué datos se vieron comprometidos, cuánto tiempo estuvo la operación degradada.
    4. **Lecciones aprendidas:** Qué controles fallaron y por qué, qué habría limitado el impacto.
    5. **Acciones correctivas recomendadas:** Lista priorizada de controles a implementar (deshabilitar SMBv1, segmentar VLANs, backup offline, formación en phishing).
    6. **Métricas de respuesta:** Tiempo hasta detección (TTD), tiempo hasta contención (TTC), tiempo hasta recuperación (TTR) — para comparar con el plan de respuesta a incidentes.

    *Criterio mínimo:* Tres elementos técnicamente correctos y coherentes con el NIST SP 800-61 Fase 4. Aceptar respuestas que no usen terminología exacta si el concepto es correcto (ej. "qué salió mal" como equivalente de "lecciones aprendidas").

    ---

    ## Notas de Aplicación

    **Distribución del tiempo recomendada:**

    | Sección | Preguntas | Tiempo sugerido |
    |---------|-----------|----------------|
    | Lectura del Escenario de Análisis | — | 5 minutos |
    | Sección A (Opción Múltiple) | P1-P3 | 8 minutos |
    | Sección B (Análisis de Escenario) | P4-P11 | 32 minutos |
    | Sección C (Respuesta Corta) | P12-P15 | 12 minutos |
    | Revisión final | — | 3 minutos |
    | **Total** | **15 preguntas** | **60 minutos** |

    **Proyección del mapa de red para Sección B:**

    El mapa de red ASCII puede proyectarse en pantalla grande al inicio del examen para que todos los alumnos lo vean con claridad. Alternativamente, imprimir y distribuir una hoja adicional con el escenario antes de comenzar. El mapa es esencial para P4-P11 — sin él, esas preguntas no tienen contexto suficiente.

    **Errores comunes esperados:**

    - P1: Alumnos pueden elegir (A) confundiendo la profundidad con la potencia técnica del firewall — reforzar que DiD es una **estrategia arquitectónica**, no una mejora de un único componente.
    - P3: Alumnos pueden elegir (A) pensando que BitLocker "protege todo" — reforzar que el cifrado en reposo solo protege datos **cuando el disco está apagado**; en operación, los datos se presentan descifrados a quien tenga sesión activa.
    - P6: Alumnos pueden mencionar solo un control (el de Capa 4) porque SMBv1 es el vector obvio — recordar que la pregunta pide **uno de Capa 3 y uno de Capa 4**.
    - P12: Alumnos pueden decir "se omite la recuperación" — aclarar que la recuperación (restaurar) es exactamente lo que el Técnico propone hacer; lo que se omite es la **erradicación** previa.
    - P15: Alumnos tienden a mencionar solo acciones futuras ("implementar X") — el informe post-incidente también debe documentar lo que **ocurrió** (cronología, alcance, vector).
