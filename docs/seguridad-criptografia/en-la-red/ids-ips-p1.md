---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# Sistemas de Detección y Prevención de Intrusiones — IDS e IPS (Parte 1)

> **Duración:** 1 hora | **Asignatura:** Seguridad de la Información y Criptografía | **Unidad:** En la Red

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Definir IDS e IPS y distinguirlos funcionalmente en términos de acción y posición en la red
- Diferenciar un NIDS (basado en red) de un HIDS (basado en host) y explicar sus puntos ciegos respectivos
- Explicar la detección por firma y la detección por anomalía, con sus ventajas y limitaciones
- Posicionar correctamente IDS/IPS en la arquitectura de red defensiva de una unidad

---

## IDS vs. IPS: La Diferencia Clave

Un **IDS (Intrusion Detection System)** detecta tráfico sospechoso y genera una alerta. Un **IPS (Intrusion Prevention System)** detecta tráfico sospechoso y lo bloquea activamente. Ambos inspeccionan el tráfico de red buscando patrones maliciosos, pero su respuesta ante una detección es radicalmente diferente.

| Característica | IDS | IPS | Implicación práctica |
|---------------|-----|-----|---------------------|
| Acción ante detección | Alertar (pasivo) | Bloquear (activo) | El IPS puede detener el ataque; el IDS solo informa |
| Posición en la red | Fuera del flujo (out-of-band) | En línea (inline) | El IPS debe procesar cada paquete antes de que pase |
| Impacto si falla | Solo pierde alertas | Puede interrumpir tráfico legítimo | Un fallo del IPS puede cortar la conectividad |
| Latencia añadida | Ninguna (no está en el flujo) | Mínima (procesamiento por paquete) | El IPS tiene impacto en el rendimiento de la red |

!!! warning "El IPS mal configurado puede ser tan peligroso como el ataque"
    Un IPS con reglas demasiado agresivas puede bloquear tráfico legítimo y causar interrupciones operacionales. En entornos militares, esto puede ser tan perjudicial como el ataque mismo: un IPS que bloquea las comunicaciones de comando durante una operación es un problema grave. Por esta razón, muchas organizaciones despliegan primero en modo IDS (observar sin bloquear) durante semanas o meses para entender el tráfico normal, y solo pasan a modo IPS cuando tienen confianza en las reglas.

---

## NIDS vs. HIDS

La distinción entre NIDS y HIDS define **dónde** se monitorea:

**NIDS (Network IDS):** Monitorea el tráfico de la red completa desde un punto de captura. Un sensor conectado al switch captura una copia de todo el tráfico que circula por el segmento de red. Herramienta ejemplo: Snort.

**HIDS (Host IDS):** Monitorea un único equipo — sus archivos de sistema, procesos en ejecución, logs del sistema operativo y llamadas al sistema. Detecta cambios en archivos críticos o comportamiento anómalo en ese host específico.

| Tipo | Qué monitorea | Herramienta ejemplo | Punto ciego |
|------|--------------|--------------------|----|
| **NIDS** | Tráfico de toda la red | Snort, Suricata | Tráfico cifrado entre dos hosts (no puede ver el contenido) |
| **HIDS** | Un solo equipo (archivos, procesos, logs) | OSSEC, componentes de Windows Defender | Tráfico de red de otros equipos de la red |

!!! note "NIDS y HIDS son complementarios"
    NIDS y HIDS no son alternativas entre sí: son herramientas complementarias. Una arquitectura de seguridad madura usa ambos: el NIDS detecta amenazas que circulan por la red (malware que se propaga lateralmente, escaneos de puertos, comunicaciones con C2), mientras que el HIDS detecta compromisos en hosts individuales (modificación de archivos del sistema, escalada de privilegios, persistencia).

---

## Detección por Firma vs. Detección por Anomalía

Existen dos métodos fundamentales de detección en sistemas IDS/IPS:

**Detección por firma:** El sistema compara cada paquete contra una base de datos de patrones conocidos de ataques (firmas). Si el tráfico coincide con una firma conocida, se genera una alerta. Es como un antivirus de red: muy preciso para ataques conocidos, completamente ciego ante ataques nuevos (zero-day).

