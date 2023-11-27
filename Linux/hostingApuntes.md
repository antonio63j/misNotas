
[TOC]

## Ubuntu 20.04

- instalar openjdk8
- instalar postgresql (version 12)
- snap, certbot (con snap), dovecot, postfix,  opendKim, 
- nginx, nvm, node (con nvm)

## Certificados para web

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

`antonio@fernandezlucena:~$ snap list
Name     Version   Rev    Tracking       Publisher     Notes
certbot  2.6.0     3024   latest/stable  certbot-eff✓  classic
core20   20230503  1891   latest/stable  canonical✓    base
snapd    2.59.2    19122  latest/stable  canonical✓    snapd`

Crear certificado, hay un script para ello en /home/antonio: 

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
También se puede comprobar la propagación [aquí ](https://www.whatsmydns.net/#CNAME/aflcv.fernandezlucena.es)

Si ya tenemos generado algún certificado, numera los directorios donde se colocan los certificados. El script de arriba, genera los certificados en  /etc/letsencrypt/live/fernandezlucena.es-0001/.

Para renobar los certificados sería:

```
sudo certbot renew (úl
tima renovacion, que ha dejado los certificados en 
  /etc/letsencrypt/live/fernandezlucena.es-0001/)
```

Crear certificado .p12 para la aplicación spring boot. Si 

```
openssl pkcs12 -export -in /etc/letsencrypt/live/fdusoernandezlucena.es-0001/fullchain.pem \
               -inkey /etc/letsencrypt/live/fernandezlucena.es-0001/privkey.pem \
               -out ./www/aflcv-back/keystore.p12 \
               -name tomcat \
               -CAfile /etc/letsencrypt/live/fernandezlucena.es-0001/chain.pem \
               -caname caname
```

Una vez 

Para ver el estado de los certificados:

```
sudo certbot --config-dir /home/antonio/config-dir certificates
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

Con estos certificados, establecer en el registro tipo "TXT" con nombre "_acme-challenge" y dominio "fernandezlucena" el valor:

lwDPVxbhSJPBeDzMD6apDYxosWmBSAEAFPyB6dFDd2M

![](/linux/adjuntos/DNS-Configuracion.jpg)

Así ha quedado la configuración tras los cambios en la migración de hostinet.

![](/linux/adjuntos/nueva-cfg-resgistros-dns-hostinet.png)


## Certificados para emails

Para renovar el certrificado, tenemos que parar el servicio nginx, crear el nuevo certificado y arrancar nginx.

Certificados empleados para postfix y dovecot en el dominio fernandezlucena.es hospedado clouding.io

Antes de ejecutar este comando habría que parar el servicio nginx

 `sudo systemctl stop nginx`

```
sudo certbot certonly --standalone --rsa-key-size 4096 --agree-tos --preferred-challenges http -d fernandezlucena.es
```

`sudo systemctl start nginx`

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
 
 **Configuración del correo electrónico**

Instalación de postfix y dovecot

Para dovecot, solo se ha instalado imap

Adaptar ficheros de configuracion:

​    /etc/postfix/main.cf
​    /etc/dovecot/conf.d/10-auth.conf
​    /etc/dovecot/conf.d/10-mail.conf
​    /etc/dovecot/conf.d/10-ssl.conf
​    /etc/dovecot/dovecot.conf

Abrir puertos:

​    ports 25 (SMTP) ok, 587 (SMTP over TLS) ok, 465 (SMTPS) ok, 143 (IMAP) ok, 993 (IMAPS) ok, 110 (POP3) ok, 995 (POP3S) ok


Ver estado del servicio postfix:

​ ver las instancias creadas por postfix

<mark>sudo systemctl -l status postfix@-</mark>

Ver la configuarción de postfix

<mark>sudo postconf</mark>

enviar correo de prueba

<mark>echo "Subject:Correo Maildir 1" | sendmail info@fernandezlucena.es</mark>

Ver log:

<mark>sudo tail -f /var/log/mail.log</mark>

Ver los correos del usurario info:
<mark>mail -f /home/info/Maildir</mark>

Ver protocolos instalados por dovecot:
<mark>sudo cat /usr/share/dovecot/protocols.d/*.protocol</mark>

Ver cola de correos de un usuario
<mark>mailq</mark>

Eliminar cola de correos
<mark>sudo postsuper -d ALL</mark>

Eliminar cola de correos diferidos
<mark>sudo postsuper -d ALL deferred</mark>
  
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
GET "https://restaurante-back.fernandezlucena.es:8084/api/pedido/1" (SSL). Los mensajes https (TLS) desde el front no necesinta de ningún reverse proxi de nginx.

  **Parada y arranque de servicios**

  sudo systemctl daemon-reload
  sudo systemctl enable restaurante-back.service
  sudo systemctl start restaurante-back.service
  sudo systemctl restart restaurante-back.service
  sudo systemctl stop restaurante-back.service

**Deploy en servidor fernandezlucena.es:**

1. Abrir puerto 8074 y 8084 para front y back respectivamente

2. Instalar nvm

3. Con nvm instalamos node

4. Instalamos express

5. Instalamos nginx

**Configuaración reverse proxies de nginx:**

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

## Instalacion del front (sin universal):

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

## Instalacion del front (con universal):

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


  

