# 👥 Análisis de /etc/group — grupos y enumeración de privilegios

> Sesión práctica del 8 de junio de 2026  
> Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Qué es /etc/group

Es el archivo "hermano" de /etc/passwd. Mientras /etc/passwd lista usuarios, /etc/group lista los grupos del sistema. Define qué grupos existen y quiénes son sus miembros.

---

## Qué es un grupo en Linux

Un grupo es un conjunto de usuarios que comparten permisos. En vez de dar acceso a un archivo a 10 personas una por una, se le da acceso al grupo y todos los miembros lo heredan automáticamente.

Analogía con empresa:
- Usuario = empleado individual
- Grupo = departamento o equipo de trabajo

---

## Formato de /etc/group

Cada línea tiene 4 campos separados por dos puntos:

    nombre:contraseña:GID:miembros

Ejemplos reales:

    root:x:0:                       (grupo root, sin miembros adicionales)
    adm:x:4:syslog,patgsj           (grupo adm con dos miembros)
    sudo:x:27:patgsj                (grupo sudo con un solo miembro)

| Campo | Qué es |
|-------|--------|
| 1 | Nombre del grupo |
| 2 | Contraseña (siempre `x`, no se usa) |
| 3 | GID — Group ID (número identificador) |
| 4 | Miembros adicionales del grupo, separados por comas |

---

## UID vs GID — la diferencia fundamental

**UID = User ID** → identifica un usuario individual  
**GID = Group ID** → identifica un grupo (conjunto de usuarios)

Un usuario tiene UN solo UID, pero puede pertenecer a MUCHOS grupos (cada uno con su propio GID).

Ejemplo en /etc/passwd:

    patgsj:x:1000:1000:Patricio:/home/patgsj:/bin/bash
            ↑    ↑
            UID  GID principal

---

## La regla crítica de seguridad: UID 0

> Cualquier cuenta con UID 0 es root, sin importar su nombre.

Linux identifica usuarios por NÚMERO, no por nombre. Si un atacante crea un usuario llamado `backup` con UID 0, ese `backup` es root con poder total.

Por eso una técnica clásica de ataque es crear cuentas con UID 0 disfrazadas con nombres inofensivos. Si la auditoría no revisa los UIDs, la cuenta maliciosa pasa desapercibida.

---

## Comandos prácticos para enumeración de privilegios

**Ver el archivo completo:**

    cat /etc/group

**Ver mis grupos (rápido):**

    groups

**Ver mi identidad numérica completa:**

    id

Resultado típico de `id`:

    uid=1000(patgsj) gid=1000(patgsj) groups=1000(patgsj),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),100(users),114(lpadmin)

Esto muestra UID, GID principal, y todos los grupos secundarios con su número.

**Ver miembros de un grupo específico:**

    grep "sudo" /etc/group

---

## Grupos críticos en Linux

Estos grupos otorgan privilegios elevados. Un usuario común que pertenezca a alguno de estos tiene capacidades administrativas:

| Grupo | Privilegio que otorga |
|-------|----------------------|
| sudo | Ejecutar comandos como root usando `sudo` |
| wheel | Equivalente a sudo en otros sistemas |
| adm | Leer archivos de log del sistema en /var/log/ |
| disk | Acceso directo a discos (lectura/escritura raw) |
| docker | Equivale a root (permite montar cualquier cosa) |
| video | Acceso a dispositivos de video |
| dialout | Acceso a puertos serie |

---

## Por qué importa esto en ciberseguridad

Cuando un atacante consigue una cuenta de usuario común, su primera acción es ejecutar `id` y revisar /etc/group para identificar oportunidades de escalada de privilegios. Una cuenta aparentemente "normal" puede pertenecer al grupo sudo o docker, lo cual le da poder de root.

Esto se llama **enumeración de privilegios** y es uno de los primeros pasos en cualquier intrusión.

---

## Hallazgo típico de auditoría

Escenario real: en un servidor de empresa se ejecuta:

    grep "sudo" /etc/group

Y el resultado es:

    sudo:x:27:admin,juan,maria,backup,test,demo,pedro,ana

Análisis:
- 8 cuentas con poder de root es excesivo
- `test` y `demo` son cuentas de prueba olvidadas con privilegios administrativos
- `backup` siendo miembro de sudo es sospechoso (los servicios no deberían escalar privilegios)

Recomendación: revisar miembros de sudo, eliminar cuentas no justificadas, aplicar principio de mínimo privilegio.

---

## Insights de ciberseguridad

1. **Linux trabaja con números, no con nombres.** Los nombres son cosmética. UID y GID son la identidad real.

2. **UID 0 = root, siempre.** Auditar UIDs es más confiable que auditar nombres.

3. **La pertenencia a grupos es vector de escalada.** Un usuario común en grupo `sudo` o `docker` es root encubierto.

4. **El comando `id` es el primer paso de enumeración.** Cualquier pentester lo ejecuta apenas obtiene acceso a una cuenta.

5. **Principio de mínimo privilegio:** solo dar permisos sudo (u otros grupos críticos) a quien estrictamente lo necesita. Cada miembro extra = una puerta más para tomar control total.

---

## Comandos resumen

    cat /etc/group              # Ver todos los grupos
    groups                      # Ver mis grupos
    id                          # Ver UID/GID completo
    grep "sudo" /etc/group      # Ver miembros del grupo sudo
    grep "patgsj" /etc/group    # Ver a qué grupos pertenezco