**Detección por anomalía:** El sistema aprende el comportamiento "normal" de la red (línea base) y genera alertas cuando el tráfico se desvía significativamente de esa línea base. Puede detectar ataques nuevos desconocidos; el coste es un mayor número de falsos positivos.

| Método | Ventaja | Desventaja | Herramienta ejemplo |
|--------|---------|-----------|---------------------|
| **Firma** | Alta precisión en ataques conocidos; pocos falsos positivos | Ciego ante zero-days; requiere actualización constante de firmas | Snort (reglas de comunidad) |
| **Anomalía** | Puede detectar ataques nuevos desconocidos | Muchos falsos positivos; requiere período de aprendizaje | Zeek (análisis de comportamiento) |

**Conceptos de evaluación de detección — el alumno debe conocerlos:**

| Término | Significado | Ejemplo |
|---------|------------|---------|
| **True Positive (TP)** | Alerta generada por tráfico realmente malicioso | Snort detecta un exploit real y alerta |
| **False Positive (FP)** | Alerta generada por tráfico legítimo | Snort alerta en tráfico normal de una aplicación empresarial |
| **True Negative (TN)** | No se genera alerta por tráfico legítimo (correcto) | El tráfico normal pasa sin alertas |
| **False Negative (FN)** | No se genera alerta por tráfico malicioso real (fallo) | Un ataque zero-day pasa sin detección |

Un buen sistema IDS minimiza los FP (para no saturar al analista) y minimiza los FN (para no perder ataques reales). En la práctica, ambos objetivos están en tensión: reducir FP a veces aumenta FN y viceversa.

---

## Posicionamiento en la Arquitectura de Red

```
Internet
    │
    ▼
[Firewall perimetral]
    │
    ▼
[Switch con mirror port]──────► Sensor NIDS (Snort)
    │                                    │
    │                              [Alertas IDS]
    ▼
[Red interna]
    │
    ├─► PC1 con HIDS
    ├─► PC2 con HIDS
    └─► Servidor con HIDS
```

**Mirror port (SPAN port):** El switch envía una copia de todo el tráfico que circula por un puerto o VLAN a otro puerto designado, donde está conectado el sensor IDS. El tráfico original no se ve afectado en absoluto — el IDS recibe solo una copia. Esto es lo que permite al IDS operar en modo pasivo (out-of-band) sin añadir latencia ni riesgo de interrumpir el tráfico.

---

## Contexto militar

!!! example "Aplicación en entorno castrense"
    En la red de una unidad, el sensor NIDS (Snort) detecta un patrón de exploración de puertos sistemática (port scan) originado desde la IP interna 192.168.1.47, que corresponde al ordenador de un operador. El scan cubre los puertos 22, 23, 80, 443, 3389 de todas las IPs de la subred en menos de 30 segundos — comportamiento imposible para un usuario legítimo. El IDS genera una alerta inmediata. El analista de seguridad investiga el equipo 192.168.1.47 y descubre que tiene un troyano de reconocimiento lateral activo. Sin el IDS, el ataque hubiera pasado invisible y el adversario hubiera completado la fase de reconocimiento antes de ser detectado.

---

## Resumen

- Un IDS detecta y alerta (pasivo, out-of-band); un IPS detecta y bloquea (activo, inline) — la diferencia operacional clave es que un IPS mal configurado puede interrumpir el tráfico legítimo
- Un NIDS monitorea el tráfico de toda la red desde un punto de captura (mirror port); un HIDS monitorea un único host — son complementarios, no alternativos
- La detección por firma es precisa pero ciega ante zero-days; la detección por anomalía puede detectar lo desconocido pero genera más falsos positivos
- Los conceptos de true positive, false positive, true negative y false negative son la base para evaluar la efectividad de cualquier sistema de detección

---

*Siguiente: [IDS e IPS (Parte 2) — Laboratorio con Snort](ids-ips-p2.md)*
