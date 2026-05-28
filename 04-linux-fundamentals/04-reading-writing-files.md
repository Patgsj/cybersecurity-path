---

## Comandos para LEER archivos

### `cat` — mostrar contenido completo

```bash
cat archivo.txt
```

- Vuelca TODO el contenido de golpe en pantalla
- Ideal para archivos **cortos**
- Problema: en archivos largos, solo alcanzas a ver el final

### `less` — leer página por página

```bash
less archivo.txt
```

- Muestra el archivo de forma paginada, sin saturar la pantalla
- Ideal para archivos **largos** (logs con miles de líneas)

**Navegación dentro de less:**

| Tecla | Acción |
|-------|--------|
| ↓ / ↑ | Bajar / subir una línea |
| Barra espaciadora | Avanzar una página |
| `b` | Retroceder una página |
| `g` / `G` | Ir al principio / final |
| `/texto` | Buscar una palabra |
| `q` | **Salir** (importante) |

---

## Comandos para ESCRIBIR archivos

### `nano` — editor de texto

```bash
nano archivo.txt
```

**Atajos (el `^` significa Ctrl):**

| Tecla | Acción |
|-------|--------|
| `Ctrl+O` | Guardar (Output) |
| `Ctrl+X` | Salir (pregunta si guardar) |
| `Ctrl+W` | Buscar texto |
| `Ctrl+K` | Cortar línea |

Secuencia para guardar y salir: `Ctrl+X` → `Y` → `Enter`

---

## Operadores de redirección — `>` y `>>`

```bash
echo "texto" > archivo.txt     # SOBRESCRIBE (borra lo anterior)
echo "texto" >> archivo.txt    # AGREGA al final (conserva lo anterior)
```

| Operador | Acción | Memoria |
|----------|--------|---------|
| `>` | Sobrescribe (borra y reemplaza) | una flecha = "pisa" |
| `>>` | Agrega al final (no borra) | dos flechas = "apila" |

**Nota:** `echo >` también CREA el archivo si no existe.

---

## Tres formas de crear un archivo

| Método | Crea archivo | Con contenido |
|--------|--------------|---------------|
| `touch archivo.txt` | ✅ | ❌ vacío |
| `echo "texto" > archivo.txt` | ✅ | ✅ una línea |
| `nano archivo.txt` | ✅ | ✅ lo que escribas |

---

## 🚨 Insights de ciberseguridad

1. **`>` vs `>>` — una flecha de diferencia, consecuencias enormes**  
   El `>` borra todo el contenido previo sin avisar. El `>>` conserva.

2. **Borrado de huellas con `>`**  
   Un atacante puede vaciar un log de seguridad con:
```bash
   echo "" > /var/log/auth.log
```
   Esto borra el registro de sus accesos. Por eso los logs críticos se configuran en modo "append only" (solo agregar).

3. **Los logs SIEMPRE se escriben con `>>`**  
   Si un programa usara `>` para loguear, borraría todo el historial en cada escritura.

4. **`less` es la herramienta para análisis de logs**  
   Permite leer miles de líneas sin saturar la pantalla, con búsqueda integrada (`/texto`).

---

## Truco útil

```bash
seq 1 100 > numeros.txt
```

`seq` genera una secuencia de números — útil para crear archivos de prueba con muchas líneas.

---

## Próximo paso

- Análisis de `/etc/passwd` (lista de usuarios del sistema)
- Lectura de logs reales en `/var/log/`
- Comandos de búsqueda: `grep`, `find`
