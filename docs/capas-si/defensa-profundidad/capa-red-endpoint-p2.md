---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# Defensa en Profundidad — Capa Endpoint (CONT-24)

> Esta clase cubre **Capa 4: Endpoint** del modelo de defensa en profundidad establecido en [Parte 1](capa-humana-perimetral-p1.md).

> **Duración:** 1 hora | **Asignatura:** Capas SI | **Unidad:** Defensa en Profundidad

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Distinguir entre un antivirus tradicional y un EDR, y explicar por qué el EDR es más efectivo frente a amenazas modernas.
- Explicar la gestión de parches como control de seguridad crítico y su relación con el vector de ataque de ransomware.
- Describir los riesgos de los dispositivos USB en entornos militares y los controles disponibles para mitigarlos.

---

> **Recapitulación:** En la Parte 1 (CONT-23) cubrimos la Capa 3 Red Interna: TLS para cifrar el tráfico interno, VPN para conectar ubicaciones remotas de forma segura, y NAC para controlar qué dispositivos se conectan a la red. Esta clase cubre la Capa 4: los controles de seguridad en el propio endpoint (el dispositivo del usuario o servidor).

---

## EDR: Más Allá del Antivirus Tradicional

Los antivirus tradicionales funcionan comparando archivos contra una base de datos de firmas de malware conocido. Si el malware es nuevo, si está ofuscado o si su firma no está en la base de datos, el antivirus no lo detecta. Esta limitación es crítica frente a amenazas modernas.

!!! note "Definición"
    **EDR** (Endpoint Detection and Response) es una categoría de software de seguridad para endpoints que, además de detectar amenazas conocidas, monitoriza el comportamiento de procesos en tiempo real para identificar actividad maliciosa aunque el malware no esté en ninguna base de datos de firmas.

| Capacidad | Antivirus Tradicional | EDR |
|-----------|----------------------|-----|
| **Detección** | Firmas de malware conocido | Firmas + análisis de comportamiento + heurística |
| **Amenazas nuevas** | No detecta (zero-day) | Puede detectar por comportamiento anómalo |
| **Respuesta** | Cuarentena del archivo | Cuarentena + aislamiento del equipo + forense automático |
| **Visibilidad** | Qué archivo fue detectado | Toda la cadena de eventos (qué proceso, qué hizo, qué contactó) |
| **Investigación** | Mínima | Historial de procesos, conexiones, archivos — para análisis forense |

El EDR registra continuamente lo que hace cada proceso en el sistema: qué archivos abre, qué conexiones de red establece, qué claves de registro modifica. Cuando detecta un patrón anómalo — por ejemplo, un proceso de Word que intenta ejecutar PowerShell que intenta conectarse a un servidor externo — lo bloquea y alerta, aunque ese malware específico no esté en ninguna base de datos.

!!! example "Aplicación en entorno castrense"
    Un terminal de puesto de mando recibe un archivo Excel con una macro que descarga y ejecuta un payload sin archivo (fileless malware) — malware que vive solo en memoria, sin crear archivos en disco que un antivirus pueda escanear. El antivirus tradicional no detecta nada. El EDR registra que Excel lanzó PowerShell, que PowerShell ejecutó código descargado de un dominio externo, que ese código intentó establecer una conexión de comando y control — y bloquea toda la cadena de actividad maliciosa antes de que el atacante consolide su acceso.

---

## Gestión de Parches: El Control Más Ignorado

Un parche es una actualización de software que corrige una vulnerabilidad de seguridad. La gestión de parches es el proceso de identificar, probar y aplicar esos parches de forma sistemática. Es el control más efectivo contra la mayoría de ataques reales y el más sistemáticamente ignorado.

!!! warning "WannaCry y el costo de no parchear"
    En mayo de 2017, el ransomware WannaCry infectó más de 200.000 sistemas en 150 países en menos de 24 horas. El vector de propagación fue una vulnerabilidad en el protocolo SMBv1 de Windows — **catalogada como crítica y parcheada por Microsoft en marzo de 2017, dos meses antes del ataque**. Los sistemas que aplicaron ese parche (MS17-010) no fueron infectados. Los que no lo aplicaron — hospitales, infraestructura crítica, sistemas militares en varios países — sufrieron el ataque. WannaCry no fue un ataque sofisticado: fue la explotación masiva de un parche disponible que la mayoría no aplicó.

