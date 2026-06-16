---
# Horas asignadas: 1 hr
# Tipo: Teoría
---

# Defensa en Profundidad — Introducción y Capa Humana (CONT-21)

> **Duración:** 1 hora | **Asignatura:** Capas SI | **Unidad:** Defensa en Profundidad

## Objetivo de la clase

Al finalizar esta clase, el alumno será capaz de:

- Explicar el concepto de defensa en profundidad y por qué una sola capa de seguridad no es suficiente.
- Identificar las 7 capas del modelo de defensa en profundidad utilizado en esta unidad.
- Describir la capa humana como la primera y más crítica línea de defensa.
- Reconocer los principales vectores de ingeniería social y cómo mitigarlos con políticas y formación.

---

## ¿Por qué una sola defensa no es suficiente?

Imagina un puesto militar con una sola verja en la entrada. Si alguien la supera — con documentación falsa, un vehículo robado o simplemente en el caos de un movimiento de tropas — el atacante tiene acceso libre a todo lo que hay dentro. No hay segunda barrera. No hay control interior. Una única defensa, una única brecha, acceso total.

La seguridad de la información funciona exactamente igual. Los primeros sistemas informatizados de las décadas de 1970 y 1980 confiaban en un único perímetro — el firewall — para proteger todo lo que había dentro. Si el firewall fallaba, no había nada más. Esta arquitectura se denominó **modelo de castillo con foso**: fuera todo es enemigo, dentro todo es de confianza.

!!! note "Definición"
    La **defensa en profundidad** (*Defense in Depth*, DiD) es una estrategia de seguridad que aplica múltiples capas de controles independientes de forma que, si una capa falla, las capas siguientes detienen o limitan el avance del atacante.

El principio viene directamente de la doctrina militar: las posiciones defensivas en profundidad obligan al atacante a superar una serie de líneas consecutivas, cada una con su propio sistema de detección y respuesta. El atacante no puede simplemente cruzar una brecha y terminar — cada capa compra tiempo, activa alertas y consume recursos del adversario.

---

## El Modelo de 7 Capas

Esta unidad organiza la defensa en profundidad en 7 capas. Cada par de clases (CONT-21 a CONT-28) cubre una capa. El diagrama siguiente es el mapa de toda la unidad — vuelve a él cuando te pierdas en los detalles.

```
Defensa en Profundidad — Capas de Seguridad

┌─────────────────────────────────────────┐
│  Capa 1: HUMANA                        │  ← CONT-21 (esta clase)
│  Personas, políticas, concienciación   │
├─────────────────────────────────────────┤
│  Capa 2: PERIMETRAL                    │  ← CONT-22
│  Firewall perimetral, DMZ, proxy       │
├─────────────────────────────────────────┤
│  Capa 3: RED INTERNA                   │  ← CONT-23
│  Segmentación, VLANs, VPN, TLS        │
├─────────────────────────────────────────┤
│  Capa 4: ENDPOINT                      │  ← CONT-24
│  EDR, parches, cifrado disco, USB      │
├─────────────────────────────────────────┤
│  Capa 5: APLICACIONES                  │  ← CONT-25
│  Auth, validación entrada, OWASP       │
├─────────────────────────────────────────┤
│  Capa 6: DATOS                         │  ← CONT-26
│  Clasificación, cifrado en reposo, DLP │
├─────────────────────────────────────────┤
│  Capa 7: ACTIVOS CRÍTICOS              │  ← CONT-27/28
│  PAM, aislamiento red, monitoreo       │
└─────────────────────────────────────────┘
```

Nota importante: las capas no son muros secuenciales que el atacante debe romper en orden. Son controles **independientes** que operan en paralelo. Un atacante que consigue entrar directamente a la Capa 5 a través de una vulnerabilidad web todavía tiene que superar la Capa 6 (cifrado de datos) y la Capa 7 (acceso a activos críticos). Cada capa hace su trabajo independientemente de las demás.

---

## Capa 1: La Capa Humana

La Capa 1 es la más difícil de controlar y, a la vez, la más explotada por atacantes reales. Los controles técnicos de las Capas 2 a 7 son inútiles si la persona detrás del teclado entrega sus credenciales voluntariamente, instala malware creyendo que es una actualización legítima, o deja su portátil desbloqueado en una cafetería.

### El Factor Humano como Vector de Ataque

