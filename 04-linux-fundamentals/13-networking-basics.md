# Redes desde Linux — ip, ping, ss

Sesiones prácticas de julio de 2026
Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Qué es una dirección IP

Una IP es la dirección de una computadora dentro de una red, igual que la dirección de una casa o la patente de un vehículo. Cada dispositivo conectado a una red tiene una IP que lo identifica.

---

## Ver la configuración de red — ip a

Comando: ip a

Muestra todas las interfaces de red de la máquina y sus direcciones.

Ejemplo de resultado real:

lo: <LOOPBACK,UP,LOWER_UP>
    inet 127.0.0.1/8 scope host lo

ens33: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 192.168.125.129/24 brd 192.168.125.255 scope global dynamic ens33

Dos interfaces relevantes:

- lo (loopback): red que apunta a la misma máquina. La dirección 127.0.0.1 siempre significa "esta misma computadora", en cualquier sistema Linux.
- ens33: la tarjeta de red real, la que conecta la máquina a la red (física o virtual). Ahí aparece la IP real de la máquina.

Para identificar la IP en el resultado, buscar la línea que dice inet (sin el 6). Las líneas con inet6 corresponden a IPv6, un tema aparte.

---

## Probar conectividad — ping

Comando: ping destino

Ejemplo: ping google.com

Envía mensajes pequeños a un destino y espera respuesta. Funciona con cualquier destino con conexión a internet (dominio o IP directa). Google se usa como ejemplo típico solo por ser un destino confiable, no porque el comando dependa de él.

El comando no se detiene solo. Para cortarlo: Ctrl + C

### Lectura de una línea de resultado

64 bytes from cj-in-f113.1e100.net (142.251.0.113): icmp_seq=1 ttl=128 time=20.3 ms

- 142.251.0.113: la IP del destino
- icmp_seq: número de secuencia del mensaje (1, 2, 3...)
- time: tiempo de ida y vuelta en milisegundos (dato más importante)

Tiempo bajo (menos de 50ms) indica buena conexión. Tiempo alto (200ms+) indica conexión lenta.

### Lectura del resumen final

--- google.com ping statistics ---
34 packets transmitted, 34 received, 0% packet loss, time 33067ms
rtt min/avg/max/mdev = 17.616/22.544/34.476/3.560 ms

- packet loss: porcentaje de mensajes perdidos. 0% es ideal.
- rtt (round trip time): min, avg (promedio), max y mdev (variación) de los tiempos de respuesta.

Un packet loss alto (ej. 45%) junto a tiempos muy altos indica problema real de red: saturación, firewall bloqueando, o posible ataque de denegación de servicio.

---

## Qué es un puerto

Si la IP es la dirección de un edificio, el puerto es el número de departamento específico dentro de ese edificio. Cada servicio de una máquina "vive" en un puerto distinto.

Formato: IP:puerto

Ejemplo: 127.0.0.1:631
- 127.0.0.1 = la IP (esta misma máquina)
- 631 = el puerto (en este caso, el puerto estándar de CUPS, sistema de impresión de Linux)

---

## Ver conexiones y puertos activos — ss

Comando: ss -tunlp

- -t: conexiones TCP
- -u: conexiones UDP
- -n: mostrar números (IP y puerto), no nombres
- -l: mostrar los que están escuchando (LISTEN)
- -p: mostrar qué programa usa cada conexión

Columnas relevantes: Netid (tcp/udp), State, Local Address:Port, Peer Address:Port

---

## Estados de conexión: LISTEN vs ESTABLISHED

- LISTEN: el servicio está esperando que alguien se conecte. Todavía no hay nadie conectado. Analogía: una recepcionista sentada esperando que suene el teléfono.
- ESTABLISHED: ya existe una conexión activa en curso ahora mismo. Analogía: la recepcionista ya está hablando con alguien por teléfono.

Para un analista que investiga "con quién está hablando mi sistema ahora mismo", el estado relevante es ESTABLISHED, porque indica actividad real en curso.

---

## Por qué importa en ciberseguridad

1. Escaneo de puertos: un atacante recorre todos los puertos de un sistema (herramientas como Nmap) para detectar cuáles están abiertos (en LISTEN). Cada puerto abierto es una posible puerta de entrada.

2. Principio de mínimo puerto abierto: cerrar todo puerto que no se use activamente reduce la superficie de ataque. Un sistema con muchos puertos abiertos sin justificación es un hallazgo de auditoría.

3. Diagnóstico de incidentes: revisar conexiones ESTABLISHED con IPs desconocidas permite detectar comunicación no autorizada (por ejemplo, malware hablando con un servidor externo).

4. Diagnóstico de conectividad: ping es el primer comando para confirmar si un servidor está caído, si hay problemas de red, o si el problema está en otro lado.

---

## Comandos resumen

ip a                    # Ver interfaces de red y direcciones IP
ping destino            # Probar conectividad (Ctrl+C para detener)
ss -tunlp               # Ver conexiones y puertos activos
sudo ss -tunlp          # Igual, con detalle de programa si se requiere
