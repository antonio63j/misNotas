---
title: ssh
tag: linux
autor: antonio
---

<h1 align="center"><img src="https://placekitten.com/300/150"/></h1>

[TOC]
 
## Crear el par de claves

El primer paso es crear un par de claves en la máquina cliente (por lo general, su computado

    1. ssh-keygen
  
Por defecto, las versiones de `ssh-keygen` crearán un par de claves RSA de 3072 bit, que es lo suficientemente seguro para la mayoría de casos de uso (opcionalmente puede pasar el indicador `-b 4096` para crear una clave de 4096 bit más grande).

 
Debería ver el siguiente mensaje:

    Enter passphrase (empty for no passphrase):
 
 Sí añadimos passphrase, tendremos que añadirla con cada inicio de sesión.
 
 El siguiente paso es ubicar la clave pública en su servidor a fin de poder usar la autenticación basada en claves de SSH para iniciar sesión.

## Copiar la clave pública al servidor

 

### `ssh-copy-id`

La sintaxis es la siguiente:

    `ssh-copy-id username@remote_host`
    
Es posible que vea el siguiente mensaje:


             
             Output
             
             
    The authenticity of host '203.0.113.1 (203.0.113.1)' can't be established.
    ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
    Are you sure you want to continue connecting (yes/no)? yes
Esto significa que su computadora local no reconoce el host remoto. Esto sucederá la primera vez que establezca conexión con un nuevo host. Escriba “yes” y presione `ENTER` para continuar.

A continuación, la utilidad analizará su cuenta local en busca de la clave `id_rsa.pub` que creamos antes. Cuando la encuentre, le solicitará la contraseña de la cuenta del usuario remoto:


             
             Output
             
             
    /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
    /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
    username@203.0.113.1's password:
Escriba la contraseña (por motivos de seguridad, no se mostrará lo que escriba) y pulse `ENTER`. La utilidad se conectará a la cuenta en el host remoto usando la contraseña que proporcionó. Luego, copia el contenido de su clave `~/.ssh/id_rsa.pub` a un archivo en el directorio principal de la cuenta remota `~/.ssh`, llamado `authorized_keys`.

Debería ver el siguiente resultado:


             
             Output
             
             
    Number of key(s) added: 1

    Now try logging into the machine, with:   "ssh 'username@203.0.113.1'"
    and check to make sure that only the key(s) you wanted were added.
En este punto, su clave `id_rsa.pub` se habrá cargado en la cuenta remota. |Puede continuar con el [paso 3](#step-3-%E2%80%94-authenticating-to-your-ubuntu-server-using-ssh-keys).

### Copiar clave pública usando SSH

Si no tiene `ssh-copy-id` disponible, pero tiene acceso de SSH basado en contraseña a una cuenta de su servidor, puede cargar sus claves usando un método de SSH convencional.

Podemos hacer esto usando el comando `cat` para leer el contenido de la clave de SSH pública en nuestra computadora local y canalizando esto a través de una conexión SSH al servidor remoto.

Por otra parte, podemos asegurarnos de que el directorio `~/.ssh` exista y tenga los permisos correctos conforme a la cuenta que usamos.

Luego podemos transformar el contenido que canalizamos a un archivo llamado `authorized_keys` dentro de este directorio. Usaremos el símbolo de redireccionamiento `>>` para anexar el contenido en lugar de sobrescribirlo. Esto nos permitirá agregar claves sin eliminar claves previamente agregadas.

El comando completo tiene este aspecto:

    1. cat ~/.ssh/id_rsa.pub | ssh username@remote_host "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh && cat >> ~/.ssh/authorized_keys"

Es posible que vea el siguiente mensaje:
             
             Output
             
    The authenticity of host '203.0.113.1 (203.0.113.1)' can't be established.
    ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
    Are you sure you want to continue connecting (yes/no)? yes
Esto significa que su computadora local no reconoce el host remoto. Esta pasará la primera vez que establezca conexión con un nuevo host. Escriba `yes` y presione `ENTER` para continuar.

Posteriormente, deberá recibir la solicitud de introducir la contraseña de la cuenta de usuario remota:

    username@203.0.113.1's password:
Una vez que ingrese su contraseña, el contenido de su clave `id_rsa.pub` se copiará al final del archivo `authorized_keys` de la cuenta del usuario remoto.

### Copiar la clave pública de forma manual

Si no tiene disponibilidad de acceso de SSH basado en contraseña a su servidor, deberá completar el proceso anterior de forma manual.

Habilitaremos el contenido de su archivo `id_rsa.pub` para el archivo `~/.ssh/authorized_keys` en su máquina remota.

Para mostrar el contenido de su clave `id_rsa.pub`, escriba esto en su computadora local:

    `cat ~/.ssh/id_rsa.pub`
    
Verá el contenido de la clave, que debería tener un aspecto similar a este:

             Output
             
    ssh-rsa| AAAAB3NzaC1yc2EAAAADAQABAAACAQCqql6MzstZYh1TmWWv11q5O3pISj2ZFl9HgH1JLknLLx44+tXfJ7mIrKNxOOwxIxvcBF8PXSYvobFYEZjGIVCEAjrUzLiIxbyCoxVyle7Q+bqgZ8SeeM8wzytsY+dVGcBxF6N4JS+zVk5eMcV385gG3Y6ON3EG112n6d+SMXY0OEBIcO6x+PnUSGHrSgpBgX7Ks1r7xqFa7heJLLt2wWwkARptX7udSq05paBhcpB0pHtA1Rfz3K2B+ZVIpSDfki9UVKzT8JUmwW6NNzSgxUfQHGwnW7kj4jp4AT0VZk3ADw497M2G/12N0PPB5CnhHf7ovgy6nL1ikrygTKRFmNZISvAcywB9GVqNAVE+ZHDSCuURNsAInVzgYo9xgJDW8wUw2o8U77+xiFxgI5QSZX3Iq7YLMgeksaO4rBJEa54k8m5wEiEE1nUhLuJ0X/vh2xPff6SQ1BL/zkOhvJCACK6Vb15mDOeCSq54Cr7kvS46itMosi/uS66+PujOO+xt/2FWYepz6ZlN70bRly57Q06J+ZJoc9FfBCbCyYH7U/ASsmY095ywPsBo1XQ9PqhnN1/YOorJ068foQDNVpm146mUpILVxmq41Cj55YKHEazXGsdBIbXWhcrRf4G2fJLRcGUr9q8/lERo9oxRm5JFX6TCmj6kmiFqv+Ow9gI0x8GvaQ== demo@test
Acceda a su host remoto usando el método que esté a su disposición.

Una vez que tenga acceso a su cuenta en el servidor remoto, debe asegurarse de que exista el directorio `~/.ssh`. Con este comando se creará el directorio, si es necesario. Si este último ya existe, no se creará:

    `mkdir -p ~/.ssh`

Ahora, podrá crear o modificar el archivo `authorized_keys` dentro de este directorio. Puede agregar el contenido de su archivo `id_rsa.pub` al final del archivo `authorized_keys` y, si es necesario, crear este último con el siguiente comando:

    `echo public_key_string >> ~/.ssh/authorized_keys|`
  
En el comando anterior, reemplace public_key_string por el resultado del comando `cat ~/.ssh/id_rsa.pub` que ejecutó en su sistema local. Debería iniciar con `ssh-rsa AAAA...`.

Por último, verificaremos que el directorio `~/.ssh` y el archivo `authorized_keys` tengan el conjunto de permisos apropiados:

   ` chmod -R go= ~/.ssh`

Con esto, se eliminan de forma recursiva todos los permisos “grupo” y “otros” del directorio `~/.ssh/`.

Si está usando la cuenta **root** para configurar claves para una cuenta de usuario, también es importante que el directorio `~/.ssh` pertenezca al usuario y no sea **root**:

  `chown -R sammy:sammy ~/.ssh`

En este tutorial, nuestro usuario recibe el nombre sammy, pero debe sustituir el nombre de usuario que corresponda en el comando anterior.

Ahora podemos intentar la autenticación sin contraseña con nuestro servidor de Ubuntu.

## Autenticación en el servidor de Ubuntu con claves de SSH

Si completó con éxito uno de los procedimientos anteriores, debería poder iniciar sesión en el host remoto *sin* la contraseña de la cuenta remota.

El proceso básico es el mismo:

    `ssh username@remote_host`

Si es la primera vez que establece conexión con este host (si empleó el último método anterior), es posible que vea algo como esto:

             Output
             
    The authenticity of host '203.0.113.1 (203.0.113.1)' can't be established.
    ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
    Are you sure you want to continue connecting (yes/no)? yes
Esto significa que su computadora local no reconoce el host remoto. Escriba “yes” y presione `ENTER` para continuar.

Si no proporcionó una frase de contraseña para su clave privada, se iniciará sesión de inmediato. Si proporcionó una frase de contraseña para la clave privada al crearla, se solicitará que la introduzca ahora (tenga en cuenta que, por motivos de seguridad, las pulsaciones de teclas no se mostrarán en la sesión de terminal). Después de la autenticación, se debería abrir una nueva sesión de shell con la cuenta configurada en el servidor de Ubuntu.

Si la autenticación basada en claves se realizó con éxito, puede aprender a proteger más su sistema inhabilitando la autenticación con contraseña.

##  Inhabilitar la autenticación con contraseña en su servidor

Si pudo iniciar sesión en su cuenta usando SSH sin una contraseña, habrá configurado con éxito la autenticación basada en claves de SSH para su cuenta. Sin embargo, su mecanismo de autenticación basado en contraseña sigue activo. Esto significa que su servidor sigue expuesto a ataques de fuerza bruta.

Antes de completar los pasos de esta sección, asegúrese de tener configurada la autenticación basada en claves de SSH para la cuenta **root** en este servidor o, preferentemente, la autenticación basada en clave de SSH para una cuenta no root en este servidor con privilegios `sudo`. Con este paso, se bloquearán los registros basados en contraseñas. Por lo tanto, es fundamental que se asegure de seguir teniendo acceso administrativo.

Una vez que haya confirmado que su cuenta remota tiene privilegios administrativos, inicie sesión en su servidor remoto con claves de SSH, ya sea como **root** o con una cuenta con privilegios `sudo`. Luego, abra el archivo de configuración del demonio de SSH:

 `sudo nano /etc/ssh/sshd_config`

Dentro del archivo, busque una directiva llamada `PasswordAuthentication`. Esta línea puede ser eliminada con una `#` al principio de la línea. Elimine la línea quitando `#`, y establezca el valor a `no`. Esto deshabilitará su capacidad de iniciar sesión vía SSH usando las contraseñas de la cuenta:

/etc/ssh/sshd_config

    . . .
    PasswordAuthentication no
    . . .
Guarde y cierre el archivo cuando haya terminado pulsando `CTRL + X`, luego `Y` para confirmar que desea guardar el archivo y, por último, `ENTER` para cerrar nano. Para activar realmente estos cambios, debemos reiniciar el servicio `sshd`:

 `sudo systemctl restart ssh`

Como medida de precaución, abra una nueva ventana de terminal y compruebe que el servicio de SSH funcione correctamente antes de cerrar su sesión actual:

 `ssh username@remote_host`

Una vez que haya verificado que su servicio de SSH está funcionando correctamente, podrá cerrar de forma segura todas las sesiones de los servidores actuales.

El demonio SSH de su servidor de Ubuntu ahora solo responderá a claves de SSH. Los inicios de sesión con contraseña han sido deshabilitados.