La ingeniería social es la técnica de manipular a personas para obtener información o acceso que de otro modo estaría protegido. No requiere conocimiento técnico avanzado — requiere entender cómo funciona la psicología humana.

!!! warning "El ataque más barato es el más efectivo"
    Según el Informe de Investigaciones de Brechas de Datos de Verizon (DBIR), más del 80% de las brechas de datos exitosas involucran algún componente humano — phishing, credenciales robadas, o error humano. Los atacantes prefieren engañar a una persona antes de romper un firewall.

| Técnica | Descripción | Señal de alerta |
|---------|-------------|-----------------|
| **Phishing** | Correo electrónico falso que imita una fuente legítima para robar credenciales | URL que no coincide con el dominio real; urgencia artificial |
| **Vishing** | Phishing por llamada telefónica — el atacante finge ser soporte técnico o autoridad | Solicitud de contraseñas por teléfono; presión de tiempo |
| **Pretexting** | Crear un escenario falso para obtener información — "soy el nuevo técnico de IT" | Solicitudes de acceso sin proceso formal de autorización |
| **Baiting** | Dejar una memoria USB infectada en un lugar visible esperando que alguien la conecte | Dispositivos USB sin propietario identificado |
| **Tailgating** | Seguir a un empleado autorizado a través de una puerta de acceso controlado sin credenciales propias | Personas que siguen sin pasar su tarjeta |

!!! example "Aplicación en entorno castrense"
    Un atacante envía un correo electrónico a técnicos del puesto de mando con asunto "Actualización de firmware obligatoria — Sistema de Radio Táctica HF". El correo incluye un enlace a un instalador que parece legítimo pero contiene un troyano de acceso remoto (RAT). Dos técnicos instalan el "firmware" antes de que el oficial de seguridad reciba el alerta. El RAT establece comunicación cifrada con el servidor del atacante y comienza a exfiltrar registros de frecuencias y configuraciones de red.
    
    El firewall perimetral nunca bloqueó nada — el tráfico salió cifrado por el puerto 443, indistinguible del tráfico HTTPS normal. La Capa 2 falló porque la Capa 1 fue comprometida primero.

### Controles de la Capa Humana

Los controles de la capa humana son principalmente organizativos y de proceso, no técnicos.

**Formación y concienciación en seguridad:**
El control más efectivo es la educación repetida y práctica. No un curso anual de 4 horas — sino recordatorios regulares, simulaciones de phishing y procedimientos claros integrados en la rutina diaria. Los usuarios que saben reconocer un ataque de ingeniería social son más eficaces que cualquier filtro de correo.

**Política de uso aceptable (AUP — Acceptable Use Policy):**
Un documento que define qué pueden y qué no pueden hacer los usuarios con los sistemas de información de la organización. Una AUP efectiva no es un listado de 200 prohibiciones en jerga legal — es un documento conciso que responde tres preguntas:

1. ¿Qué sistemas puedo usar y para qué?
2. ¿Qué no está permitido bajo ninguna circunstancia?
3. ¿Qué hago si sospecho un incidente de seguridad?

**Procedimiento de reporte de incidentes:**
El personal debe saber a quién reportar si sospecha un phishing, encuentra un USB desconocido o nota comportamiento anómalo en su equipo. El obstáculo principal no es la falta de voluntad — es no saber qué hacer o temer represalias por "reportar nada importante". El procedimiento debe ser simple: un número de teléfono o correo específico, sin burocracia.

---

## Resumen

1. La defensa en profundidad aplica múltiples capas de controles independientes; si una falla, las siguientes mantienen la protección.
2. El modelo de esta unidad tiene 7 capas: Humana, Perimetral, Red Interna, Endpoint, Aplicaciones, Datos y Activos Críticos.
3. La Capa 1 (Humana) es la más explotada — la ingeniería social compromete sistemas sin tocar ningún control técnico.
4. Los controles de la capa humana son organizativos: formación, política de uso aceptable y procedimientos de reporte.

## Para profundizar

> Recursos opcionales — no requeridos para el examen.

- **NIST SP 800-50 Building an Information Technology Security Awareness and Training Program:** https://csrc.nist.gov/publications/detail/sp/800-50/final
- **Verizon DBIR (Data Breach Investigations Report):** https://www.verizon.com/business/resources/reports/dbir/

---

*Siguiente: [Capa Humana y Perimetral (Parte 2) — Firewall, DMZ y Segmentación](capa-humana-perimetral-p2.md)*
