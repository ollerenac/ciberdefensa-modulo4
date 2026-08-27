---
# Horas asignadas: 1 hr
# Tipo: Examen
---

# Examen Final — Redes Defensivas

> **Duración:** 60 minutos | **Preguntas:** 20 | **Valor:** 20 puntos (1 punto por pregunta)

!!! warning "Instrucciones"
    - Responder de forma individual y sin consultar apuntes, salvo indicación del instructor.
    - Marcar **una sola respuesta** por pregunta.
    - Cada pregunta presenta cuatro alternativas: A, B, C y D.
    - Si se marcan dos o más alternativas en una pregunta, la respuesta se considera incorrecta.
    - Escribir nombre completo y número de identificación antes de comenzar.

El examen evalúa conceptos y decisiones básicas de **iptables**, **proxy Squid**, **IDS/IPS** y **honeypots**.

---

## Iptables — Preguntas 1 a 5

**P1. Pregunta:** ¿Qué tráfico examina principalmente la cadena `INPUT` de iptables?

A) El tráfico generado por el propio equipo y enviado a Internet

B) El tráfico que llega al propio equipo desde la red

C) El tráfico que atraviesa el equipo cuando funciona como router

D) Únicamente el tráfico destinado al puerto 22

---

**P2. Pregunta:** Una cadena `INPUT` tiene política predeterminada `DROP`. ¿Qué ocurre con un paquete entrante que no coincide con ninguna regla anterior?

A) Se acepta y se registra automáticamente

B) Se reenvía a la cadena `OUTPUT`

C) Se descarta

D) Se guarda para que el administrador decida después

---

**P3. Pregunta:** ¿Cuál es el propósito de una regla que permite todo el tráfico por la interfaz `lo`?

A) Permitir la comunicación local entre procesos del mismo equipo

B) Permitir conexiones desde cualquier equipo de Internet

C) Traducir direcciones privadas mediante NAT

D) Registrar todo el tráfico que será bloqueado

---

**P4. Pregunta:** ¿Por qué importa el orden de las reglas en una cadena de iptables?

A) Porque las reglas se ejecutan siempre desde la última hacia la primera

B) Porque las reglas de permiso deben escribirse obligatoriamente en orden alfabético

C) Porque iptables solo puede mantener una regla por puerto

D) Porque normalmente se aplica la primera regla que coincide con el paquete

---

**P5. Pregunta:** ¿Para qué se utiliza `iptables-save` durante un laboratorio?

A) Para instalar automáticamente el servicio de persistencia

B) Para obtener una representación del ruleset actual que puede guardarse como respaldo

C) Para eliminar todas las reglas sin cambiar las políticas

D) Para comprobar qué proceso escucha en cada puerto

---

## Proxy Squid — Preguntas 6 a 10

**P6. Pregunta:** ¿Cuál es la función principal de un proxy forward como Squid?

A) Intermediar las solicitudes de los clientes hacia servicios de Internet

B) Reemplazar el antivirus instalado en cada equipo

C) Asignar direcciones IP mediante DHCP

D) Detectar físicamente equipos conectados a un switch

---

**P7. Pregunta:** ¿Cuál es el puerto predeterminado más habitual de Squid?

A) 22

B) 80

C) 3128

D) 3389

---

**P8. Pregunta:** En `squid.conf`, ¿por qué una regla `http_access deny` específica debe colocarse antes de una regla general `http_access allow`?

A) Porque Squid procesa primero las líneas más largas

B) Porque Squid utiliza la primera regla de acceso que coincide

C) Porque las reglas `allow` solo funcionan al final del archivo

D) Porque una regla `deny` cambia automáticamente el puerto del proxy

---

**P9. Pregunta:** ¿Qué indica normalmente el resultado `TCP_DENIED/403` en `access.log`?

A) El contenido se entregó desde la caché

B) El servidor remoto no tenía conexión a Internet

C) El cliente descargó el archivo correctamente

D) Squid rechazó la solicitud según su política de acceso

---

**P10. Pregunta:** Squid bloquea correctamente un dominio, pero el usuario puede visitarlo al desactivar el proxy del navegador. ¿Cuál es la causa más probable?

A) La red permite una conexión directa a Internet que evita el proxy

B) Squid convierte automáticamente las solicitudes bloqueadas en HTTPS

