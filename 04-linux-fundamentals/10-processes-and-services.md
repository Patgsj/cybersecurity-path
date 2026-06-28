# ⚙️ Procesos en Linux — ps y top

> Sesiones prácticas del 27 y 28 de junio de 2026  
> Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Qué es un proceso

Un proceso es un programa en ejecución. Todo lo que corre en Linux es un proceso:
- El sistema operativo (kernel)
- Los servicios del sistema (servidor web, base de datos, logs)
- Las aplicaciones del usuario (navegador, terminal, editor)

Cuando se abre un programa, se crea un proceso. Cuando se cierra, el proceso muere. El sistema está siempre vivo, con procesos naciendo y muriendo constantemente.

---

## Qué es el kernel

El kernel es el corazón de Linux. Es el programa más importante del sistema. Actúa como traductor entre los programas y el hardware.

Ningún programa habla directamente con el CPU, la memoria, el disco o los dispositivos. Todo pasa por el kernel.

Las 4 funciones principales del kernel:
1. Controla el CPU (decide qué proceso usa el procesador)
2. Controla la memoria RAM (la reparte entre los programas)
3. Controla el disco (guarda y lee archivos)
4. Controla los dispositivos (teclado, mouse, pantalla, red, USB)

Los procesos del kernel aparecen entre corchetes en la salida de `ps`, por ejemplo `[kworker]`, `[ksoftirqd]`. Son tareas internas, no aplicaciones.

---

## PID — Process ID

Cada proceso tiene un identificador numérico único llamado PID. Es la misma idea que UID (usuario) y GID (grupo):

| Identificador | Para qué |
|---------------|----------|
| UID | Identifica un usuario |
| GID | Identifica un grupo |
| PID | Identifica un proceso |

Linux usa números únicos para identificar todo en el sistema.

---

## Comando ps — foto fija de procesos

### ps (sin opciones)
Muestra solo los procesos de la terminal actual.

    ps

Resultado típico:

    PID TTY          TIME CMD
    3439 pts/0    00:00:00 bash
    3479 pts/0    00:00:00 ps

### ps aux — todos los procesos del sistema
Sin guión antes de `aux`. Muestra TODOS los procesos.

    ps aux

Columnas importantes:
- USER → quién ejecuta el proceso
- PID → identificador único
- %CPU → consumo de CPU
- %MEM → consumo de memoria
- COMMAND → nombre del programa

---

## Lectura de ps aux por grupos

Una salida típica tiene 3 grandes grupos según el USER:

### Grupo 1 — Procesos de root
Sistema operativo y kernel. Muchos aparecen entre corchetes `[kthreadd]`, `[kworker/...]`. Son normales y esperados.

Ejemplos importantes:
- `/sbin/init splash` (PID 1) → padre de todos los procesos
- `systemd-journald` → logs del sistema
- `NetworkManager` → gestor de red
- `gdm3` → gestor gráfico de login

### Grupo 2 — Procesos de servicios con usuarios dedicados
Servicios del sistema corriendo con cuentas aisladas:

| Usuario | Servicio |
|---------|----------|
| systemd+ | systemd-resolved, systemd-timesyncd |
| avahi | avahi-daemon (descubrimiento de red) |
| syslog | rsyslogd (sistema de logs) |
| colord | gestión de color |
| kernoops | reporte de errores del kernel |

Esto es buena práctica de seguridad: si un servicio es comprometido, solo afecta a su usuario, no a todo el sistema.

### Grupo 3 — Procesos del usuario humano
Aplicaciones del entorno gráfico y herramientas:
- gnome-shell → escritorio Ubuntu
- gnome-terminal-server → ventana de terminal
- bash → shell de comandos
- Firefox, editor de código, etc.

---

## Filtrado con grep

Una salida de 300+ líneas no se lee a mano. Se filtra con grep usando pipes.

### Filtrar por usuario

    ps aux | grep patgsj

### Filtrar por programa

    ps aux | grep firefox
    ps aux | grep bash

### Eliminar el grep de los resultados

El propio comando `grep` siempre aparece en los resultados. Para excluirlo:

    ps aux | grep bash | grep -v grep

Lectura:
1. ps aux → lista todos los procesos
2. grep bash → filtra solo los que contengan "bash"
3. grep -v grep → de esos, excluye los que contengan "grep"

Resultado: salida limpia sin ruido.

---

## Comando top — procesos en tiempo real

`top` muestra los procesos actualizándose en vivo cada segundo. Es la diferencia entre una foto fija (`ps`) y un video en directo (`top`).

    top

