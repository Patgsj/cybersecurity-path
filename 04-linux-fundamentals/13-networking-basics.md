# Redes desde Linux — ip, ping, ss

Sesiones prácticas de julio de 2026
Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Qué es una dirección IP

Una IP es la dirección de una computadora dentro de una red, igual que la dirección de una casa o la patente de un vehículo. Cada dispositivo conectado a una red tiene una IP que lo identifica.

---

## Ver la configuración de red — ip a

Comando:

    ip a

Muestra todas las interfaces de red de la máquina y sus direcciones.

Ejemplo de resultado real:

    lo: <LOOPBACK,UP,LOWER_UP>
        inet 127.0.0.1/8 scope host lo

    ens33: <BROADCAST,MULTICAST,UP,LOWER_UP>
        inet 192.168.125.129/24 brd 192.168.125.255 scope global dynamic ens33

Dos interfaces relevantes:

- lo (loopback): red que apunta a la misma máquina. La dirección 127.0.0.1 siempre significa "esta misma computadora", en cualquier sistema Linux.
- ens33: la
