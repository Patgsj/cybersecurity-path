# 🔎 Buscar archivos con find — el detective del sistema

> Sesión práctica del 11 de junio de 2026  
> Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Qué es find

`find` es el comando de búsqueda más poderoso de Linux. Recorre el sistema de archivos buscando archivos según criterios: nombre, fecha, tamaño, permisos o dueño. A diferencia de `ls`, que solo muestra una carpeta, `find` entra recursivamente en todas las subcarpetas.

Es la herramienta clave para responder preguntas tipo:
- ¿Dónde está este archivo en el sistema?
- ¿Qué archivos se modificaron en las últimas horas?
- ¿Hay scripts shell en lugares sospechosos?
- ¿Hay archivos con permisos peligrosos?

---

## Sintaxis básica

    find [dónde buscar] -name "[qué buscar]"

Ejemplo:

    find /home -name "nota.txt"

Lectura:
- `find` → buscar
- `/home` → empezar desde esta carpeta (revisa también subcarpetas)
- `-name "nota.txt"` → archivos llamados exactamente "nota.txt"

Resultado típico: la ruta completa donde está el archivo, por ejemplo `/home/patgsj/lab-linux/nota.txt`.

---

## Comodín `*` para nombres parciales

El asterisco `*` significa "cualquier cosa". Sirve para buscar por extensión o por parte del nombre.

| Comando | Qué busca |
|---------|-----------|
| `find /home -name "*.txt"` | Todos los archivos `.txt` |
| `find /home -name "config*"` | Archivos que empiezan con "config" |
| `find /home -name "*log*"` | Archivos que contienen "log" en el nombre |
| `find /home -name ".*"` | Archivos ocultos (empiezan con punto) |

---

## Búsqueda recursiva — el poder real de find

`find` revisa la carpeta indicada y TODAS sus subcarpetas hasta el fondo. No hay que decirle dónde mirar dentro — busca solo.

Ejemplo: al ejecutar `find /home/patgsj/lab-linux -name "*.txt"`, encontró 6 archivos `.txt`, uno de ellos dentro de una subcarpeta (`carpeta-ejemplo/ejemplo.txt`) que podría haberse olvidado.

Esto es exactamente lo que hace un analista en una investigación de incidente: el atacante no deja un cartel diciendo "aquí está mi archivo malicioso". Se buscan patrones y `find` los detecta sin importar dónde estén escondidos.

---

## Manejo de errores: `2>/dev/null`

Cuando se ejecuta `find` sobre carpetas del sistema, suelen aparecer errores tipo "Permission denied" en subcarpetas que solo root puede leer.

Para silenciar esos errores y ver solo los resultados reales:

    find /tmp -name "*.sh" 2>/dev/null

Lectura del truco:
- `2>` → redirige los errores
- `/dev/null` → al "agujero negro" de Linux, donde lo que entra desaparece

Es el equivalente a decirle: "busca, pero si hay errores no me los muestres".

---

## Comandos útiles aprendidos

    find /home -name "nota.txt"              # Por nombre exacto
    find /home -name "*.txt"                 # Por extensión
    find /home -name ".*"                    # Archivos ocultos
    find /tmp -name "*.sh" 2>/dev/null       # Versión silenciosa (sin errores)

---

## Archivos ocultos importantes encontrados en /home

La búsqueda `find /home -name ".*"` reveló archivos relevantes para seguridad:

| Archivo | Qué es |
|---------|--------|
| `.bash_history` | Historial COMPLETO de comandos ejecutados |
| `.ssh` | Claves SSH (alta sensibilidad) |
| `.bashrc` | Configuración de shell Bash |
| `.profile` | Configuración del entorno de usuario |
| `.sudo_as_admin_successful` | Marcador de uso previo de sudo |

Estos archivos son objetivos de primera prioridad para un atacante con acceso al sistema.

---

## Aplicaciones reales en ciberseguridad

1. **Respuesta a incidentes**  
   Después de una intrusión, buscar archivos modificados recientemente para identificar qué dejó el atacante.

2. **Búsqueda de archivos sospechosos**  
   Scripts en `/tmp`, archivos con nombres tipo "password", "creds", "backup" en lugares inesperados.

3. **Hardening preventivo**  
   Buscar archivos con permisos peligrosos (777) o ejecutables en carpetas donde no deberían estar.

4. **Auditoría de configuraciones**  
   Localizar todos los archivos `.conf` o `.config` del sistema para revisarlos.

---

## Insights de ciberseguridad

1. **`find` es el detective del sistema.** Cualquier analista lo usa decenas de veces al día. Es uno de los primeros comandos que se aprende y de los últimos que se domina (tiene opciones muy avanzadas).

2. **`/tmp` es zona caliente.** Es lugar favorito de atacantes para dejar archivos temporales porque cualquier usuario puede escribir ahí. Auditar `/tmp` regularmente es práctica básica.

3. **Los archivos ocultos no son seguridad.** `.archivo` solo significa "Linux lo oculta a `ls`". Cualquiera con `find` o `ls -a` los ve. No usar archivos ocultos como "protección".

4. **`2>/dev/null` es esencial en scripts.** Un script de auditoría con errores ruidosos es ilegible. Silenciar errores que no aportan permite ver solo lo importante.

5. **El que domina find encuentra cualquier cosa.** Combinado con `grep` y pipes, `find` se vuelve la herramienta universal de descubrimiento en Linux.

---

## Errores comunes que se aprendieron en la sesión

**Comillas sin cerrar:**  
Escribir `find /tmp -name "*.sh` sin cerrar la comilla deja la terminal esperando con `>`. Solución: cerrar la comilla y Enter, o presionar Ctrl+C para cancelar.

**Confundir `find` con `ls`:**  
`ls` muestra una carpeta. `find` busca en una carpeta y todas sus subcarpetas. Son herramientas distintas.
