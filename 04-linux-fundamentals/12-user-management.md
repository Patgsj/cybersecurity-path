# Gestión de Usuarios — useradd, passwd, su, userdel

Sesión práctica de julio de 2026
Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## De leer usuarios a administrarlos

Hasta ahora solo se había leído /etc/passwd para ver qué usuarios existen. 
Esta sesión cubre cómo crear, modificar y eliminar usuarios reales del 
sistema — el trabajo activo de un administrador.

---

## Crear un usuario nuevo

Comando: sudo useradd -m nombre_usuario

- sudo → se necesitan privilegios de administrador para modificar /etc/passwd
- useradd → comando para agregar un usuario
- -m → crea también la carpeta home del usuario (/home/nombre_usuario)

Ejemplo real:

sudo useradd -m analista

Este comando no muestra ningún mensaje si sale bien. En Linux, el silencio 
es señal de éxito.

---

## Verificar que el usuario se creó

Comando: grep "nombre_usuario" /etc/passwd

Resultado de ejemplo:

analista:x:1001:1001::/home/analista:/bin/sh

---

## Asignación secuencial de UID

Cada usuario nuevo recibe el siguiente número disponible de UID, 
empezando en 1000 para cuentas humanas.

- patgsj (primer usuario humano del sistema) → UID 1000
- analista (segundo usuario creado) → UID 1001
- el próximo usuario que se cree → UID 1002

Linux no asigna números al azar, sigue un orden secuencial de creación.

---

## Diferencia entre bash y sh

Ambas son shells (intérpretes de comandos), pero con distinto nivel 
de funciones:

- bash: shell completa, con más funciones y comodidades (historial, 
  autocompletado, prompt decorado con usuario/carpeta)
- sh: shell básica, cumple lo esencial pero sin las comodidades de bash

Cuando se crea un usuario con useradd sin más configuración, Ubuntu 
le asigna por defecto /bin/sh, no /bin/bash. Por eso el prompt de un 
usuario nuevo puede verse como un simple $ en vez del prompt decorado 
usuario@maquina:carpeta$.

---

## Asignar contraseña a un usuario

Comando: sudo passwd nombre_usuario

Pide escribir la contraseña dos veces (confirmación). A diferencia de 
useradd, este comando sí confirma el resultado explícitamente:

passwd: password updated successfully

---

## Cambiar de usuario dentro de la misma terminal

Comando: su nombre_usuario

Pide la contraseña del usuario al que se quiere cambiar. Si es correcta, 
la sesión pasa a operar como ese usuario. Se puede confirmar con whoami.

Para volver al usuario anterior sin cerrar la terminal:

exit

---

## Eliminar un usuario

Comando: sudo userdel -r nombre_usuario

- userdel → elimina el usuario
- -r → elimina también su carpeta home (sin esto, la carpeta queda huérfana)

Puede aparecer un mensaje como:

userdel: analista mail spool (/var/mail/analista) not found

Esto no es un error grave — solo indica que no existía una carpeta de 
correo interno para ese usuario, por lo que no había nada que borrar ahí.

Verificación final:

grep "nombre_usuario" /etc/passwd

Si no devuelve nada, el usuario fue eliminado correctamente.

---

## Por qué importa en ciberseguridad

1. Crear y eliminar cuentas de prueba correctamente evita dejar cuentas 
   olvidadas en el sistema — exactamente el tipo de hallazgo de auditoría 
   que se identificó en el proyecto integrador (cuentas "test" abandonadas 
   con acceso activo).

2. Entender la asignación de UID ayuda a detectar anomalías: una cuenta 
   con UID fuera de secuencia o UID 0 disfrazada es señal de alerta.

3. Saber administrar usuarios (crear, dar contraseña, eliminar) es tarea 
   diaria de un sysadmin o analista SOC cuando gestiona altas y bajas de 
   personal en servidores reales.

---

## Comandos resumen

sudo useradd -m nombre        # Crear usuario con carpeta home
grep "nombre" /etc/passwd     # Verificar que existe
sudo passwd nombre            # Asignar contraseña
su nombre                     # Cambiar de usuario
exit                          # Volver al usuario anterior
sudo userdel -r nombre        # Eliminar usuario y su carpeta home
