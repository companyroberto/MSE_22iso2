# MSE_22iso2
Implementación de Sistemas Operativos (II)

### Entregables:

- Presentación del Trabajo Práctico.
<br />https://github.com/companyroberto/MSE_22iso2/blob/master/Documentacion/Presentaci%C3%B3n%20Trabajo%20Final.pdf

- Contenido de la memoria MicroSD.
<br />https://github.com/companyroberto/MSE_22iso2/tree/master/MicroSD


### Notas:

Configuración en las particiones de la SD:
<br />Utilizando una SD de 16Gb, crear tres particiones primarias:
- 100MB Fat16
- ~8Gb Ext4 (para un FS mínimo de primeras clases con busybox)
- ~8Gb Ext4 (para otro FS, con buildroot)

En la partición en FAT copie los archivos:
- zImage (hice 2 distintos para hacer demostraciones)
- DTB
- RAMDisk

En cada partición Ext4 copie el FS que indique arriba.
 

Una vez que el U-Boot esté funcionando y teniendo la SD configurada con los archivos copiados se puede probar esto:
- Desde el shell de U-Boot ejecutar "fatls mmc 0:1" y se deberían ver los archivos de la particion FAT.
- Para ver el contenido de las particiones EXT4 es "ext4ls mmc 0:1" y "ext4ls mmc 0:2", pero por algún motivo después de copiarle el contenido no me funciona y dice que está corrupto. Antes de copiar me mostraba que estaban en blanco con la carpeta lost.


Para bootear por TFTP en la materia anterior hacíamos esto:
- setenv ipaddr 192.168.1.100 (en mi caso)
- setenv serverip 192.168.1.21  (en mi caso)
- setenv bootargs root=/dev/nfs rw ip=192.168.1.100 console=ttyS0,115200 nfsroot=192.168.1.21:/home/tito/linux-kernel-labs/modules/nfsroot/
- tftp 0x42000000 zImage
- tftp 0x43000000 sun8i-h2-plus-orangepi-zero.dtb
- bootz 0x42000000 - 0x43000000

Para bootear desde la SD ahora es así:
- setenv bootargs root=/dev/mmcblk0p2 rootwait rw console=ttyS0,115200 ( para la primer partición FS es  root=/dev/mmcblk0p2  y para la segunda es  root=/dev/mmcblk0p3  )
- fatload mmc 0:1 0x42000000 zImage
- fatload mmc 0:1 0x43000000 sun8i-h2-plus-orangepi-zero.dtb
- bootz 0x42000000 - 0x43000000

Para bootear desde la SD con RAMDisk es así:
- setenv bootargs console=ttyS0,115200
- fatload mmc 0:1 0x42000000 zImage_5.2.0
- fatload mmc 0:1 0x43000000 sun8i-h2-plus-orangepi-zero.dtb
- fatload mmc 0:1 0x43300000 rootfs.cpio.uboot
- bootz 0x42000000 0x43300000 0x43000000