El ciclo de vida de la gestión de parches:

1. **Identificación:** El proveedor publica una vulnerabilidad con parche disponible (CVE — Common Vulnerabilities and Exposures).
2. **Evaluación:** El equipo de seguridad evalúa la criticidad (CVSS score) y el impacto en los sistemas propios.
3. **Prueba:** El parche se prueba en un entorno de test antes de aplicar en producción (para verificar que no rompe aplicaciones).
4. **Despliegue:** El parche se aplica en producción, típicamente con una ventana de mantenimiento programada.
5. **Verificación:** Se confirma que el parche se aplicó correctamente en todos los sistemas objetivo.

!!! tip "Priorización por criticidad"
    No todos los parches son iguales. Los parches con CVSS score ≥ 9.0 (críticos) en sistemas expuestos a red deben aplicarse en horas, no en semanas. Los parches de funcionalidad de baja criticidad pueden esperar al siguiente ciclo de mantenimiento mensual.

---

## Control de Dispositivos Externos: La Amenaza del USB

Los dispositivos de almacenamiento USB representan un vector de ataque específico en entornos militares: permiten introducir malware desde fuera de la red (incluso en redes air-gapped), y exfiltrar información sin dejar rastro en la red.

!!! warning "Stuxnet y el vector USB"
    Stuxnet (2010) fue el primer malware conocido dirigido a infraestructura industrial crítica. Comprometió los sistemas de centrifugadoras de enriquecimiento de uranio iraníes a través de memorias USB conectadas por personal legítimo — los sistemas atacados no tenían conexión a Internet. La red de aire (air gap) no fue suficiente porque el vector de entrada fue físico.

**Controles para dispositivos USB:**

| Control | Descripción | Efectividad |
|---------|-------------|-------------|
| **Política de prohibición** | Los USB personales están prohibidos; solo se usan dispositivos corporativos registrados | Alta (si se cumple); baja (si no hay enforcement técnico) |
| **Bloqueo por software** | Configuración de directiva de grupo (GPO) que deshabilita o controla los puertos USB | Alta; técnicamente aplicable incluso si el usuario quiere usar USB |
| **USB corporativo con cifrado** | Solo los USB con cifrado hardware aprobados pueden conectarse | Alta; el USB cifrado es inútil en otro equipo |
| **Escaneo obligatorio** | Todo USB externo pasa por un equipo de cuarentena antes de entrar a la red | Media; depende del cumplimiento del procedimiento |
| **Registro de actividad** | El EDR registra todos los dispositivos USB conectados y los archivos copiados | Detectivo, no preventivo; útil para forense post-incidente |

!!! example "Aplicación en entorno castrense"
    El batallón aplica la siguiente política: los puertos USB de todos los equipos están deshabilitados por GPO excepto en dos "estaciones de cuarentena" en la sala de informática. Cualquier medio externo (USB, disco) que entra a la instalación debe pasar por una estación de cuarentena donde es escaneado con el antivirus actualizado, registrado con número de serie y nombre del propietario, y copiado solo si pasa el escaneo. Esta política fue implementada después de que un técnico conectara inadvertidamente un USB encontrado en el aparcamiento — el dispositivo contenía malware diseñado para robar credenciales del sistema C2.

---

## Resumen

1. El EDR supera al antivirus tradicional porque detecta amenazas por comportamiento, no solo por firmas — crítico frente a malware nuevo y fileless malware.
2. La gestión de parches es el control más efectivo contra la mayoría de ataques reales: WannaCry (2017) explotó una vulnerabilidad con parche disponible dos meses antes.
3. Los dispositivos USB son un vector de riesgo crítico en entornos militares — la solución es una combinación de política, bloqueo técnico y procedimiento de cuarentena.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **CISA Known Exploited Vulnerabilities Catalog:** https://www.cisa.gov/known-exploited-vulnerabilities-catalog
- **NIST SP 800-40 Rev. 4 — Guide to Enterprise Patch Management Planning:** https://csrc.nist.gov/publications/detail/sp/800-40/rev-4/final

---

*Siguiente: [Capa de Aplicaciones y Datos (Parte 1) — Autenticación, Autorización y OWASP](capa-aplicaciones-datos-p1.md)*
