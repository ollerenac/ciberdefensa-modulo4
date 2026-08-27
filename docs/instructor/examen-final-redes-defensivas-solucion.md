---
search:
  exclude: true
meta:
  - name: robots
    content: noindex, nofollow
---

# Guía del Instructor — Examen Final de Redes Defensivas

!!! danger "Solo para instructores"
    Este documento contiene la clave de respuestas del examen final sobre iptables, proxy Squid, IDS/IPS y honeypots. No mostrar ni compartir durante la evaluación.

[Abrir el examen para alumnos](../seguridad-criptografia/en-la-red/examen-final.md)

## Estructura y calificación

- **Duración:** 60 minutos.
- **Total:** 20 preguntas de respuesta única.
- **Puntaje:** 1 punto por cada respuesta correcta; máximo 20 puntos.
- **Distribución:** cinco preguntas por tema.
- Si el alumno marca más de una alternativa, la pregunta recibe 0 puntos.

## Clave de respuestas

### Iptables — Preguntas 1 a 5

| Pregunta | Respuesta | Justificación |
|---|:---:|---|
| P1 | **B** | `INPUT` procesa paquetes cuyo destino es el propio equipo. |
| P2 | **C** | La política `DROP` descarta el paquete cuando ninguna regla anterior coincide. |
| P3 | **A** | La interfaz `lo` permite comunicaciones internas entre procesos del mismo sistema. |
| P4 | **D** | Iptables evalúa las reglas en orden y normalmente aplica la primera coincidencia. |
| P5 | **B** | `iptables-save` muestra el ruleset en un formato que puede guardarse y restaurarse; no configura por sí solo la persistencia al reiniciar. |

### Proxy Squid — Preguntas 6 a 10

| Pregunta | Respuesta | Justificación |
|---|:---:|---|
| P6 | **A** | Un proxy forward recibe solicitudes de clientes y las envía hacia servicios externos según la política configurada. |
| P7 | **C** | Squid utiliza habitualmente el puerto TCP 3128. |
| P8 | **B** | `http_access` se evalúa en orden; una regla general de permiso colocada primero puede volver inalcanzable una denegación posterior. |
| P9 | **D** | `TCP_DENIED/403` indica que Squid negó la solicitud por una regla de acceso. |
| P10 | **A** | Si la red permite salida directa, el usuario puede evitar el proxy; se requieren controles complementarios para forzar su uso. |

### IDS e IPS — Preguntas 11 a 15

| Pregunta | Respuesta | Justificación |
|---|:---:|---|
| P11 | **B** | Un IDS observa y alerta; un IPS está en capacidad de intervenir y bloquear tráfico. |
| P12 | **C** | Un falso positivo ocurre cuando actividad legítima genera una alerta equivocada. |
| P13 | **A** | Un NIDS vigila tráfico de red; un HIDS observa actividad y eventos en un host concreto. |
| P14 | **D** | Las firmas reconocen patrones conocidos; la detección por anomalía identifica desviaciones de una línea base. |
| P15 | **B** | `threshold` reduce alertas repetidas para evitar que el ruido oculte eventos importantes. |

### Honeypots — Preguntas 16 a 20

| Pregunta | Respuesta | Justificación |
|---|:---:|---|
| P16 | **C** | Un honeypot es un señuelo sin uso operacional legítimo; por ello cualquier acceso merece revisión. |
| P17 | **A** | La baja interacción emula servicios con menor exposición; la alta interacción brinda más realismo e inteligencia a cambio de mayor riesgo. |
| P18 | **D** | El aislamiento, el monitoreo y la limitación de salidas reducen la posibilidad de que el honeypot se convierta en un punto de ataque. |
| P19 | **B** | Origen, tiempo, puerto y secuencia de intentos permiten reconocer escaneos y correlacionar eventos. |
| P20 | **C** | Los intentos rápidos contra varios puertos son compatibles con reconocimiento automatizado o escaneo y requieren investigación. |

## Hoja rápida de corrección

| P1 | P2 | P3 | P4 | P5 | P6 | P7 | P8 | P9 | P10 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| B | C | A | D | B | A | C | B | D | A |

| P11 | P12 | P13 | P14 | P15 | P16 | P17 | P18 | P19 | P20 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| B | C | A | D | B | C | A | D | B | C |

## Observaciones para la retroalimentación

- **P4 y P8:** ambas comprueban el orden de evaluación. Relacionar la primera coincidencia de iptables con el orden de `http_access` ayuda a consolidar el concepto.
- **P5:** distinguir entre capturar un ruleset y hacerlo persistente. La persistencia requiere un mecanismo adicional que lo restaure al iniciar.
- **P10:** Squid solo controla el tráfico que pasa por el proxy. El diseño de red debe impedir o controlar rutas directas cuando su uso sea obligatorio.
- **P11:** explicar que desplegar un IPS sin ajustar sus reglas puede interrumpir tráfico legítimo debido a falsos positivos.
- **P20:** un registro de escaneo no demuestra por sí solo que exista una intrusión exitosa; es una señal que debe correlacionarse e investigarse.
