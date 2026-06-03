# 👥 Análisis de /etc/passwd — los habitantes del sistema

> Sesión práctica del 2 de junio de 2026  
> Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Qué es /etc/passwd

Es un archivo que existe en TODOS los sistemas Linux y Unix del mundo. Funciona como la "guía telefónica" del sistema operativo: lista todos los usuarios que existen.

Pero no solo lista personas. Lista 2 tipos de cuentas:

1. **Cuentas de personas** — humanos que pueden iniciar sesión y trabajar
2. **Cuentas de programas** — procesos internos del sistema que tienen identidad propia

---

## Por qué los programas tienen cuenta

Linux es estricto: todo lo que corre en el sistema debe tener una identidad. Cuando se instala un servicio como un servidor web, Linux automáticamente crea una cuenta para que ese servicio tenga identidad propia y permisos limitados.

Ejemplos en un sistema típico:

| Cuenta | Programa real |
|--------|---------------|
| www-data | Servidor web (Apache, Nginx) |
| mysql | Base de datos MySQL |
| syslog | Sistema de logs |
| dhcpcd | Cliente DHCP que pide IP al router |
| systemd-network | Manejo de red |

Estos NO son personas. No tienen contraseña, no tienen home con archivos personales, no pueden iniciar sesión.

---

## Cómo distinguir el tipo de cuenta

Cada línea de /etc/passwd tiene 7 campos separados por dos puntos. Para auditoría básica, solo importa el ÚLTIMO campo (lo que viene después del último `:`).

Ese último campo se llama "shell" y es la regla de oro:

| Final de la línea | Tipo de cuenta |
|-------------------|----------------|
| /bin/bash | Persona — puede iniciar sesión |
| /bin/sh | Persona — puede iniciar sesión |
| /bin/zsh | Persona — puede iniciar sesión |
| /usr/sbin/nologin | Programa — NO puede iniciar sesión |
| /bin/false | Programa — NO puede iniciar sesión |

Esa es la única información necesaria para una auditoría rápida de cuentas.

---

## Ejemplo de lectura

Línea de root:

    root:x:0:0:root:/root:/bin/bash

Termina en `/bin/bash` → es persona, puede iniciar sesión.

Línea de daemon:

    daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin

Termina en `/usr/sbin/nologin` → es programa, NO puede iniciar sesión.

Línea de mysql:

    mysql:x:106:113:MySQL Server,,,:/nonexistent:/bin/false

Termina en `/bin/false` → es programa, NO puede iniciar sesión.

---

## Comandos prácticos para auditoría

**Ver el archivo completo:**

    cat /etc/passwd

**Listar SOLO las cuentas que pueden iniciar sesión:**

    grep "/bin/bash" /etc/passwd

**Contar cuántas son:**

    grep "/bin/bash" /etc/passwd | wc -l

**Filtrar excluyendo las que NO pueden loguearse:**

    grep -v "nologin" /etc/passwd | grep -v "false"

(Nota: este último filtro puede dejar pasar casos especiales como /bin/sync. Es un ejemplo de "falso positivo" en seguridad.)

---

## Por qué importa esto en ciberseguridad

Cuando un atacante consigue acceso a un sistema, una de las primeras cosas que hace es leer /etc/passwd para identificar cuentas atacables. NO le sirven las cuentas de programa (no se pueden loguear). Solo le interesan las personas, especialmente:

- root (acceso total)
- Cuentas administrativas
- Cuentas con nombres tipo "test", "backup", "admin", "demo" (suelen tener contraseñas débiles olvidadas)

Por eso uno de los principios básicos de hardening de Linux es: deshabilitar el login de cualquier cuenta que no lo necesite. Cada cuenta extra con shell de login = una puerta más para forzar.

---

## Escenario de auditoría real

Caso típico que se reporta como hallazgo:

> Un servidor tiene 20 cuentas con /bin/bash, pero la empresa solo tiene 8 personas autorizadas. Existen 12 cuentas no identificadas.

Posibles explicaciones:
- Ex-empleados con cuentas no eliminadas
- Cuentas de testing olvidadas (test, dev, qa)
- Cuentas creadas por proveedores externos no removidas
- Cuentas de servicios mal configurados con shell de login
- Cuentas compartidas tipo "admin1", "admin2" sin control

Cada cuenta abandonada con contraseña débil es una vía de entrada para un atacante.

---

## Insights de ciberseguridad

1. **Lectura rápida de /etc/passwd es enumeración básica de objetivos.** Cualquier pentester o analista lo hace en los primeros minutos de un análisis.

2. **El archivo /etc/passwd es PÚBLICO.** Cualquier usuario del sistema puede leerlo. Las contraseñas reales están en /etc/shadow, que solo root puede leer.

3. **Cuentas con shell de login deben justificarse.** Si el número de cuentas con /bin/bash es mayor al de personas autorizadas, hay un hallazgo de auditoría.

4. **Los filtros simples pueden generar falsos positivos.** Excluir "nologin" y "false" puede dejar pasar casos especiales como /bin/sync. La auditoría requiere criterio, no solo comandos.

5. **El hardening básico recomienda:** revisar /etc/passwd periódicamente, deshabilitar el login de cuentas inactivas, eliminar cuentas obsoletas, no usar cuentas compartidas.

---

## Comandos combinados aprendidos

    grep "/bin/bash" /etc/passwd                    # Listar cuentas con login
    grep "/bin/bash" /etc/passwd | wc -l            # Contarlas
    grep -v "nologin" /etc/passwd | grep -v "false" # Filtrar las que pueden loguearse
