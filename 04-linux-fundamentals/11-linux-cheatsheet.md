# 📋 Cheat Sheet — Linux Fundamentals

> Referencia rápida de todos los comandos aprendidos.
> Consultar aquí en vez de memorizar.

---

## Navegación básica

| Comando | Qué hace |
|---------|----------|
| whoami | Muestra el usuario logueado |
| pwd | Muestra en qué carpeta estoy parado |
| ls | Lista archivos y carpetas del lugar actual |
| ls -la | Lista todo, con detalles y archivos ocultos |
| cd nombre_carpeta | Entra a una carpeta |
| cd .. | Sube un nivel |
| cd | Va directo a mi home |
| clear | Limpia la pantalla |

---

## Crear y modificar

| Comando | Qué hace |
|---------|----------|
| mkdir nombre | Crea una carpeta |
| touch archivo.txt | Crea un archivo vacío |
| chmod u+x archivo | Modifica permisos (u=dueño, g=grupo, o=otros, a=todos / +=agregar, -=quitar, ==fijar / r,w,x) |

---

## Eliminar

| Comando | Qué hace |
|---------|----------|
| rm archivo.txt | Borra un archivo (sin confirmación, sin papelera) |
| rmdir carpeta | Borra una carpeta SOLO si está vacía |
| rm -r carpeta | Borra una carpeta con todo su contenido (peligroso) |
| rm -i archivo | Borra pidiendo confirmación |

---

## Leer y escribir archivos

| Comando | Qué hace |
|---------|----------|
| cat archivo.txt | Muestra el contenido completo |
| less archivo.txt | Muestra paginado (para archivos largos). Salir con q |
| nano archivo.txt | Abre editor de texto. Guardar: Ctrl+O. Salir: Ctrl+X |
| echo "texto" > archivo | Escribe texto en archivo (SOBREESCRIBE todo) |
| echo "texto" >> archivo | Agrega texto al final (sin borrar lo anterior) |

---

## Búsqueda de texto

| Comando | Qué hace |
|---------|----------|
| grep "palabra" archivo | Busca líneas que contengan "palabra" DENTRO de un archivo |
| grep -i "palabra" archivo | Busca sin importar mayúsculas/minúsculas |
| grep -v "palabra" archivo | Muestra las líneas que NO contienen "palabra" |
| grep -c "palabra" archivo | Cuenta cuántas líneas coinciden |
| grep -n "palabra" archivo | Muestra el número de línea junto al resultado |
| grep -r "palabra" carpeta/ | Busca dentro de todos los archivos de una carpeta |

Diferencia clave: grep busca TEXTO dentro de archivos. find busca ARCHIVOS.

---

## Pipes y encadenamiento

| Comando | Qué hace |
|---------|----------|
| comando1 pipe comando2 | Pasa la salida de comando1 como entrada a comando2 |
| wc -l | Cuenta líneas |
| wc -w | Cuenta palabras |
| wc -c | Cuenta caracteres/bytes |
| sort archivo | Ordena alfabéticamente |
| sort -n archivo | Ordena numéricamente (de menor a mayor) |
| sort -nr archivo | Ordena numéricamente descendente (mayor a menor) |

Ejemplo combinado: grep "error" log.txt | wc -l → cuenta cuántas líneas tienen "error"

---

## /etc/passwd — usuarios del sistema

| Comando | Qué hace |
|---------|----------|
| cat /etc/passwd | Ver todos los usuarios del sistema |
| grep "/bin/bash" /etc/passwd | Ver solo cuentas que pueden hacer login |
| grep "/bin/bash" /etc/passwd | wc -l | Contar cuántas cuentas tienen login |

Estructura de una línea: nombre:pass:UID:GID:info:home:shell

Regla clave: shell termina en /bin/bash = persona (puede loguear). Termina en /usr/sbin/nologin o /bin/false = servicio (no puede loguear).

---

## /etc/group y privilegios — UID/GID

| Comando | Qué hace |
|---------|----------|
| cat /etc/group | Ver todos los grupos del sistema |
| groups | Ver mis propios grupos |
| id | Ver mi UID, GID y todos mis grupos con sus números |
| grep "sudo" /etc/group | Ver quién pertenece al grupo sudo |
| awk -F: '$3 == 0 {print $1}' /etc/passwd | Buscar cuentas con UID 0 (root encubierto) |

Regla clave: UID = identifica usuario. GID = identifica grupo. UID 0 = siempre es root, sin importar el nombre.

---

## find — búsqueda de archivos

| Comando | Qué hace |
|---------|----------|
| find /ruta -name "archivo.txt" | Busca por nombre exacto |
| find /ruta -name "*.txt" | Busca por extensión (comodín *) |
| find /ruta -name ".*" | Busca archivos ocultos |
| find /ruta -iname "*texto*" | Busca sin importar mayúsculas/minúsculas |
| find /ruta -perm 777 | Busca archivos con permisos peligrosos (777) |
| comando 2>/dev/null | Silencia mensajes de error (ej: permission denied) |

---

## Procesos — ps y top

| Comando | Qué hace |
|---------|----------|
| ps | Procesos de mi terminal actual |
| ps aux | TODOS los procesos del sistema |
| ps aux | grep nombre | Filtrar procesos por nombre o usuario |
| ps aux | grep x | grep -v grep | Filtrar quitando el ruido del propio grep |
| top | Ver procesos en tiempo real |
| top → M | Ordenar por memoria (dentro de top) |
| top → P | Ordenar por CPU (dentro de top) |
| top → q | Salir de top |

---

## Permisos rwx — lectura rápida

-   rwx   rwx   rwx
tipo dueño grupo otros

- = archivo / d = carpeta
r = read (leer) / w = write (escribir) / x = execute (ejecutar)
Orden: Dueño, Grupo, Otros (D-G-O)

---

## Comandos de emergencia / utilidad

| Comando | Qué hace |
|---------|----------|
| Ctrl + C | Cancela el comando actual |
| Ctrl + L | Limpia pantalla (igual que clear) |
| TAB | Autocompleta nombres de archivos/carpetas |
| Esc | Cancela una pregunta dentro de un programa interactivo (como top) |

---

## Notas de seguridad rápidas

1. UID 0 = root, sin importar el nombre de la cuenta.
2. /etc/passwd es público, cualquiera lo puede leer. Las contraseñas reales están en /etc/shadow.
3. Permisos 777 son peligrosos en archivos de usuario — evitar siempre, salvo casos justificados de infraestructura (como Snap).
4. La carpeta .ssh debe tener permisos 700 (rwx------) y sus archivos 600 (rw-------). Si están más abiertos, SSH puede rechazar la conexión.
5. Zombies en top — pocos son normales, muchos (10+) son señal de alerta.
