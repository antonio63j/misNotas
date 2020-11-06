



## Certificados para web

  Instalamos con apt snap certbot, en ubuntu 20.04 ya biene instalado openssl y git

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

Para renobar los certificados sería:

```
sudo certbot --config-dir /home/antonio/config-dir renew
```

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

"CXSiyrZ3bHpWgipqYGD1d1NcMXIzGiuK411WSgpVt4E"

![](C:\Proyectos\AngularSpring\curriculum2\aflcv-back\configuracionDNS.jpg)

## Certificados para emails

Certificados empleados para postfix y dovecot en el dominio fernandezlucena.es hospedado clouding.io

```
sudo certbot certonly --standalone --rsa-key-size 4096 --agree-tos --preferred-challenges http -d fernandezlucena.es
```

## Emails

### Instalar postfix y dovecot

Para dovecot, solo se ha instalado imap

### Adaptar ficheros de configuracion:

​	/etc/postfix/main.cf
​	/etc/dovecot/conf.d/10-auth.conf
​	/etc/dovecot/conf.d/10-mail.conf
​	/etc/dovecot/conf.d/10-ssl.conf
​	/etc/dovecot/dovecot.conf

### Abrir puertos:

​	ports 25 (SMTP) ok, 587 (SMTP over TLS) ok, 465 (SMTPS) ok, 143 (IMAP) ok, 993 (IMAPS) ok, 110 (POP3) ok, 995 (POP3S) ok

### ver estado del servicio postfix:

​		ver las instancias creadas por postfix

```
sudo systemctl -l status postfix@-
```

​          ver la configuarción de postfix

```
sudo postconf
```

### enviar correo de prueba

```
echo "Subject:Correo Maildir 1" | sendmail info@fernandezlucena.es
```

### ver log:

```
sudo tail -f /var/log/mail.log
```

### ver los correos del usurario info:

```
mail -f /home/info/Maildir
```

### ver protocolos instalados por dovecot:

```
sudo cat /usr/share/dovecot/protocols.d/*.protocol
```

### ver cola de correos de un usuario

```
mailq
```

### eliminar cola de correos

```
sudo postsuper -d ALL
```

### eliminar cola de correos diferidos

```
sudo postsuper -d ALL deferred
```

