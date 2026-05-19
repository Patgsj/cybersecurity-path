# 📂 Crear archivos, carpetas y modificar permisos

> Sesión práctica del 18 de mayo de 2026  
> Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Comandos aprendidos

### 🟢 `mkdir` — crear directorios

```bash
mkdir lab-linux
```

- **Lectura:** *MaKe DIRectory*
- Convención profesional: evitar espacios en nombres. Usar `-` o `_`.

---

### 🟢 `touch` — crear archivos vacíos

```bash
touch nota.txt
touch archivo1.txt archivo2.txt archivo3.txt
```

- **Lectura:** crea archivos vacíos (también actualiza fecha si ya existen)
- Acepta múltiples archivos en un solo comando
- **🔒 Seguridad:** Linux NO marca archivos como ejecutables por defecto (`x` ausente). Diferencia clave con Windows — protege contra malware ejecutado por accidente.

---

### 🟢 `chmod` — modificar permisos

**Sintaxis simbólica:**

```bash
chmod [QUIÉN][OPERACIÓN][PERMISO] archivo
```

| Quién | Operación | Permiso |
|-------|-----------|---------|
| `u` user (dueño) | `+` dar | `r` leer |
| `g` group (grupo) | `-` quitar | `w` modificar |
| `o` others (otros) | `=` asignar exacto | `x` ejecutar |
| `a` all (todos) | | |

**Ejemplos prácticos:**

```bash
chmod u+x archivo.sh        # Dar al dueño permiso de ejecutar
chmod go-rwx secreto.txt    # Quitar a grupo y otros TODO
chmod o+r publico.txt       # Dar a otros permiso de leer
chmod g-w archivo.txt       # Quitar al grupo permiso de modificar
```

---

## Lab realizado

Carpeta `lab-linux` creada en `/home/patgsj` con 4 archivos configurados en distintos perfiles de seguridad:

| Archivo | Permisos finales | Perfil de seguridad |
|---------|------------------|---------------------|
| `archivo1.txt` | `-rwxrw-r--` | Script ejecutable, grupo modifica, otros leen |
| `archivo2.txt` | `-rw-r--r--` | Archivo público estándar |
| `archivo3.txt` | `-rw-r--r--` | Archivo público estándar |
| `nota.txt` | `-rw-rw----` | Archivo de equipo, nadie fuera del grupo accede |

---

## Atajos universales de navegación

| Símbolo | Significa |
|---------|-----------|
| `.` | Directorio actual |
| `..` | Directorio padre (subir un nivel) |
| `~` | Home del usuario (`/home/patgsj`) |
| `/` | Raíz del sistema |
| `cd` (sin argumentos) | Volver al home automáticamente |

---

## 🚨 Insights de ciberseguridad

1. **Principio de mínimo cambio en `chmod`**  
   Modificar solo el permiso necesario, no más. Mejor `u+x` que `u+rwx` cuando solo falta `x`.  
   Razones: trazabilidad, reversibilidad, lectura clara en auditorías.

2. **Verificar siempre con `ls -la` después de `chmod`**  
   Una sola letra mal puede bloquear tu propio acceso. Confirmar el cambio antes de seguir.

3. **Linux protege contra typos**  
   Si escribes mal el nombre, el comando falla con "No such file or directory". No crea archivos por error.

4. **Comandos desconocidos NO se ejecutan**  
   Si falta el verbo (ej: `u+x archivo` sin `chmod`), Linux responde "command not found". Protección contra inyección.

5. **El prompt es tu GPS**  
   Siempre revisar la línea del prompt (`~$`, `~/lab-linux$`, etc.) antes de ejecutar comandos sobre archivos. Las rutas relativas solo funcionan desde la carpeta correcta.

---

## Lección lateral: Ctrl+C

`Ctrl+C` cancela el comando actual o limpia una línea en construcción.  
Es el **botón de emergencia** de Linux — útil cuando un comando se cuelga o escupe texto sin parar.

---

## Próximo paso

- Comando `rm` para borrar archivos y carpetas
- Análisis de `/etc/passwd` (lista de usuarios del sistema)
- Lectura de logs en `/var/log/`
