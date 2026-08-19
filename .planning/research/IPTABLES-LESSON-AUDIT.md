# Auditoría técnica: laboratorios de iptables

Fecha: 2026-08-19

## Alcance

- `docs/seguridad-criptografia/en-el-ordenador/lab-iptables-p1.md`
- `docs/seguridad-criptografia/en-el-ordenador/lab-iptables-p2.md`
- `docs/instructor/lab-iptables-p1-solucion.md`
- `docs/instructor/lab-iptables-p2-solucion.md`

## Conclusión de plataforma

La práctica está diseñada para realizarse **desde un equipo Windows**, pero los
comandos `iptables` se ejecutan en **Ubuntu sobre WSL2**. Por tanto, configuran
Netfilter en el kernel Linux de WSL2; no configuran Windows Defender Firewall.

La distinción importa porque la conectividad WSL cambia entre el modo NAT y el
modo mirrored, y las versiones actuales de WSL también pueden aplicar el firewall
Hyper-V. Una prueba inbound puede fallar fuera de `iptables` si no se fija la
topología y el modo de red del laboratorio.

## Hallazgos prioritarios

### Alta — la prueba DROP de la Parte 1 es inválida por el orden

En `lab-iptables-p1.md:151` se permite primero `ESTABLISHED,RELATED`. Después, en
`:215`, se agrega con `-A` el DROP para paquetes con origen `8.8.8.8`. La respuesta
al ping forma parte de una conexión rastreada y puede coincidir antes con
`ESTABLISHED`; por ello el DROP posterior no demuestra lo que afirma la lección.

La solución del instructor reconoce el conflicto en `lab-iptables-p1-solucion.md:210`,
pero esperar a que expire la entrada no corrige el orden permanente. La prueba debe
insertar temporalmente el DROP antes de `ESTABLISHED`, comprobar su contador y
eliminarlo al terminar; alternativamente debe usar un cliente externo controlado.

### Alta — no se prueban realmente las reglas inbound permitidas

Las reglas para SSH, HTTP y HTTPS se crean sin levantar servicios que escuchen en
esos puertos y sin efectuar una conexión desde Windows u otro host. `curl` hacia
Internet sólo prueba OUTPUT y el retorno stateful; no prueba las reglas de entrada.

La práctica debe levantar un listener controlado, comprobarlo con `ss -lntp` y
probar desde un segundo punto de red. En WSL2 debe documentar si se usa la IP de la
distro en NAT o `localhost`/la red mirrored, y contemplar el firewall Hyper-V.

### Alta — “persistencia” sólo es backup y restauración manual

`lab-iptables-p2.md:163-195` exporta reglas a un archivo, hace flush y las restaura
manualmente. Eso demuestra serialización y restore, pero no que sobrevivan a un
reinicio. La afirmación del escenario en `:47` no queda implementada.

Hay dos opciones coherentes:

1. Renombrar la sección como “backup y restauración manual”.
2. Configurar `netfilter-persistent`/un servicio equivalente y comprobarlo tras
   reiniciar WSL. En WSL, la disponibilidad de systemd también debe verificarse.

### Alta — se destruye el ruleset previo y no se restaura al terminar

Ambas partes usan `-F`/`-X`; la Parte 1 no captura el estado inicial ni ofrece
limpieza final. Además, `-F` no modifica las políticas. Si INPUT ya era DROP,
vaciar primero las reglas puede cortar conectividad. En la Parte 2 las políticas
se cambian a ACCEPT sólo después del flush (`lab-iptables-p2.md:67-72`).

Antes del laboratorio se debe guardar el estado con `iptables-save`. Para resetear,
primero deben establecerse las políticas integradas en ACCEPT y después vaciar las
reglas. Al finalizar se debe validar y restaurar el snapshot original. En una máquina
no descartable conviene una cadena exclusiva del laboratorio o `iptables-apply`.

### Media — LOG no tiene limitación de tasa y el paso no genera tráfico

`lab-iptables-p2.md:126` registra todo paquete no permitido sin `-m limit`; una
ráfaga puede inundar el log del kernel. Debe añadirse, por ejemplo, un límite y un
burst razonables. El Paso 10 (`:199-205`) se titula “Generar tráfico”, pero sólo lee
logs. Debe incluir una sonda concreta desde Windows y verificar tanto el evento como
el incremento del contador de la regla LOG.

