# Guia-practica---autocorregido

Parte A — Backup automatizado de configuración 
Paso 1. Crear el directorio de destino del backup
$ sudo mkdir -p /var/backups/web_config/

Paso 2. Armar el comando de respaldo
$ sudo tar -cjvpf /var/backups/web_config/config_backup$(date +%Y%m%d_%H%M%S).tar.bz2 /etc/hosts /etc/fstab /etc/nginx

Paso 3. Verificar el contenido sin extraer
$ sudo tar -tjvf /var/backups/web_config/config_backup*

Parte B — Disco virtual y montaje seguro para uploads (60 puntos)
Paso 1. Crear el archivo imagen
$ dd if=/dev/zero of=/tmp/archivo_imagen.img bs=1M count=256

Paso 2. Asociarlo a un dispositivo loop
$ sudo losetup -fP /tmp/archivo_imagen.img
$ LOOP_DEV=$(losetup -j /tmp/archivo_imagen.img | cut -d: -f1)
$ echo "Dispositivo asignado: ${LOOP_DEV}

Paso 3. Particionar el dispositivo
$ sudo fdisk ${LOOP_DEV} <<EOF
n
p
1


w
EOF

Paso 4. Formatear la partición
$ sudo mkfs.ext4 -L "UPLOADS_SEC" ${LOOP_DEV}p1

Paso 5. Montar con hardening
$ sudo mkdir -p /mnt/uploads
$ sudo mount -o defaults,noexec,nosuid ${LOOP_DEV}p1 /mnt/ulpoads
$ sudo mount -o defaults,noexec,nodev ${LOOP_DEV}p1 /mnt/ulpoads
$ sudo umount /mnt/uploads
$ sudo mount -o defaults,noexec,nosuid,nodev ${LOOP_DEV}p1 /mnt/ulpoads

Paso 6. Verificar que noexec funciona
$ echo '#!/bin/bash' | sudo tee 
$ echo '#!/bin/bash' | sudo tee /mnt/ulpoads/test.sh
$ echo '#!/bin/bash' | sudo tee /mnt/ulpoads/test.sh
$ echo 'echo"Escrito de prueba"' | sudo tee -a /mnt/ulpoads/test.sh
$ sudo chmod +x /mnt/ulpoads/test.sh
$ /mnt/ulpoads/test.sh

Paso 7. Hacer el montaje persistente
