Chuleta de permisos Linux
Las letras y qué significan
LetraSignificaEn palabras simplesrreadleerwwritemodificarxexecuteejecutar (o entrar a carpeta)-nadapermiso negado

El primer carácter dice QUÉ ES
Primer carácterTipo-archivo comúnddirectorio (carpeta)lenlace (acceso directo)

Las 9 letras siguientes se parten en 3 grupos
d   rwx   rwx   rwx
│    │     │     │
│    │     │     └─ otros (cualquier usuario del sistema)
│    │     └─ grupo
│    └─ dueño
└─ tipo
Orden fijo siempre: tipo → dueño → grupo → otros

Truco para leer rápido cada grupo de 3
Las 3 letras de cada grupo siempre van en este orden:
r   w   x
│   │   │
leer modificar ejecutar/entrar
Si ves una letra → tiene ese permiso.
Si ves un guion - → NO tiene ese permiso.

Ejemplos rápidos
PermisosLectura en españolrwxpuede todor-xpuede leer y entrar, NO modificarrw-puede leer y modificar, NO ejecutarr--solo lee---nada de nada

Casos típicos para reconocer al ojo
LíneaQué es-rw-------archivo privado del dueño (ejemplo: claves SSH)-rw-r--r--archivo legible por todos, solo dueño modifica (ejemplo: .bashrc)drwxr-xr-xcarpeta normal: dueño todo, los demás entran y leendrwx------carpeta privada del dueño (ejemplo: .ssh)drwxrwxrwtcarpeta pública compartida con sticky bit (ejemplo: /tmp)-rwxrwxrwx🚨 todos pueden todo — bandera roja en auditorías

Letras especiales que aparecen a veces
LetraDónde apareceQué hacetúltimo lugar de "otros"sticky bit — solo el dueño puede borrar sus archivos dentro de la carpetasen el lugar de la x del dueño o gruposetuid/setgid — el archivo se ejecuta con permisos del dueño (avanzado, lo verás después)
