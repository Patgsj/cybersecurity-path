# 🗑️ Borrar archivos y carpetas — rm, rmdir

> Sesión práctica del 27 de mayo de 2026  
> Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## ⚠️ Advertencia fundamental

En Linux, `rm` **NO envía a la papelera**. Borra de forma **permanente e inmediata**.  
No hay "deshacer", no hay Ctrl+Z, no hay papelera de reciclaje.

---

## Comandos de borrado

| Comando | Qué borra | Nivel de peligro |
|---------|-----------|------------------|
| `rm archivo.txt` | Un archivo, sin preguntar | ⚠️ Medio |
| `rm -i archivo.txt` | Un archivo, preguntando antes | ✅ Seguro |
| `rmdir carpeta` | Solo carpetas **vacías** | ✅ Seguro |
| `rm -r carpeta` | Carpeta **+ todo su contenido** | 🔴 Alto |
| `rm -rf /` | (NUNCA ejecutar) destruiría el sistema | ☠️ Mortal |

---

## `rm` — borrar archivos

```bash
rm archivo.txt          # Borra inmediato, sin confirmar
rm -i archivo.txt       # Pregunta antes: y (sí) / n (no)
```

- `-i` = **interactive** → pide confirmación antes de borrar
- Respuesta `y` = borra | `n` = cancela

---

## `rmdir` — borrar carpetas vacías

```bash
rmdir carpeta_vacia
```

- Solo funciona si la carpeta está **vacía**
- Si tiene contenido: error `Directory not empty`
- Es la opción **segura** para carpetas

---

## `rm -r` — borrar carpetas con contenido

```bash
rm -r carpeta_llena
```

- `-r` = **recursive** → borra la carpeta y TODO lo que contiene
- Comando muy peligroso — destruye todo el árbol de una vez

---

## 🚨 Insights de seguridad

1. **`rm` no tiene papelera.** Lo borrado desaparece permanentemente.

2. **El `-i` protege solo si LEES la pregunta.** Responder `y` en automático anula la protección.

3. **`-` (guion medio) ≠ `_` (guion bajo).** Linux los trata como caracteres distintos. Causa errores "No such file or directory". Solución: autocompletar con la tecla **TAB**.

4. **Regla de oro antes de `rm -r`:**  
   Siempre verificar con `pwd` (dónde estoy) y `ls` (qué voy a borrar). Nunca ejecutar en automático.

5. **`rm -rf /` es el comando más destructivo de Linux.** Ha destruido servidores enteros por un error de tipeo.

---

## Comandos útiles aprendidos de paso

| Comando | Función |
|---------|---------|
| `clear` | Limpia la pantalla de la terminal (atajo: Ctrl+L) |
| `carpeta/archivo` | La barra `/` indica "dentro de" |
| TAB | Autocompleta nombres de archivos/carpetas existentes |

---

## Ciclo completo de manejo de archivos

Con esta sesión se cierra el ciclo fundamental:

- **Crear:** `mkdir`, `touch`
- **Modificar permisos:** `chmod`
- **Borrar:** `rm`, `rmdir`, `rm -r`

---

## Próximo paso

- Análisis de `/etc/passwd` (lista de usuarios del sistema)
- Lectura de logs en `/var/log/`