C) El archivo `access.log` está lleno

D) El dominio utiliza una dirección IPv4 privada

---

## IDS e IPS — Preguntas 11 a 15

**P11. Pregunta:** ¿Cuál es la diferencia principal entre un IDS y un IPS?

A) El IDS trabaja solo con Windows y el IPS solo con Linux

B) El IDS detecta y alerta; el IPS también puede bloquear tráfico automáticamente

C) El IDS analiza puertos y el IPS analiza únicamente archivos

D) El IDS siempre está fuera de la red y el IPS siempre está en un endpoint

---

**P12. Pregunta:** ¿Qué es un falso positivo en un IDS?

A) Un ataque real que el sistema no detectó

B) Una regla que fue eliminada correctamente

C) Una alerta generada por actividad legítima que fue interpretada como sospechosa

D) Una conexión maliciosa bloqueada por un IPS

---

**P13. Pregunta:** ¿Qué diferencia existe entre un NIDS y un HIDS?

A) El NIDS observa tráfico de red; el HIDS supervisa eventos de un equipo específico

B) El NIDS bloquea todo el tráfico; el HIDS solo permite tráfico web

C) El NIDS funciona sin reglas; el HIDS requiere siempre firmas

D) El NIDS protege únicamente redes inalámbricas; el HIDS protege redes cableadas

---

**P14. Pregunta:** ¿Qué diferencia hay entre detección por firma y detección por anomalía?

A) La firma analiza usuarios y la anomalía analiza únicamente puertos

B) La firma siempre bloquea; la anomalía solo registra

C) La firma se usa en un HIDS y la anomalía únicamente en un NIDS

D) La firma busca patrones conocidos; la anomalía busca desviaciones del comportamiento normal

---

**P15. Pregunta:** ¿Para qué se utiliza `threshold` en una regla de Snort que podría generar muchas alertas repetidas?

A) Para convertir automáticamente el IDS en IPS

B) Para limitar la frecuencia de alertas y evitar una tormenta de eventos

C) Para cifrar los registros producidos por Snort

D) Para permitir todo el tráfico ICMP

---

## Honeypots — Preguntas 16 a 20

**P16. Pregunta:** ¿Por qué una conexión a un honeypot suele considerarse sospechosa?

A) Porque el honeypot bloquea todo el tráfico legítimo de Internet

B) Porque el honeypot reemplaza al servidor principal durante el mantenimiento

C) Porque es un recurso señuelo que no debería recibir uso legítimo normal

D) Porque solamente acepta conexiones de atacantes previamente identificados

---

**P17. Pregunta:** ¿Cuál es una diferencia correcta entre un honeypot de baja interacción y uno de alta interacción?

A) El de baja interacción emula servicios y reduce el riesgo; el de alta interacción ofrece un entorno más real y recopila más información, pero implica mayor riesgo

B) El de baja interacción funciona solo con UDP y el de alta interacción solo con TCP

C) El de baja interacción siempre bloquea al atacante y el de alta interacción nunca registra eventos

D) El de baja interacción necesita un sistema operativo completo y el de alta interacción no necesita software

---

**P18. Pregunta:** ¿Dónde debería ubicarse un honeypot para reducir el riesgo de que un atacante lo use contra otros sistemas?

A) En el mismo segmento y con los mismos permisos que los servidores críticos

B) En el equipo personal del administrador, sin monitoreo

C) Directamente dentro de la red de mando, con acceso sin restricciones

D) En un segmento aislado y monitoreado, con conexiones salientes limitadas

---

**P19. Pregunta:** ¿Qué información básica resulta útil en el registro de un honeypot?

A) Únicamente el nombre comercial del antivirus del atacante

B) Dirección IP de origen, fecha y hora, puerto de destino y patrón de intentos

C) Solo el nombre completo de la persona que realizó la conexión

D) La contraseña del administrador del honeypot

---

**P20. Pregunta:** Un honeypot interno registra que una misma IP intentó conectarse rápidamente a los puertos 22, 23, 80 y 3389. ¿Cuál es la interpretación más adecuada?

A) Es una actualización normal del sistema operativo

B) Demuestra que todos esos servicios están vulnerados

C) Puede ser reconocimiento o escaneo de puertos y debe investigarse

D) Significa que el honeypot dejó de funcionar
