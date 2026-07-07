# 🔑 Chuleta de permisos Linux

## Las letras y qué significan

| Letra | Significa | En palabras simples |
|-------|-----------|---------------------|
| `r`   | read      | leer                |
| `w`   | write     | modificar           |
| `x`   | execute   | ejecutar (o entrar a carpeta) |
| `-`   | nada      | permiso negado      |

---

## El primer carácter dice QUÉ ES

| Primer carácter | Tipo                     |
|-----------------|--------------------------|
| `-`             | archivo común            |
| `d`             | directorio (carpeta)     |
| `l`             | enlace (acceso directo)  |

---

## Las 9 letras siguientes se parten en 3 grupos

```
d   rwx   rwx   rwx
│    │     │     │
│    │     │     └─ otros (cualquier usuario del sistema)
│    │     └─ grupo
│    └─ dueño
└─ tipo
```

**Orden fijo siempre:** tipo → dueño → grupo → otros

---

## Truco para leer rápido cada grupo de 3

Las 3 letras de cada grupo siempre van en este orden:

```
r   w   x
│   │   │
leer modificar ejecutar/entrar
```

- Si ves una **letra** → tiene ese permiso.
- Si ves un **guion `-`** → NO tiene ese permiso.

---

## Ejemplos rápidos

| Permisos | Lectura en español                          |
|----------|---------------------------------------------|
| `rwx`    | puede todo                                  |
| `r-x`    | puede leer y entrar, NO modificar           |
| `rw-`    | puede leer y modificar, NO ejecutar         |
| `r--`    | solo lee                                    |
| `---`    | nada de nada                                |

---

## Casos típicos para reconocer al ojo

| Línea         | Qué es                                                              |
|---------------|---------------------------------------------------------------------|
| `-rw-------`  | archivo privado del dueño (ejemplo: claves SSH)                     |
| `-rw-r--r--`  | archivo legible por todos, solo dueño modifica (ejemplo: `.bashrc`) |
| `drwxr-xr-x`  | carpeta normal: dueño todo, los demás entran y leen                 |
| `drwx------`  | carpeta privada del dueño (ejemplo: `.ssh`)                         |
| `drwxrwxrwt`  | carpeta pública compartida con sticky bit (ejemplo: `/tmp`)         |
| `-rwxrwxrwx`  | 🚨 todos pueden todo — bandera roja en auditorías                   |

---

## Letras especiales que aparecen a veces

| Letra | Dónde aparece                  | Qué hace                                                       |
|-------|--------------------------------|----------------------------------------------------------------|
| `t`   | último lugar de "otros"        | **sticky bit** — solo el dueño puede borrar sus archivos dentro de la carpeta |
| `s`   | en el lugar de la `x` del dueño o grupo | **setuid/setgid** — el archivo se ejecuta con permisos del dueño (avanzado) |

---

## Comando para verlo en tu sistema

```bash
ls -la
```

- `ls` → listar
- `-l` → formato largo (muestra permisos, dueño, tamaño, fecha)
- `-a` → incluye archivos ocultos (los que empiezan con `.`)
