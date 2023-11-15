---
title: termuxConfiguracion
---

## Configuración termux
Hemos instalado termux desde f-droid

https://youtu.be/jQ3I2rtASto?si=eE7adKZZYtJFPoEt

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
  