# Análisis de Logs con Splunk — Primera Búsqueda SIEM

Proyecto integrador de análisis de logs. Ejercicio realizado sobre un sistema Ubuntu 24.04 LTS personal, simulando el flujo de trabajo diario de un analista SOC junior: ingestar logs de autenticación en un SIEM real y ejecutar búsquedas para detectar eventos de seguridad.

Fecha: julio 2026

---

## 1. Contexto y objetivo

- Sistema auditado: VM Ubuntu 24.04 LTS
- Herramienta: Splunk Enterprise 10.4.1
- Archivo de origen: `/var/log/auth.log`
- Hostname: patgsj

Antes de tocar Splunk, se practicó la lectura y filtrado de logs directamente en la terminal, usando el archivo nativo de autenticación de Ubuntu. Esto permitió entender qué hace Splunk por debajo antes de automatizarlo con la herramienta.

**Observación:** la lógica seguida en todo el proceso fue: hay un archivo de logs → se lee manualmente → se filtra con `grep` → se automatiza todo eso dentro de un SIEM (Splunk).

---

## 2. Fase Manual — Lectura y Filtrado de Logs en Terminal

### 2.1 Leer el archivo de logs directamente

```bash
sudo tail -20 /var/log/auth.log
```

Cada línea de este archivo sigue una estructura fija, que es la base para interpretar cualquier log:

| Parte | Ejemplo | Significado |
|---|---|---|
| Cuándo | `2026-07-13T14:22:32` | Fecha y hora del evento |
| Dónde | `patgsj` | Nombre de la máquina |
| Quién | `sudo:` seguido del usuario | Programa que reporta + usuario que actuó |
| Qué | `COMMAND=/usr/bin/tail ...` | Acción concreta ejecutada |

### 2.2 Filtrar eventos específicos con `grep`

Buscar únicamente los usos de `sudo` (solicitudes de privilegios de administrador):

```bash
sudo grep "sudo" /var/log/auth.log | tail -10
```

Buscar fallos de autenticación (intentos de login con credenciales incorrectas):

```bash
sudo grep "authentication failure" /var/log/auth.log
```

**Resultado:** se detectaron 11 fallos reales de autenticación — 10 en el login gráfico (`gdm-password`) y 1 al usar `sudo` en terminal.

**Observación:** un fallo repetido en `sudo` es más grave que uno en pantalla de login, porque sugiere un intento de escalar privilegios ya estando dentro del sistema.

### 2.3 Excluir falsos positivos con `grep -v`

Al ejecutar la búsqueda anterior, el propio comando de búsqueda quedó registrado en el log (porque contenía las palabras "authentication failure" en su texto), generando un falso positivo: una línea que aparece en los resultados sin ser un evento real de seguridad.

Solución — excluir las líneas que contienen la palabra `COMMAND`:

```bash
sudo grep "authentication failure" /var/log/auth.log | grep -v "COMMAND"
```

**Observación:** distinguir señal de ruido es una habilidad central de un analista SOC. Un filtro mal ajustado puede inflar el número real de incidentes reportados.

---

## 3. Instalación y Puesta en Marcha de Splunk

### 3.1 Problema: Splunk no arranca tras reiniciar la VM

```bash
sudo /opt/splunk/bin/splunk status
```

Resultado: `splunkd was not running.`

### 3.2 Error común: ejecutar Splunk con `sudo`

```bash
sudo /opt/splunk/bin/splunk start
```

Resultado: `Running Splunk Enterprise as root is deprecated...`

Splunk está configurado para correr con el usuario propietario de la carpeta (`patgsj`), no con `root`. Solución: ejecutar sin `sudo`:

```bash
/opt/splunk/bin/splunk start
```

### 3.3 Problema: contraseña de administrador olvidada

Solución sin perder datos:

```bash
/opt/splunk/bin/splunk stop
mv /opt/splunk/etc/passwd /opt/splunk/etc/passwd.bak
sudo nano /opt/splunk/etc/system/local/user-seed.conf
```

Contenido del archivo `user-seed.conf`:

```
[user_info]
USERNAME = admin
PASSWORD = TuClaveNueva123
```

Guardar (`Ctrl+O`, Enter), salir (`Ctrl+X`), luego iniciar Splunk:

```bash
/opt/splunk/bin/splunk start
```

---

## 4. Ingesta del Archivo de Logs en Splunk

Pasos seguidos desde la interfaz web (`http://localhost:8000`):

1. Settings → Add Data → Monitor → Files & Directories
2. Ruta del archivo: `/var/log/auth.log`
3. Source Type: `linux_secure`
4. Index: se creó un índice nuevo, `linux_logs`
5. Continuously Monitor: Yes

| Campo | Valor |
|---|---|
| Input Type | File Monitor |
| Source Path | `/var/log/auth.log` |
| Source Type | `linux_secure` |
| Index | `linux_logs` |
| Host | `patgsj` |

---

## 5. Primera Búsqueda SPL

```spl
index=linux_logs "authentication failure"
```

**Observación:** Splunk filtra por defecto solo las últimas 24 horas (o incluso "real-time"). Para ver eventos históricos completos, el rango de tiempo debe ajustarse a "All time".

**Resultado:** 4 eventos encontrados, todos fallos de autenticación en login gráfico (`gdm-password`).

![Primera búsqueda en Splunk mostrando 4 eventos de authentication failure](imagenes/splunk-primera-busqueda.png)

---

## 6. Comparación — Terminal Manual vs. Splunk

| Tarea | Método manual | Método en Splunk |
|---|---|---|
| Leer el archivo | `tail -20 /var/log/auth.log` | Ingesta continua vía Monitor |
| Buscar un término | `grep "authentication failure"` | `index=linux_logs "authentication failure"` |
| Excluir ruido | `grep -v "COMMAND"` | Ajustar la búsqueda SPL |
| Interpretar campos | Manual, leyendo cada línea | Automático (`date_hour`, `user`, `process`, etc.) |

**Observación:** Splunk automatiza y visualiza exactamente el mismo proceso de análisis que se puede hacer a mano en la terminal — la diferencia está en la escala y la velocidad de respuesta, no en la lógica subyacente.

---

## 7. Próximos Pasos

- Practicar más búsquedas SPL variando términos y campos
- Aprender comandos SPL de análisis: `stats`, `table`, `sort`, `timechart`
- Construir un dashboard básico con los fallos de autenticación detectados
- Explorar el asistente de IA integrado en Splunk para generar búsquedas SPL más complejas
