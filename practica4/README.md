# Práctica 4: Asegurar la Granja Web
En esta práctica el objetivo es llevar a cabo la configuracin de seguridad de la granja web. Para ello llevaremos acabo las siguientes tareas:

## Instalar un certificado SSL autofirmado para configurar el acceso por HTTPS
Un certificado SSL sirve para brindar seguridad al visitante de su página web, una manera de decirles a sus clientes que el sitio es auténtico, real y confiable para ingresar datos personales.

### 1: Generar e instalar un certificado autofirmado
Para esto solo debemos activar el módulo SSL de Apache, generar los certificados y especificarle la ruta a los certificados en la configuración. Vamos a ejecutar lo siguiente en modo root:
    
    a2enmod ssl
    service apache2 restart
    mkdir /etc/apache2/ssl
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
    
Ahora editamos el archivo de configuración del sitio default-ssl:

    nano /etc/apache2/sites-available/default-ssl
    
Y agregamos estas lineas debajo de SSLEngine on:

    SSLCertificateFile /etc/apache2/ssl/apache.crt
    SSLCertificateKeyFile /etc/apache2/ssl/apache.key
    
Activamos el sitio default-ssl y reiniciamos el apache:

    a2ensite default-ssl
    service apache2 reload
