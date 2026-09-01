---
# Tipo: Actividad de repaso
---

# Precalentamiento y Repaso — Redes Defensivas

> **Duración sugerida:** 30–40 minutos | **Preguntas:** 20 | **Modalidad:** individual o por equipos

!!! info "Instrucciones"
    - Seleccionar **una sola respuesta** por pregunta.
    - Cada pregunta presenta cuatro alternativas: A, B, C y D.
    - Después de responder individualmente, comparar las decisiones con el equipo y justificar cualquier cambio.
    - Los temas evaluados son iptables, proxy Squid, IDS/IPS y honeypots.

---

## Iptables — Preguntas 1 a 5

**P1. Pregunta:** ¿Qué tráfico procesa principalmente la cadena `OUTPUT` de iptables?

A) Tráfico generado por el propio equipo y enviado hacia otro destino

B) Tráfico que llega al propio equipo desde la red

C) Tráfico que atraviesa el equipo cuando funciona como router

D) Únicamente respuestas de servidores DNS

---

**P2. Pregunta:** ¿Qué información permite observar el comando `sudo iptables -L -n -v --line-numbers`?

A) Las contraseñas de los usuarios conectados

B) Las reglas, sus números de línea y sus contadores de paquetes y bytes

C) Solo las direcciones IP asignadas por DHCP

D) Los archivos que fueron descargados desde Internet

---

**P3. Pregunta:** ¿Para qué sirve una regla que acepta estados `ESTABLISHED,RELATED`?

A) Para permitir respuestas pertenecientes a conexiones ya iniciadas o relacionadas

B) Para aceptar cualquier conexión nueva desde Internet

C) Para convertir automáticamente al equipo en router

D) Para bloquear el tráfico generado por la interfaz de loopback

---

**P4. Pregunta:** Antes de eliminar todas las reglas durante un reset seguro, ¿qué acción reduce el riesgo de perder la conexión con el equipo?

A) Cambiar primero las políticas integradas a `ACCEPT`

B) Desactivar la interfaz de red

C) Eliminar únicamente la cadena `OUTPUT`

D) Reiniciar el equipo sin guardar el ruleset

---

**P5. Pregunta:** Una regla usa el objetivo `LOG` y la siguiente regla descarta el paquete. ¿Qué hace la regla `LOG`?

A) Permite el paquete después de registrarlo

B) Registra información del paquete, pero no lo bloquea por sí sola

C) Guarda una copia completa del archivo transferido

D) Cambia la dirección IP de origen del paquete

---

## Proxy Squid — Preguntas 6 a 10

**P6. Pregunta:** ¿Qué tipo de ACL de Squid se utiliza normalmente para identificar la red de origen de los clientes?

A) `src`

B) `dstdomain`

C) `url_regex`

D) `port_name`

---

**P7. Pregunta:** ¿Qué debería ejecutar el administrador antes de reiniciar o recargar Squid después de editar `squid.conf`?

A) `iptables-save`

B) `squid -k parse`

C) `ping squid.conf`

D) `netstat -D proxy`

---

**P8. Pregunta:** ¿Qué indica normalmente `TCP_HIT` en el archivo `access.log` de Squid?

A) La solicitud fue bloqueada por una ACL

B) El cliente intentó evitar el proxy

C) El contenido se entregó desde la caché de Squid

D) El servicio Squid dejó de funcionar

---

**P9. Pregunta:** Una política permite únicamente una lista pequeña de dominios autorizados y bloquea todos los demás. ¿Qué enfoque aplica?

A) Lista blanca o *allowlist*

B) Detección por anomalía

C) Traducción de direcciones mediante NAT

D) Honeypot de alta interacción

---

**P10. Pregunta:** Cuando un navegador usa el método `CONNECT` para acceder a un sitio HTTPS mediante Squid sin inspección TLS, ¿qué puede controlar el proxy con mayor facilidad?

A) Cada palabra escrita dentro de la página cifrada

B) El destino de la conexión, pero no todo el contenido cifrado

C) La contraseña del usuario dentro del sitio

D) Los archivos guardados localmente en el equipo cliente

---

## IDS e IPS — Preguntas 11 a 15

**P11. Pregunta:** ¿Qué ubicación permite a un NIDS observar una copia del tráfico de varios equipos sin bloquearlo directamente?

A) Un puerto espejo del switch o un punto de monitoreo equivalente

B) La carpeta de descargas de cada usuario

C) El teclado del administrador

D) Una impresora desconectada de la red

---

**P12. Pregunta:** ¿Qué es un falso negativo en un sistema de detección?

A) Una alerta generada por tráfico legítimo

B) Una amenaza real que el sistema no detectó

C) Una regla que produjo demasiadas alertas repetidas

D) Un paquete que el IPS bloqueó correctamente

---

**P13. Pregunta:** ¿Qué detecta la siguiente regla simplificada de Snort?

```text
alert tcp any any -> $HOME_NET 23 (msg:"Acceso Telnet"; sid:1000001; rev:1;)
```

A) Tráfico UDP que sale de la red interna por el puerto 23

B) Tráfico TCP dirigido a la red interna por el puerto 23

C) Cualquier conexión HTTPS originada en la red interna

D) Archivos llamados `Telnet` almacenados en un endpoint

---

**P14. Pregunta:** ¿Para qué sirve el campo `sid` dentro de una regla local de Snort?

A) Para identificar de manera única la regla

B) Para indicar la contraseña del sensor

C) Para seleccionar la interfaz de red

D) Para definir la dirección IP del atacante

---

**P15. Pregunta:** Antes de habilitar bloqueo automático en un IPS nuevo, ¿qué práctica ayuda a evitar interrupciones por alertas incorrectas?

A) Observar primero el tráfico normal y ajustar las reglas

B) Eliminar todos los registros del sensor

C) Bloquear inmediatamente todos los puertos

D) Desactivar las actualizaciones de firmas

---

## Honeypots — Preguntas 16 a 20

**P16. Pregunta:** ¿Cuál es un objetivo principal de un honeypot?

A) Sustituir todos los controles de seguridad de la red

B) Atraer y registrar actividad sospechosa para estudiarla

C) Almacenar la información más crítica de la organización

D) Acelerar las conexiones legítimas de los usuarios

---

**P17. Pregunta:** ¿Qué tipo de información debería evitarse en un honeypot?

A) Registros de fecha y hora de las conexiones

B) Direcciones IP observadas durante los intentos

C) Datos reales sensibles o credenciales válidas de producción

D) Puertos señuelo destinados al monitoreo

---

**P18. Pregunta:** ¿Por qué se limitan las conexiones salientes de un honeypot?

A) Para impedir que un atacante que lo comprometa lo use contra otros sistemas

B) Para evitar que el honeypot registre direcciones IP

C) Para convertirlo automáticamente en un HIDS

D) Para permitirle acceder sin restricciones a servidores críticos

---

**P19. Pregunta:** Un honeypot no registra conexiones durante varios días. ¿Cuál es la conclusión más prudente?

A) El honeypot necesariamente está averiado

B) No se observó actividad dirigida al señuelo durante ese periodo, pero debe verificarse que el monitoreo siga activo

C) La red está garantizada contra cualquier ataque futuro

D) Se deben publicar sus credenciales para atraer tráfico

---

**P20. Pregunta:** Un honeypot SSH registra cientos de intentos de contraseña desde una misma IP en pocos minutos. ¿Qué comportamiento representa con mayor probabilidad?

A) Una copia de seguridad normal

B) Un intento de fuerza bruta o adivinación de credenciales

C) Una actualización de firmas del antivirus

D) Una consulta legítima al servidor DNS
