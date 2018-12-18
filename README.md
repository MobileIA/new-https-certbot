# Como utilizar Certificados HTTPS gratis con Let`s Encrypt
1. Agregar la siguiente regla al virtualhost en Apache:
```xml
<Directory /var/www/mobileia.com/.well-known/acme-challenge>
    DefaultType text/plain
</Directory>
```
2. Reiniciamos Apache:
```bin
sudo service apache2 restart
```
3. Generamos el certificado con el siguiente comando:
```bin
sudo certbot certonly
# Seleccionar directamente el dominio
sudo certbot --apache certonly
```
4. Seleccionar: 3: Place files in webroot directory (webroot):
5. Especificar una dirección de email valida.
6. Aceptar los terminos y condiciones
7. Indicar el dominio o dominios para los cuales sera valido el certificado.
8. Siguiente paso indicar la raiz del sitio web (webroot)
9. Seleccionar la ruta indicada.
10. Y ya se tenemos el certicado creado: 
```konsole
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/mobileia.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/mobileia.com/privkey.pem
   Your cert will expire on 2018-11-01. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:
   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    
```
11. Ya tenemos los archivos en las carpetas correspondientes.
12. Activar modulo SSL en apache:
```konsole
sudo a2enmod ssl
```
12. Solo resta configurar Apache:
```
<VirtualHost *:80>
    ServerAdmin matiascamiletti@mobileia.com
    ServerName contact.mobileia.com
    ServerAlias www.contact.mobileia.com
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
</VirtualHost>

<VirtualHost *:443>
        ServerAdmin matiascamiletti@mobileia.com
        ServerName contact.mobileia.com
        ServerAlias www.contact.mobileia.com

        SSLEngine on
        SSLCertificateFile /etc/letsencrypt/live/contact.mobileia.com/cert.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/contact.mobileia.com/privkey.pem
        SSLCertificateChainFile /etc/letsencrypt/live/contact.mobileia.com/chain.pem
        
        DocumentRoot /var/www/mobileia-contact-api/public
        ErrorLog ${APACHE_LOG_DIR}/mobileia-contact-api.error.log
        CustomLog ${APACHE_LOG_DIR}/mobileia-contact-api.access.log combined
        AccessFileName .htaccess
        RewriteEngine on
        <Directory /var/www/mobileia-contact-api/public >
              AllowOverride All
        </Directory>
        <Directory /var/www/mobileia-contact-api/public/.well-known/acme-challenge>
                DefaultType text/plain
        </Directory>
</VirtualHost>
```