[TOC]

# Ubuntu 22.04 (ionos)

- instalar openjdk8

- instalar postgresql (version 14.9)

  Para permitir conexiones exteriores o no locales, se han modificados estos fichero de configuración de la carpeta /etc/postgresql/14/main/: pg_hba.conf y postgresql.conf.

  sudo apt install postgresql postgresql-contrib

- snap ya viene instalado, certbot (con snap), dovecot, postfix,  opendKim,

- nvm, node (con nvm):

```hhh
  curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
```

Instalamos la última versión LTS de node con nvm (node 20.10)

# Certificados

[postfix web](https://www.postfix.org)

[certbot web](https://certbot.eff.org/)

[letsencrypt web](https://letsencrypt.org/es)

[crear certificados para nginx](https://nolifelover.medium.com/create-a-reverse-proxy-for-your-application-using-nginx-and-certbot-25fe971682c6)

## Certificados wildcard para web de letsencrypt

  Instalamos con apt snap certbot, en ubuntu 20.04 ya viene instalado openssl y git

```
`sudo snap install certbot --classic`
```

la instalacion de certbot no será visible con

```
sudo apt list --installed
```

para ver lo instaldo con snap, tendremos que hacer

```
snap list
```

a fecha 30/05/2023:

```
antonio@fernandezlucena:~$ snap list
Name     Version   Rev    Tracking       Publisher     Notes
certbot  2.6.0     3024   latest/stable  certbot-eff✓  classic
core20   20230503  1891   latest/stable  canonical✓    base
snapd    2.59.2    19122  latest/stable  canonical✓    snapd`
```

Crear certificado wildcard, hay un script para ello en /home/antonio:

```
certbot certonly \
          --server https://acme-v02.api.letsencrypt.org/directory \
          --manual \
          --email info@fernandezlucena.es \
          --preferred-challenges dns \
           -d *.fernandezlucena.es
```

 Ejecutar el script con sudo. Este script se detiene hasta que damos "enter",  nos ofrece una valor que deberemos colocar en el registro _acme-challenge del dominio (hostinet), y una vez que hayamos comprovado que se ha desplegado en la red, pulsamos "enter", a continuación se nos ofrecerá dónde se ha colocado el certificados y fecha de validez.

Podemos comprovar que el registro _acme-callenge se ha desplegado con el comando:

```
host -t txt _acme-challenge.fernandezlucena.es     
```

También se puede comprobar la propagación [aquí](https://www.whatsmydns.net/#CNAME/aflcv.fernandezlucena.es)

Si ya tenemos generado algún certificado, numera los directorios donde se colocan los certificados. El script de arriba, genera los certificados en  /etc/letsencrypt/live/fernandezlucena.es-0001/.

Para renobar los certificados sería:

```
sudo certbot renew (última renovacion, que ha dejado los certificados en /etc/letsencrypt/live/fernandezlucena.es-0001/)
```

Crear certificado .p12 para la aplicación spring boot.

```
openssl pkcs12 -export -in /etc/letsencrypt/live/fernandezlucena.es-0001/fullchain.pem \
               -inkey /etc/letsencrypt/live/fernandezlucena.es-0001/privkey.pem \
               -out ./www/aflcv-back/keystore.p12 \
               -name tomcat \
               -CAfile /etc/letsencrypt/live/fernandezlucena.es-0001/chain.pem \
               -caname caname
```

Con estos certificados, establecer en el registro tipo "TXT" con nombre "_acme-challenge" y dominio "fernandezlucena" el valor:

lwDPVxbhSJPBeDzMD6apDYxosWmBSAEAFPyB6dFDd2M

![](/linux/adjuntos/DNS-Configuracion.jpg)

Así ha quedado la configuración tras los cambios en la migración de hostinet.

![](/linux/adjuntos/nueva-cfg-resgistros-dns-hostinet.png)

## Certificados para emails de letsencrypt

Para renovar el certrificado, tenemos que parar el servicio nginx, crear el nuevo certificado y arrancar nginx.

Certificados empleados para postfix y dovecot en el dominio fernandezlucena.es hospedado clouding.io

Antes de ejecutar este comando habría que parar el servicio nginx

 `sudo systemctl stop nginx`

```
sudo certbot certonly --standalone --rsa-key-size 4096 --agree-tos --preferred-challenges http -d fernandezlucena.es
```

certbot nos mostrará:

```
antonio@fernandezlucena:~$ sudo certbot certonly --standalone --rsa-key-size 4096 --agree-tos --preferred-challenges http -d fernandezlucena.es
  Saving debug log to /var/log/letsencrypt/letsencrypt.log
  Plugins selected: Authenticator standalone, Installer None
  Cert not yet due for renewal

  You have an existing certificate that has exactly the same domains or certificate name you requested and isn't close to expiry.
  (ref: /etc/letsencrypt/renewal/fernandezlucena.es.conf)

  What would you like to do?

  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

  1: Keep the existing certificate for now
  2: Renew & replace the certificate (may be subject to CA rate limits)

  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

  Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2
  Renewing an existing certificate for fernandezlucena.es

  IMPORTANT NOTES:

   - Congratulations! Your certificate and chain have been saved at:
     /etc/letsencrypt/live/fernandezlucena.es/fullchain.pem
     Your key file has been saved at:
     /etc/letsencrypt/live/fernandezlucena.es/privkey.pem
     Your certificate will expire on 2021-06-22. To obtain a new or
     tweaked version of this certificate in the future, simply run
     certbot again. To non-interactively renew *all* of your
     certificates, run "certbot renew"

   - If you like Certbot, please consider supporting our work by:

     Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
     Donating to EFF:                    https://eff.org/donate-le

```

## Gestión de certificados letsencrypt

`Para ver el estado de los certificados`

```
sudo certbot [--config-dir /home/antonio/config-dir] certificates
Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

Found the following certs:
  Certificate Name: fernandezlucena.es
    Serial Number: 3da014eee84924d3359b8f2b66a893ec99a
    Domains: *.fernandezlucena.es
    Expiry Date: 2020-11-25 19:09:58+00:00 (VALID: 33 days)
    Certificate Path: /home/antonio/config-dir/live/fernandezlucena.es/fullchain.pem
    Private Key Path: /home/antonio/config-dir/live/fernandezlucena.es/privkey.pem
```

`Podemos ver la esctructura de certificados letsencrypt:`

Vemos dos certificados uno generado con la opccion --nginx para el subdominio metarestaurante.ajamam.es y otro para el dominio fernandezlucena.es que nos sirve para los servidores de email.

```
sudo tree -r /etc/letsencrypt 

/etc/letsencrypt
├── ssl-dhparams.pem
├── renewal-hooks
│   ├── pre
│   ├── post
│   └── deploy
├── renewal
│   ├── metarestaurante.ajamam.es.conf
│   └── ajamam.es.conf
├── options-ssl-nginx.conf
├── live
│   ├── metarestaurante.ajamam.es
│   │   ├── privkey.pem -> ../../archive/metarestaurante.ajamam.es/privkey2.pem
│   │   ├── fullchain.pem -> ../../archive/metarestaurante.ajamam.es/fullchain2.pem
│   │   ├── chain.pem -> ../../archive/metarestaurante.ajamam.es/chain2.pem
│   │   ├── cert.pem -> ../../archive/metarestaurante.ajamam.es/cert2.pem
│   │   └── README
│   ├── ajamam.es
│   │   ├── privkey.pem -> ../../archive/ajamam.es/privkey1.pem
│   │   ├── fullchain.pem -> ../../archive/ajamam.es/fullchain1.pem
│   │   ├── chain.pem -> ../../archive/ajamam.es/chain1.pem
│   │   ├── cert.pem -> ../../archive/ajamam.es/cert1.pem
│   │   └── README
│   └── README
├── keys
│   ├── 0003_key-certbot.pem
│   ├── 0002_key-certbot.pem
│   ├── 0001_key-certbot.pem
│   └── 0000_key-certbot.pem
├── csr
│   ├── 0003_csr-certbot.pem
│   ├── 0002_csr-certbot.pem
│   ├── 0001_csr-certbot.pem
│   └── 0000_csr-certbot.pem
├── cli.ini
├── archive
│   ├── metarestaurante.ajamam.es
│   │   ├── privkey2.pem
│   │   ├── privkey1.pem
│   │   ├── fullchain2.pem
│   │   ├── fullchain1.pem
│   │   ├── chain2.pem
│   │   ├── chain1.pem
│   │   ├── cert2.pem
│   │   └── cert1.pem
│   └── ajamam.es
│       ├── privkey1.pem
│       ├── fullchain1.pem
│       ├── chain1.pem
│       └── cert1.pem
└── accounts
    └── acme-v02.api.letsencrypt.org
        └── directory
            └── 6437f42aed1a2cc218a6df81928db4f9
                ├── regr.json
                ├── private_key.json
                └── meta.json
```

`Para eliminar un certificado`

```
certbot delete --cert-name mywebsite.com
```

`Parar la renovación de un certificado sin eliminarlo`

  ```
  mv /etc/letsencrypt/renew/<cert-name>.conf  /etc/letsencrypt/renew/<cert-name>.conf.disabled
  ```

`Chequear los timers de certbot`

```text
systemctl list-timers

NEXT                        LEFT          LAST                        PASSED       UNIT                           ACTIVATES         >
Sat 2023-12-09 05:21:31 CET 6h left       Fri 2023-12-08 19:37:15 CET 3h 17min ago certbot.timer                 certbot.service

```
`Para ver el estado del servicio`

```text
antonio@ajamam:~$ sudo systemctl status certbot.service
○ certbot.service - Certbot
     Loaded: loaded (/lib/systemd/system/certbot.service; static)
     Active: inactive (dead) since Fri 2023-12-08 19:37:16 CET; 3h 43min ago
TriggeredBy: ● certbot.timer
       Docs: file:///usr/share/doc/python-certbot-doc/html/index.html
             https://certbot.eff.org/docs
    Process: 221512 ExecStart=/usr/bin/certbot -q renew (code=exited, status=0/SUCCESS)
   Main PID: 221512 (code=exited, status=0/SUCCESS)
        CPU: 1.360s

Dec 08 19:37:15 ajamam systemd[1]: Starting Certbot...
Dec 08 19:37:16 ajamam systemd[1]: certbot.service: Deactivated successfully.
Dec 08 19:37:16 ajamam systemd[1]: Finished Certbot.
Dec 08 19:37:16 ajamam systemd[1]: certbot.service: Consumed 1.360s CPU time.
```

`Para ver la configuración del servicio certbot.service`

```text
antonio@ajamam:~$ sudo cat /lib/systemd/system/certbot.service
[Unit]
Description=Certbot
Documentation=file:///usr/share/doc/python-certbot-doc/html/index.html
Documentation=https://certbot.eff.org/docs
[Service]
Type=oneshot
ExecStart=/usr/bin/certbot -q renew
PrivateTmp=true
```

`La configuración del timer`

```
antonio@ajamam:~$ sudo cat /lib/systemd/system/certbot.timer
[Unit]
Description=Run certbot twice daily

[Timer]
OnCalendar=*-*-* 00,12:00:00
RandomizedDelaySec=43200
Persistent=true
```


`Mostrar los resultado de la renovación de certificdos`
```text
sudo journalctl --follow -u certbot --since "2023-12-11 16:10:00"
```

## Certificados para web y email de Ionos
   
   El certificado wildcard porporcionado por Ionos es válido para web y para email. Utilizando letsencrypt con cerbot, se ha tenido que generar un certificado wildcar para web *.dominio.es y otro para postfix y dovecot.

   Desde la página de ionos, hacemos download de la clave privada (dominio-cert-ssl-private.key) y luego del certificado para el cominio *.ajamam.es (dominio-cert-ssl.cer), y el certificado de la CA (dominio-cert-ssl-CA.cer).
   Genemos el fichero fullchain.cer concatenando al certificado del dominio, el certificado de la CA (este último queda al final del fichero).

**Certificados y clave privada proporcionados por ionos**

_.ajamam.es_private_key.key

_.ajamam.es_ssl_certificate_INTERMEDIATE.cer (certificado de la CA)

ajamam.es_ssl_certificate.cer

**Cambio de nombres de los certificados y clave y generación de fullchain**

dominio-cert-ssl-CA.cer = _.ajamam.es_ssl_certificate_INTERMEDIATE.cer

dominio-cert-ssl-fullchain.cer = ajamam.es_ssl_certificate.cer + _.ajamam.es_ssl_certificate_INTERMEDIATE.cer

dominio-cert-ssl-private.key = _.ajamam.es_private_key.key

dominio-cert-ssl.cer = ajamam.es_ssl_certificate.cer

`Generamos el p12 utilizado por la aplicacion spring REST:`
```
   openssl pkcs12 -export -in /home/antonio/certificados/dominio-cert-ssl.cer \
               -inkey /home/antonio/certificados/dominio-cert-ssl-private.key \
               -out ./www/keystore.p12 \
               -name tomcat \
               -CAfile /home/antonio/certificados/dominio-cert-ssl-CA.cer \
               -caname caname
```

## Instalacioón y configuración del correo electrónico

**Postfix**

```
sudo apt install postfix
```

`Utilidad para configurar postfix`

```
sudo dpkg-reconfigure postfix

```

`Ficheros de postfix a modificar`
```
/etc/postfix/main.cf
/etc/postfix/master.cf
/etc/opendkim.conf
/etc/opendkim/*
```

`Algunos cambios en /etc/postfix/main.cf`

```
antonio@ajamam:~$ sudo postconf -e 'home_mailbox= Maildir/'
antonio@ajamam:~$ sudo postconf -e 'virtual_alias_maps= hash:/etc/postfix/virtual'
antonio@ajamam:~$ sudo nano /etc/postfix/virtual
antonio@ajamam:~$ sudo postmap /etc/postfix/vihisrtual
```

`Instalar mailutils para envio de corres y pruebas`

```
sudo apk install mailutils
```

`IMPORTANTE: Los registros del servidor de nombres SPF Y DKIM`

[Pincha aquí para una explicación extendida](https://www.linuxbabe.com/mail-server/setting-up-dkim-and-spf)

[Aquí también se explica posfix, opendkim y dmarc](https://blog.tiraquelibras.com/?p=660)

[Enlace para instalar postfix y dovecot](https://www.linuxbabe.com/mail-server/secure-email-server-ubuntu-postfix-dovecot)

El registro SPF (Marco de políticas del remitente) especifica qué hosts o direcciones IP pueden enviar correos electrónicos en nombre de un dominio . Debe permitir que sólo su propio servidor de correo electrónico o el servidor de su ISP envíen correos electrónicos para su dominio.

```
TXT  @   v=spf1 mx ~all
```
~all indicamos que solo tener en cuenta la ip del servidor de correo

**Queda pendiente de aplicar en servidor, la gestión de la política SPF para correos entrantes. Ir a la explicación extendida.**


DKIM (DomainKeys Identified Mail) utiliza una clave privada para agregar una firma a los correos electrónicos enviados desde su dominio . Los servidores SMTP receptores verifican la firma utilizando la clave pública correspondiente, que está publicada en su administrador de DNS.

`Instalación de OPENDKIM`

```
sudo apt install opendkim opendkim-tools
```

Adaptar ficheros de configuracion:
​    

​    /etc/dovecot/conf.d/10-auth.conf

​    /etc/dovecot/conf.d/10-mail.conf

​    /etc/dovecot/conf.d/10-ssl.conf

​    /etc/dovecot/conf.d/10-master.conf

​    /etc/dovecot/dovecot.conf

Abrir puertos:

​    ports 25 (SMTP) ok, 587 (SMTP over TLS) ok, 465 (SMTPS) ok, 143 (IMAP) ok, 993 (IMAPS) ok, 110 (POP3) ok, 995 (POP3S) ok

`Ver estado del servicio postfix:`

```console
sudo systemctl status postfix
```

`Ver la configuarción de postfix' 

```console
sudo postconf
```

`enviar correo de prueba`

```console
echo "Subject:Correo Maildir 1" | sendmail <info@fernandezlucena.es>
```

`Ver log`

```console
sudo tail -f -n 200 /var/log/mail.log
```

`Ver los correos del usurario info`

```console
mail -f /home/info/Maildir
```

`Ver protocolos instalados por dovecot`

```console
sudo cat /usr/share/dovecot/protocols.d/*.protocol
```

`Ver cola de correos de un usuario`

```consolesu
mailq
```

`Eliminar cola de correos`

```python
sudo postsuper -d ALL
```

`Eliminar cola de correos diferidos`

```python
sudo postsuper -d ALL deferred
```

`Enviar email`

```python
mail -s "Email simple enviado desde la terminal" nonaino@mail.com
```

`Ver cabecera y cola de un correo`

``` consola
postcat -q ID-Correo
```

`Eliminar un correo determinado en cola`

```
postsuper -d ALL
```

`Eliminar todos los correos devueltos`

```consola
postsuper -d ALL deferred
```


`Comprobación de calidad de nuestros correos`

[En este enlace puedes hacer la evalucación](https://www.mail-tester.com/)

## Servicios systemctl

- generar el servicio systemctl, para ello creamos el archivo
  etc/systemd/system/aflcv-service.service:

```
  [Unit]
  Description=Java restaurante Service

  [Service]
  User=antonio

  WorkingDirectory=/home/antonio/www/restaurante-back
  ExecStart=java -jar /home/antonio/www/restaurante-back/app.jar

  [Install]
  WantedBy=multi-user.target
```

Esta app es un spring boot al que se le asigna un keystore.p12, por tanto  el front de angular puede solicitar los servicios con:
GET "<https://restaurante-back.fernandezlucena.es:8084/api/pedido/1>" (SSL). Los mensajes https (TLS) desde el front no necesinta de ningún reverse proxi de nginx.


  ***Parada y arranque de servicios***

  sudo systemctl daemon-reload

  sudo systemctl enable restaurante-back.service

  sudo systemctl start restaurante-back.service

  sudo systemctl restart restaurante-back.service

  sudo systemctl stop restaurante-back.service

## Deploy en servidor fernandezlucena.es

1. Abrir puerto 8074 y 8084 para front y back respectivamente

2. Instalar nvm

3. Con nvm instalamos node

4. Instalamos express

5. Instalamos nginx

## Configuaración reverse proxies de nginx

   En el directorio /etc/nginx/conf.d, añadimos un fichero para la parte front de cada aplicación web, este es un ejemplo para la aplicación restaurante (restaurante.fernandezlucena.es.conf):

```
   server {
   listen 80;
   server_name restaurante.fernandezlucena.es www.restaurante.fernandezlucena.es; # Edit this to your domain name
   rewrite ^ https://$host$request_uri permanent;
   }

   server {
   listen 443 ssl;

   server_name restaurante.fernandezlucena.es;
   \# Edit the above _YOUR-DOMAIN_ to your domain name

   ssl_certificate /etc/letsencrypt/live/fernandezlucena.es-0001/fullchain.pem;
   \# If you use Lets Encrypt, you should just need to change the domain.
   \# Otherwise, change this to the path to full path to your domains public certificate file.

   ssl_certificate_key /etc/letsencrypt/live/fernandezlucena.es-0001/privkey.pem;
   \# If you use Let's Encrypt, you should just need to change the domain.
   \# Otherwise, change this to the direct path to your domains private key certificate file.

   ssl_session_cache builtin:1000 shared:SSL:10m;
   \# Defining option to share SSL Connection with Passed Proxy

   ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
   \# Defining used protocol versions.

   ssl_ciphers HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4;
   \# Defining ciphers to use.

   ssl_prefer_server_ciphers on;
   \# Enabling ciphers

   access_log /var/log/nginx/access.log;
   \# Log Location. the Nginx User must have R/W permissions. Usually by ownership.

   location / {
   proxy_set_header Host $host;
   proxy_set_header X-Real-IP $remote_addr;
   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_set_header X-Forwarded-Proto $scheme;
   proxy_pass http://localhost:8074;
   \#proxy_pass unix:/path/to/php7.3.sock # This is an example of how to define a unix socket.
   proxy_read_timeout 90;
   }

   } \# Don't leave this out! It "closes" the server block we started this ile with.
```

Las peticiones al back emitidas desde angular no se tratan con nginx, puesto que la aplicación back de angular boot, trabaja con un certificado que le permite la gestion TLS.

podemos ahora comprovar la sintaxis con **sudo nginx -t**

y para que entre en vigor: **sudo systemctl restart nginx**

**Ejemplo de estructura de ficheros de una aplicación back:**
antonio@fernandezlucena:~/www/restaurante-back$ tree -a

También habría que añadir keystore.p12 (para https)

```
•    .
•    ├── app.jar
•    ├── downloads
•    │   ├── Areadme.txt
•    │   └── cv-AntonioFernandezLucena.pdf
•    ├── logs
•    │   ├── archived
•    │   │   ├── spring-boot-logger-2021-08-08.0.log
•    │   │   └── spring-boot-logger-2021-08-08.1.log
•    │   └── spring-boot-logger.log
•    ├── src
•    │   └── main
•    │       └── resources
•    │           ├── application.properties
•    │           ├── import.sql
•    │           ├── log4j.properties
•    │           ├── logback-spring.out.xml
•    │           ├── logback-spring.xml
•    │           ├── mail
•    │           │   ├── editablehtml
•    │           │   │   ├── email-activacion-cuenta.html
•    │           │   │   ├── email-reset-pwd.html
•    │           │   │   └── images
•    │           │   │       ├── background.png
•    │           │   │       ├── logo-background.png
•    │           │   │       ├── thymeleaf-banner.png
•    │           │   │       └── thymeleaf-logo.png
•    │           │   ├── emailconfig.properties
•    │           │   ├── html
•    │           │   │   ├── email-inlineimage.html
•    │           │   │   ├── email-simple.html
•    │           │   │   └── email-withattachment.html
•    │           │   ├── javamail.properties
•    │           │   ├── MailMessages_en.properties
•    │           │   ├── MailMessages_es.properties
•    │           │   ├── MailMessages_fr.properties
•    │           │   ├── MailMessages_it.properties
•    │           │   ├── MailMessages.properties
•    │           │   ├── MailMessages_pt.properties
•    │           │   ├── MailMessages_zh.properties
•    │           │   └── text
•    │           │       └── email-text.txt
•    │           ├── Messages_es.properties
•    │           ├── Messages_it.properties
•    │           ├── Messages.properties
•    │           ├── Messages_pt.properties
•    │           ├── static
•    │           │   └── images
•    │           │       ├── background.png
•    │           │       ├── logo-background.png
•    │           │       ├── mini_no-photo.jpg
•    │           │       ├── mini_no-photo.png
•    │           │       ├── no-photo-2.jpg
•    │           │       ├── no-photo-2.png
•    │           │       ├── no-photo-3.png
•    │           │       ├── no-photo-ok.png
•    │           │       ├── no-photo.png
•    │           │       ├── np-photo.jpg
•    │           │       ├── thymeleaf-banner.png
•    │           │       └── thymeleaf-logo.png
•    │           └── templates
•    │               ├── console.html
•    │               ├── cuentaactivada.html
•    │               └── images
•    │                   ├── background.png
•    │                   ├── logo-background.png
•    │                   ├── thymeleaf-banner.png
•    │                   └── thymeleaf-logo.png
•    └── uploads
•        ├── admin
•        │   ├── empresa.png
•        │   ├── filtro-32.png
•        │   ├── help.png
•        │   ├── home-old.png
•        │   ├── home_peque.png
•        │   ├── home.png
•        │   ├── menu_peque.png
•        │   ├── menu.png
•        │   ├── no-filtro.png
•        │   ├── no-photo.jpg
•        │   ├── no-photo.png
•        │   ├── orders.png
•        │   ├── sugerencias_peque.png
•        │   ├── sugerencias.png
•        │   ├── tipos_peque.png
•        │   ├── tipos.png
•        │   └── upload.png
•        ├── menus
•        ├── sliders
•        ├── sugerencias
•        │   └── e25d7c35-d89e-44e2-ad51-438b3f763633_slider22.jpg
•        └── tipoplatos
•    
•    21 directories, 70 files
```

## Instalacion del front (sin universal)

Creamos dist para producción (**npm run – ng build –prod**)
En \home\antonio\www\restaurante-front creamos un carpetas con nombre
dist, en ella hacemos un npm init, instalamos express:

```
nmp install express –save
```

Copiamos la carpeta de desarrallo del pc …..\dist\restaurante from en la carpeta de producción dist que acabamos de crear.
En la carpeta de producción dist creamos index.js que es el que escuchará en el puerto 8084. La carpeta index tendrá:

antonio@fernandezlucena:~/www/restaurante-front/dist $cat index.js:

```
let express = require('express');
let path = require('path');
let app = express();
let port = 8074;


app.use(express.static('restaurante-front'));
app.get('*', (req, res, next)  => {
    res.sendFile(path.resolve('restaurante-front/index.html'));
});
app.listen(port, () => {
    console.log('\n express escuchando sobre el puerto ' + port)

});
```

Mostramos la carpeta del front:
antonio@fernandezlucena:~/www/restaurante-front$ **tree -I node_modules**:

```
.
└── dist
    ├── index.js
    ├── package.json
    ├── package-lock.json
    └── restaurante-front
        ├── 10.1ece1a9a9c5193248e11.js
        ├── 1.0e4b4ce2d30bfaacd094.js
        ├── 11.82b9514f85d5b15d3ff2.js
        ├── 12.ef9233db6b07fa0229b0.js
        ├── 3.270fd670bdc139cb5a9e.js
        ├── 3rdpartylicenses.txt
        ├── 7.00d382c510f62f6619c2.js
        ├── 8.80d44fb1786445fd2f72.js
        ├── 9.520396dda44da9604adb.js
        ├── assets
        │   ├── i18n
        │   │   ├── de.json
        │   │   ├── en.json
        │   │   ├── es.json
        │   │   ├── fa.json
        │   │   ├── fr.json
        │   │   ├── it.json
        │   │   ├── ur.json
        │   │   └── zh-CHS.json
        │   └── images
        │       ├── zzsliders
        │       │   ├── slider1.jpg
        │       │   ├── slider2.jpg
        │       │   └── slider3.jpg
        │       └── zzutilidades
        │           ├── angular-material.jpg
        │           ├── angular.png
        │           ├── bootstrap.jpg
        │           ├── eclipse.png
        │           ├── git.png
        │           ├── java.jpg
        │           ├── jwt.png
        │           ├── maven.png
        │           ├── postgresql.jpg
        │           ├── spring-boot.jpg
        │           ├── spring-data.jpg
        │           ├── spring-oauth2.png
        │           ├── spring-security.jpg
        │           └── vs-code.jpg
        ├── common.0bbfbc17c307f5ae1e65.js
        ├── favicon.ico
        ├── fontawesome-webfont.1e59d2330b4c6deb84b3.ttf
        ├── fontawesome-webfont.20fd1704ea223900efa9.woff2
        ├── fontawesome-webfont.8b43027f47b20503057d.eot
        ├── fontawesome-webfont.c1e38fd9e0e74ba58f7a.svg
        ├── fontawesome-webfont.f691f37e57f04c152e23.woff
        ├── index.html
        ├── main.9221e31d7044ddd5a2d4.js
        ├── polyfills.8b8220e5f603f418c256.js
        ├── runtime.a317b2920e26a5d35901.js
        └── styles.b4ddf2dbc5795ba15b24.css
```

## Instalacion del front (con universal)

[explicación de universal](https://www.ganatan.com/tutorials/server-side-rendering-with-angular-universal)

Al añadir universal al proyecto se creó un fichero server.ts, este fichero hay que retocarlo para poner el puerto de escucha para servir los ficheros al navegador, en este caso el puerto 8074 (restaurante.fernandezlucena.es). Esto se reflejará en el main.js, que finalmente atiendo el puerto 8074. Ver como se utiliza este ejecutable (main.js) cuando se describe el servicio "metarestaurante-front.service".

Ahora podemos generar la carpeta deploy (dist) con npm run -- ng build:ssr.

A continuación se copia la carpeta dist en /home/antonio/www/metarestaurante-front del servidor

Tener en cuenta que cada vez que actualicemos la carpeta dist del servidor, tendremos que incorporar los ficheros sitemap.xml y robots.txt en

/home/antonio/www/metarestaurante-front/dist/metarestaurante-front/browser

podemos automatizar con angular.json:

```
   "architect": {

​    "build": {

​     "builder": "@angular-devkit/build-angular:browser",

​     "options": {

​      "outputPath": "dist/metarestaurante-front/browser",

​      "index": "src/index.html",

​      "main": "src/main.ts",

​      "polyfills": "src/polyfills.ts",

​      "tsConfig": "tsconfig.app.json",

​      "inlineStyleLanguage": "scss",

​      "assets": [

​       "src/favicon.ico",

​       "src/assets",

​       "src/sitemap.xml",

​       "src/robots.txt"
```

El servicio /etc/systemd/system/restaurante-front.service es:

```
[Unit]
Description=Angular restaurante-front Service

[Service]
User=antonio
#The configuration file application.properties should be here:
#WorkingDirectory=/home/antonio/www/restaurante-front/dist
WorkingDirectory=/home/antonio/www/restaurante-front
#ExecStart= /home/antonio/.nvm/versions/node/v14.17.4/bin/node /home/antonio/www/restaurante-front/dist/index.js
ExecStart= /home/antonio/.nvm/versions/node/v14.17.4/bin/node /home/antonio/www/restaurante-front/dist/restaurante-front/server/main.js

[Install]
WantedBy=multi-user.target
```

**SEO**: debemos ir a google search console, para indicar las paginas a indexar y asigna el sitemap.xmlsb
