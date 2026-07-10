# Auditoría Básica de Seguridad — Sistema Linux

Proyecto integrador de la sección Linux Fundamentals.
Auditoría realizada sobre un sistema Ubuntu 24.04 LTS personal, 
aplicando los comandos y conceptos aprendidos durante el curso.

Fecha: julio 2026

---

## 1. Identificación del Sistema

- Usuario auditor: patgsj
- Identidad completa: UID 1000, GID 1000
- Grupos secundarios: adm, cdrom, sudo, dip, plugdev, users, lpadmin
- Privilegios elevados: sí (miembro del grupo sudo)
- Hostname: patgsj
- Sistema operativo: Ubuntu 24.04 LTS
- Kernel: Linux 6.17.0-29-generic (compilado el 11 mayo 2026)
- Arquitectura: x86_64 (64 bits)

**Observación:** el sistema operativo y el kernel están actualizados.
El usuario que ejecuta esta auditoría tiene privilegios sudo,
lo que permite acceso completo a archivos del sistema.

---

## 2. Análisis de Usuarios y Privilegios

**Cuentas con capacidad de login (shell /bin/bash):**

- root (UID 0) — administrador del sistema
- patgsj (UID 1000) — usuario auditor

Total: 2 cuentas. No se detectaron cuentas de prueba, backup 
o abandonadas con login habilitado.

**Verificación de cuentas UID 0 (root encubierto):**

- Comando: `awk -F: '$3 == 0 {print $1}' /etc/passwd`
- Resultado: root

Solo la cuenta root legítima posee UID 0. No se detectaron 
cuentas backdoor con privilegios de root disfrazadas bajo 
otro nombre.

**Miembros del grupo sudo:**

- Comando: `grep "sudo" /etc/group`
- Resultado: sudo:x:27:patgsj

Único miembro: patgsj. Cumple el principio de mínimo privilegio 
— solo el usuario auditor tiene capacidad de escalar a root.

**Conclusión de la sección:** el sistema presenta una configuración 
de usuarios saludable. Sin cuentas huérfanas, sin privilegios 
excesivos, sin señales de compromiso en la gestión de accesos.

---

## 3. Análisis de Archivos Sensibles

**Búsqueda de archivos con contraseñas en texto plano:**

- Comando: `find /home -iname "*password*" 2>/dev/null`
- Resultado: sin coincidencias

No se encontraron archivos con nombres relacionados a contraseñas 
en el directorio home.

**Verificación de permisos en ~/.ssh:**

- Comando: `ls -la /home/patgsj/.ssh`
- Resultado: permisos correctos (rwx------ en carpeta, rw------- en authorized_keys). Archivo vacío, sin claves configuradas.

**Búsqueda de archivos con permisos 777:**

- Comando: `find /home -perm 777 2>/dev/null`
- Resultado: 89 coincidencias, todas dentro de la infraestructura interna de Snap (Firefox, firmware-updater, snapd-desktop-integration). Sin riesgo real — son archivos de caché y configuración gestionados automáticamente por el sistema de paquetes.

**Conclusión de la sección:** no se detectaron archivos sensibles 
expuestos, credenciales en texto plano, ni configuraciones de 
permisos peligrosas creadas por el usuario. Los únicos resultados 
con permisos 777 corresponden a comportamiento esperado del 
sistema de paquetes Snap.

---

## 4. Conclusiones y Recomendaciones

**Conclusión general:**

El sistema se encuentra seguro. No se encontró ningún archivo con 
contraseñas en texto plano. Los permisos de la carpeta .ssh eran 
correctos, ya que solo el usuario auditor, único usuario del 
sistema, tiene acceso a ella. Los archivos con permisos 777 
encontrados pertenecían al sistema Snap, no a archivos creados 
por el usuario, por lo que no representan un riesgo real.

**Recomendaciones:**

1. Mantener el sistema y el kernel actualizados regularmente.
2. Revisar periódicamente /etc/passwd y el grupo sudo para 
   detectar cuentas no autorizadas.
3. Configurar claves SSH en .ssh/authorized_keys únicamente si 
   se necesita acceso remoto, respetando siempre los permisos 
   correctos (700 en la carpeta, 600 en los archivos).

---

## Herramientas y comandos utilizados

whoami, id, hostname, uname -a, grep, awk, find, ls -la

Referencia completa de comandos disponible en:
`04-linux-fundamentals/11-linux-cheatsheet.md`