### Cabecera de top

    top - 12:35:33 up 9 min, 1 user, load average: 0.10, 0.38, 0.37
    Tasks: 301 total, 2 running, 299 sleeping, 0 stopped, 0 zombie
    %Cpu(s): 0.2 us, 0.2 sy, 0.0 ni, 99.7 id, ...
    MiB Mem: 3867.9 total, 1214.6 free, 1223.2 used, 1699.3 buff/cache
    MiB Swap: 3867.0 total, 0.0 used

Lectura de cada línea:

**Línea 1:** hora, tiempo desde el último reinicio (`up`), usuarios logueados

**Línea 2:** resumen de procesos
- total → cantidad total de procesos
- running → procesos activos
- sleeping → procesos esperando
- zombie → procesos muertos sin limpiar (BANDERA ROJA si hay muchos)

**Línea 3:** uso de CPU
- `id` (idle) → porcentaje ocioso. Si es alto, sistema relajado.

**Línea 4:** memoria RAM
- total → RAM total
- used → en uso
- free → libre
- buff/cache → caché del sistema

**Línea 5:** swap (memoria de respaldo en disco)
- Si `used` es 0 → la RAM es suficiente. Bueno.
- Si `used` es alto → falta RAM. Mal.

---

## Procesos zombie — concepto de seguridad

Un proceso zombie es un proceso que terminó pero no se limpió completamente del sistema. Está "muerto pero presente".

Pocos zombies (1-2) → normal en sistemas activos.
Muchos zombies (10+) → bandera roja. Puede indicar:
- Programas mal escritos (memory leak)
- Posible ataque de denegación de servicio
- Procesos forzados a terminar mal

---

## Teclas interactivas de top

Una vez dentro de top, estas teclas controlan la vista:

| Tecla | Acción |
|-------|--------|
| M | Ordenar por memoria (RAM) |
| P | Ordenar por CPU (procesador) |
| q | Salir de top |
| Esc | Cancelar pregunta o acción |

Truco de memoria:
- M de Memory
- P de Processor

---

## Aplicaciones reales en ciberseguridad

`top` y `ps` son los primeros comandos que ejecuta un analista cuando recibe un caso. Permiten identificar en segundos:

### 1. Diagnóstico de lentitud
"El servidor está lento" → top muestra qué proceso consume más CPU/RAM.

### 2. Detección de procesos sospechosos
Procesos desconocidos con alto %CPU pueden indicar:
- Mineros de criptomonedas (cryptomining malware)
- Procesos maliciosos del atacante
- Programas comprometidos

### 3. Búsqueda de herramientas de atacantes
Filtrados típicos en una investigación:

    ps aux | grep nc          # Netcat - herramienta clásica
    ps aux | grep curl        # Descargas desde la red
    ps aux | grep python      # Scripts maliciosos
    ps aux | grep miner       # Cryptominers

### 4. Detección de zombies acumulados
Indica problemas en programas o posibles ataques.

### 5. Verificación de servicios esperados
Confirmar que servicios críticos estén corriendo (firewalls, antivirus, logging).

---

## Diferencias clave entre ps y top

| Característica | ps aux | top |
|----------------|--------|-----|
| Tipo de vista | Foto fija | Tiempo real |
| Actualización | No | Cada segundo |
| Interactivo | No | Sí (teclas) |
| Ordenamiento | Por PID | Por CPU (default) |
| Uso típico | Búsqueda específica con grep | Diagnóstico general en vivo |

---

## Insights de ciberseguridad

1. **Cada proceso tiene una identidad.** El USER ejecutando el proceso es información crítica para auditoría.

2. **La separación por usuarios es seguridad.** Servicios corriendo como `www-data`, `mysql`, `syslog` en vez de root limitan el daño de una intrusión.

3. **Los procesos hablan en tiempo real.** `top` muestra lo que está pasando AHORA, no lo que pasó.

4. **Filtros son esenciales.** Un sistema con 500 procesos no se lee a mano. `ps aux | grep` es enumeración real.

5. **Patrones anómalos saltan a la vista.** Procesos desconocidos, alto consumo sin razón, zombies acumulados, todo se ve en segundos con estos comandos.

---

## Comandos resumen

    ps                              # Procesos de mi terminal
    ps aux                          # Todos los procesos del sistema
    ps aux | grep [nombre]          # Filtrar por nombre o usuario
    ps aux | grep [x] | grep -v grep   # Filtrar sin el ruido del grep
    top                             # Vista en tiempo real
    top -> M                        # Ordenar por memoria
    top -> P                        # Ordenar por CPU
    top -> q                        # Salir
