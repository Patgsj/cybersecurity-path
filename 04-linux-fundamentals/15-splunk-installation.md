# Instalación de Splunk Enterprise — Caso de Troubleshooting Real

Sesión práctica de julio de 2026
Lab realizado en Ubuntu 24.04 LTS (VMware Workstation)

---

## Objetivo

Instalar Splunk Enterprise (herramienta SIEM) en la propia VM para practicar análisis de logs y simulacros de trabajo real de SOC. Splunk es una de las herramientas SIEM más solicitadas en ofertas reales de ciberseguridad en Chile y el mundo.

---

## Problema previo: Firefox no abría (Snap corrupto)

Antes de poder descargar Splunk, se detectó que Firefox no abría en la VM.

### Diagnóstico

    firefox
    → internal error: timeout waiting for snap system profiles to get updated

El error indicó un problema con el servicio Snap (sistema de paquetes de Ubuntu, del cual depende Firefox).

    systemctl status snapd.service
    → Active: failed (Result: exit-code)

    journalctl -u snapd.service --no-pager
    → cannot run daemon: cannot read state: unexpected EOF

### Causa raíz encontrada

Revisando el historial completo con journalctl, se identificó que el error comenzó el 28 de mayo de 2026 — el archivo interno de estado de Snap se corrompió, probablemente por un apagado brusco de la VM mientras Snap escribía ese archivo.

### Solución aplicada

    sudo mv /var/lib/snapd/state.json /var/lib/snapd/state.json.dañado
    sudo systemctl restart snapd
    systemctl status snapd
    → Started snapd.service - Snap Daemon (sin errores)

    firefox
    → Abrió correctamente

---

## Instalación de Splunk Enterprise

### Paso 1 — Crear cuenta y descargar

Cuenta gratuita creada en splunk.com. Licencia: 60 días de prueba completa, luego se convierte automáticamente en licencia gratuita perpetua (500 MB/día de indexación).

Descarga mediante wget (más confiable que el navegador para archivos grandes):

    wget -O splunk-10.4.1-5a009d941268-linux-amd64.deb "https://download.splunk.com/products/splunk/releases/10.4.1/linux/splunk-10.4.1-5a009d941268-linux-amd64.deb"

Nota: verificar siempre que el link corresponda a Linux/.deb y no a Windows/.msi antes de descargar — es fácil copiar el link equivocado si hay varias pestañas de sistema operativo abiertas.

### Paso 2 — Instalar el paquete

    sudo dpkg -i splunk-10.4.1-5a009d941268-linux-amd64.deb

Instala Splunk en /opt/splunk. El mensaje final "complete" confirma éxito.

### Paso 3 — Habilitar arranque automático y crear administrador

    sudo /opt/splunk/bin/splunk enable boot-start --accept-license

Solicita usuario y contraseña de administrador de Splunk (independiente del usuario del sistema Linux). La contraseña no se muestra en pantalla mientras se escribe.

---

## Problema: Splunk no arrancaba (permisos)

### Síntoma

    sudo /opt/splunk/bin/splunk start
    → Running Splunk Enterprise as root is deprecated

Splunk rechaza correr como root por buenas prácticas de seguridad (principio de mínimo privilegio).

Al intentar sin sudo:

    /opt/splunk/bin/splunk start --accept-license
    → Múltiples errores "Permission denied" en archivos de configuración y logs

### Causa raíz

La instalación con sudo dpkg dejó todos los archivos de /opt/splunk con dueño root. Al intentar arrancar como usuario normal (patgsj), no había permisos suficientes para escribir logs ni leer configuraciones.

### Solución aplicada

    sudo chown -R patgsj:patgsj /opt/splunk

Cambia el dueño de toda la carpeta Splunk (recursivamente) al usuario patgsj, permitiendo que Splunk corra sin sudo, como es la práctica recomendada.

### Verificación final

    /opt/splunk/bin/splunk start --accept-license
    → Checking prerequisites... Done
    → Starting splunk server daemon (splunkd)...
    → The Splunk web interface is at http://patgsj:8000

---

## Acceso a la interfaz web

URL: http://localhost:8000
Usuario: admin
Contraseña: (configurada durante la instalación)

Login exitoso confirmado: panel "Hello, Administrator" visible con apps disponibles (Search & Reporting, Audit Trail, Data Management, entre otras).

---

## Comando chown — nuevo concepto

    chown -R usuario:grupo /ruta

- chown = change owner (cambiar dueño)
- -R = recursivo, aplica a la carpeta y todo su contenido
- usuario:grupo = nuevo dueño y grupo del archivo/carpeta

Mismo principio que los permisos rwx vistos anteriormente: el dueño de un archivo determina quién puede leerlo, escribirlo o ejecutarlo. chown permite reasignar ese dueño cuando sea necesario.

---

## Por qué importa en ciberseguridad

1. Troubleshooting metódico: el problema de Snap se resolvió sin adivinar — se usó journalctl para revisar el historial completo y encontrar la fecha exacta y causa del fallo, en vez de reinstalar todo a ciegas.

2. Principio de mínimo privilegio en la práctica: Splunk se niega activamente a correr como root, reforzando un concepto ya visto (nunca dar más privilegios de los necesarios a un proceso).

3. Permisos y dueños de archivos son la causa más común de fallos de instalación en Linux: reconocer un error de "Permission denied" y saber resolverlo con chown es una habilidad diaria de sysadmin.

4. Splunk es la herramienta SIEM más solicitada en ofertas reales de ciberseguridad (confirmado en búsqueda de mercado en Chile: NTT DATA, EY y otras piden explícitamente experiencia con SIEM). Tenerlo instalado y funcional es evidencia directa de habilidad práctica para el portfolio.

---

## Comandos resumen

    systemctl status snapd.service          # Diagnosticar servicio fallido
    journalctl -u snapd.service --no-pager   # Ver historial completo de logs
    sudo mv archivo archivo.dañado           # Renombrar archivo corrupto
    wget -O nombre "url"                     # Descargar archivo por terminal
    sudo dpkg -i paquete.deb                 # Instalar paquete .deb
    sudo chown -R usuario:grupo /ruta        # Cambiar dueño recursivamente
    /opt/splunk/bin/splunk start             # Iniciar Splunk (sin sudo)