### Media — la subred `/24` no se deriva del entorno real

`lab-iptables-p2.md:52-54` pide mirar la IP de `eth0`, pero eso no basta para conocer
la dirección de red y su prefijo. WSL puede asignar un prefijo distinto de `/24` y
el modo mirrored cambia la arquitectura. La práctica debe obtener el CIDR de la
ruta conectada y usarlo como variable, no aceptar cualquier red privada `/24`.

### Media — sólo se filtra IPv4

La lección usa `iptables`, que administra reglas IPv4. IPv6 requiere `ip6tables` o
un ruleset `nftables` en familia `inet`. La práctica debe declararse explícitamente
IPv4 y forzar las pruebas con `-4`, o cubrir también IPv6. De lo contrario puede
enseñar una postura DROP que no existe para la otra familia.

### Media — inconsistencias en la solución del instructor

- `lab-iptables-p2-solucion.md:15-30` presenta como salida “exacta” de
  `iptables-save` reglas con comentarios explicativos inline. Esa no es la salida
  generada mostrada después en `:132-150`. Para comentarios restaurables debe usarse
  `-m comment --comment` o comentarios en líneas propias.
- `lab-iptables-p2-solucion.md:186` afirma que `ssh localhost` queda restringido por
  la subred, pero el tráfico de localhost coincide primero con la regla ACCEPT de
  loopback. Tampoco sirve como prueba de la restricción por origen.
- `lab-iptables-p1-solucion.md:211` recomienda sobrescribir `resolv.conf` con
  `8.8.8.8`; eso puede romper DNS corporativo y contradecir DNS tunneling de WSL.
  Debe diagnosticarse la configuración WSL/Windows antes de cambiar DNS.

### Baja — expectativas demasiado rígidas

No se garantiza que una distro WSL existente arranque sin reglas ni con contadores
en cero. Tampoco debe exigirse siempre `HTTP/1.1 200 OK`; proxies, redirecciones y
políticas de salida pueden producir otro resultado correcto. Conviene comprobar el
exit code y aceptar una respuesta HTTP válida.

## Mejoras de enfoque

- Presentar `iptables` como interfaz clásica. Si aparece `(nf_tables)`, explicar que
  la sintaxis de iptables usa el backend nftables; no mezclar nft y legacy.
- Preferir `-m conntrack --ctstate ESTABLISHED,RELATED`; `state` sigue funcionando,
  pero es un subconjunto de conntrack.
- Completar el ejemplo MASQUERADE: una regla NAT por sí sola no convierte la distro
  en router; también hacen falta forwarding del kernel, dos segmentos/interfaces y
  reglas FORWARD.
- Separar visualmente comandos de PowerShell y Bash con etiquetas de terminal.
- Si las máquinas Windows ya son guests de VirtualBox, evitar WSL2 anidado. Para
  máxima reproducibilidad, ejecutar iptables en una VM Ubuntu con adaptadores NAT y
  Host-only, usando otra VM o el host como cliente de prueba.

## Fuentes primarias

- [Microsoft Learn — What is WSL](https://learn.microsoft.com/en-us/windows/wsl/about)
- [Microsoft Learn — Networking applications with WSL](https://learn.microsoft.com/en-us/windows/wsl/networking)
- [Microsoft Learn — systemd en WSL](https://learn.microsoft.com/en-us/windows/wsl/systemd)
- [Netfilter — proyecto iptables](https://www.netfilter.org/projects/iptables/index.html)
- [Ubuntu manpage — iptables/ip6tables](https://manpages.ubuntu.com/manpages/jammy/man8/iptables.8.html)
- [Ubuntu manpage — extensiones de iptables](https://manpages.ubuntu.com/manpages/jammy/man8/iptables-extensions.8.html)
- [Ubuntu manpage — iptables-save](https://manpages.ubuntu.com/manpages/jammy/man8/iptables-save.8.html)
- [Ubuntu manpage — iptables-restore](https://manpages.ubuntu.com/manpages/jammy/man8/iptables-restore.8.html)
- [Ubuntu manpage — netfilter-persistent](https://manpages.ubuntu.com/manpages/resolute/man8/netfilter-persistent.8.html)
- [Oracle VirtualBox — networking modes](https://docs.oracle.com/en/virtualization/virtualbox/7.2/user/networkingdetails.html)
