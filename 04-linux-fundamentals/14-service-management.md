# Gestión de Servicios — netstat y systemctl

Sesión práctica de julio de 2026
Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## netstat — el "primo mayor" de ss

Comando equivalente a ss, más antiguo y universal, presente en casi todos los sistemas Unix/Linux desde hace décadas. Aparece con frecuencia en documentación y exámenes de certificación.

Comando:

    netstat -tunlp

Mismas opciones que ss: -t (TCP), -u (UDP), -n (números), -l (LISTEN), -p (programa).

Sin sudo, la columna de programa aparece vacía (guión -) porque no se tienen permisos para ver procesos de otros usuarios. Con sudo, se completa con PID y nombre del programa:

    sudo netstat -tunlp

Ejemplo real:

    127.0.0.1:631   LISTEN   1701/cupsd

Esto puede cruzarse con ps para confirmar el hallazgo:

    ps aux | grep 1701

---

## systemctl — controlar servicios del sistema

Comando para revisar, iniciar y detener servicios (daemons) del sistema.

### Ver estado de un servicio

    systemctl status nombre_servicio

Ejemplo: systemctl status cups

Datos clave del resultado:
- Active: active (running) → el servicio está funcionando
- Active: inactive (dead) → el servicio está detenido
- Main PID → identificador del proceso principal del servicio

### Detener un servicio

    sudo systemctl stop nombre_servicio

### Iniciar un servicio

    sudo systemctl start nombre_servicio

---

## Simulación de ticket real — Caso CUPS

Ejercicio práctico simulando un ticket de soporte real, tal como se recibiría en un puesto de analista o soporte técnico.

**Ticket:** verificar el servicio de impresión (CUPS), detenerlo para mantenimiento, y reiniciarlo confirmando que vuelva a funcionar.

Paso 1 — Verificación inicial:

    systemctl status cups
    → Active: active (running), Main PID: 1701

Paso 2 — Detención para mantenimiento:

    sudo systemctl stop cups
    systemctl status cups
    → Active: inactive (dead), "Deactivated successfully"

Paso 3 — Reinicio y verificación final:

    sudo systemctl start cups
    systemctl status cups
    → Active: active (running), Main PID: 3921

---

## Hallazgo importante: el PID cambia tras un reinicio

Al reiniciar un servicio, Linux no reutiliza el PID anterior (1701). Crea un proceso completamente nuevo con un PID distinto (3921), porque el proceso original dejó de existir por completo al detenerse. Es la misma lógica de asignación secuencial vista con la creación de usuarios (UID 1000, 1001, 1002...).

---

## Reporte final del ticket (formato profesional)

Ticket #001 — Verificación y mantenimiento de servicio CUPS

1. Se verificó el estado del servicio: activo y funcionando correctamente (PID 1701).
2. Se detuvo el servicio para mantenimiento. Verificado: desactivación exitosa sin errores.
3. Se reinició el servicio. Verificado: activo nuevamente con PID 3921 (nuevo proceso, comportamiento esperado tras un reinicio).

Estado final: servicio operativo, sin incidentes durante el proceso.

---

## Por qué importa en ciberseguridad

1. systemctl es la herramienta estándar para gestionar servicios en sistemas Linux modernos (basados en systemd). Se usa constantemente en tareas de soporte, sysadmin y respuesta a incidentes.

2. Verificar con evidencia, no asumir: cada acción (detener, iniciar) se confirmó con systemctl status antes de darla por buena. Este hábito es central en el trabajo de un analista.

3. Cruce de fuentes: combinar netstat/ss con ps para confirmar un mismo hallazgo desde dos ángulos distintos es una práctica estándar de verificación en auditorías.

4. Simulación de tickets como método de estudio: practicar con escenarios similares a los de un puesto real (en vez de solo memorizar sintaxis) prepara mejor para el trabajo diario de un analista SOC o soporte técnico.

---

## Comandos resumen

    netstat -tunlp                  # Ver puertos y conexiones (requiere sudo para detalle completo)
    sudo netstat -tunlp              # Igual, con PID y programa visibles
    systemctl status servicio        # Ver estado de un servicio
    sudo systemctl stop servicio     # Detener un servicio
    sudo systemctl start servicio    # Iniciar un servicio
    sudo systemctl restart servicio  # Detener e iniciar en un solo paso (no probado hoy)
