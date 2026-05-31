# 🔗 Pipes y encadenamiento de comandos — la filosofía Unix

> Sesión práctica del 30 de mayo de 2026  
> Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Qué es un pipe

El pipe (carácter `|`) conecta la salida de un comando con la entrada del siguiente. Permite encadenar comandos para construir análisis complejos a partir de piezas simples.

Sintaxis básica:

    comando1 | comando2 | comando3

Lectura: "la salida de comando1 entra a comando2, la salida de comando2 entra a comando3".

En el teclado Latin American: `Alt Gr` + `1` (o `Alt Gr` + `<`).

---

## La filosofía Unix detrás del pipe

Principio histórico de Unix (años 70):

> "Haz una sola cosa, pero hazla bien. Y diseña programas para que trabajen juntos."

Cada comando hace UNA cosa bien:
- `ls` solo lista
- `grep` solo filtra  
- `wc` solo cuenta
- `sort` solo ordena

Por separado son limitados. Encadenados con pipes, hacen cualquier análisis imaginable. Es lo opuesto a herramientas modernas que intentan hacer todo en un programa gigante.

---

## Comando wc — Word Count

Cuenta líneas, palabras o caracteres recibidos por pipe (o de un archivo directo).

| Opción | Qué cuenta |
|--------|-----------|
| `wc -l` | Líneas |
| `wc -w` | Palabras |
| `wc -c` | Caracteres (incluye saltos de línea invisibles) |

Ejemplo:

    grep "5" numeros.txt | wc -l

Cuenta cuántas líneas contienen "5". Resultado: 19.

---

## Caracteres invisibles importan

`wc -c` cuenta MÁS caracteres que los visibles porque incluye los saltos de línea (`\n`) al final de cada línea.

Ejemplo en numeros.txt filtrado por "5":
- 37 caracteres visibles
- 19 saltos de línea invisibles
- Total: 56 caracteres reales

Esto importa en ciberseguridad porque:
- Hashes MD5/SHA256 cambian con saltos de línea distintos (Windows `\r\n` vs Linux `\n`)
- Atacantes esconden caracteres invisibles en payloads para evadir filtros
- Una contraseña con espacio invisible al final genera hash diferente

---

## Comando sort — ordenar líneas

| Opción | Qué hace |
|--------|----------|
| `sort` | Orden alfabético (por defecto) |
| `sort -n` | Orden numérico real |
| `sort -r` | Orden inverso |
| `sort -nr` | Numérico de mayor a menor |

---

## ⚠️ Trampa importante: sort sin -n

Ejecutar `sort` con números sin la opción `-n` produce resultados engañosos:

    grep "5" numeros.txt | sort

Devuelve: 15, 25, 35, 45, **5**, 50, 51... porque para sort los números son texto. "5" viene después de "45" porque la primera letra "5" es mayor que "4".

Solución:

    grep "5" numeros.txt | sort -n

Devuelve correctamente: 5, 15, 25, 35, 45, 50, 51, 52...

---

## Encadenar 3 comandos — ejemplo real

    ls | grep "txt" | wc -l

Lectura paso a paso:
1. `ls` lista todos los archivos de la carpeta
2. `| grep "txt"` filtra solo los que contienen "txt" en el nombre
3. `| wc -l` cuenta cuántos quedaron

Resultado: cantidad de archivos .txt en la carpeta.

Es un análisis completo en una sola línea, sin crear archivos intermedios.

---

## 🚨 Insights de ciberseguridad

1. **El pipe es el concepto bisagra de Linux.** Toda automatización seria en blue team y pentesting se basa en encadenar comandos. Sin pipes, el análisis se vuelve manual y lento.

2. **sort sin -n engaña al analista.** Ordenar IPs por número de intentos fallidos sin `-n` puede mostrar una IP con 9 ataques DESPUÉS de una con 100 (alfabéticamente "9" > "1"). Lleva a priorizar mal en triage.

3. **Caracteres invisibles existen.** Auditorías de integridad, validación de contraseñas y análisis de payloads requieren contar bytes reales, no caracteres visibles.

4. **Errores tipográficos te protegen.** Linux falla limpio cuando el archivo no existe. Si un script auditado nunca da error, sospechar — algo puede estar pasando silenciosamente.

5. **Pipes son la diferencia entre principiante y profesional.** Un comando solo responde una pregunta. Tres comandos encadenados responden una pregunta de auditoría real:

       cat /var/log/auth.log | grep "Failed" | wc -l

   Esto responde: "¿cuántos intentos fallidos de login hubo?" — una métrica de seguridad concreta en una sola línea.

---

## Comandos combinados ya disponibles

Con lo aprendido, ya puedes construir análisis como:

    ls | grep "patron" | wc -l           # Contar archivos que coinciden
    grep "texto" archivo.log | sort -u   # Filtrar y mostrar sin duplicados
    cat archivo.txt | sort -n | head     # Top N de un archivo numérico
