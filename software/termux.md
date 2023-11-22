---
title: termux
---
## termux

Hemos instalado termux desde f-droid

### Configuración termux

[Video explicativo de instalación](https://youtu.be/jQ3I2rtASto?si=eE7adKZZYtJFPoEt)

- pkg update
- pkg install proot-distro
- proot-distro install debian 
- proot-distro login debian
- apt update && upgrade -y
- apt install sudo
- adduser antonio
- nano /etc/sudoers
  - damos los mismo privilegios que root a antonio
- proot-distro login --user antonio debian 
  - Para entrar como antonio en debían
  
Permisos de termux sobre la memoria de Android: https://wiki.termux.com/wiki/Internal_and_external_storage

### Carpeta con todos los permisos
Termux no tiene permisos por ejemplo para modificar ficheros sobre cualquier carpeta android.
Podemos crear un repositorio en la carpeta storage de termux. Para trabajar en esta carpeta con termux, la podremos encontrar en: `/data/data/com.termux/files/home/storage`.
Los ficheros que cuelgan de esta carpeta podrán ser modificados por cualquier usuario que trabajan con termux. Pero las aplicaciones no podrán renombrar los ficheros de este entorno de ficheros de termux.
