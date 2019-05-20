# Práctica 5: Replicación de bases de datos MySQL
En esta práctica vamos a realizar una réplica de una Base de Datos del servidor principal al servidor secundario. Para ello, podemos o usar mysqldump o una configuración maestro-esclavo. Lo primero que debemos hacer es es crear una base de datos con una tabla y posteriormente insertar datos en la misma:

![imagen](https://github.com/Antobio17/swap1819/blob/master/practica5/imagenes/db1.png)
![imagen](https://github.com/Antobio17/swap1819/blob/master/practica5/imagenes/db2.png)
![imagen](https://github.com/Antobio17/swap1819/blob/master/practica5/imagenes/db3.png)

Una vez hecho esto, debemos replicar la base de datos creada usando una de las dos configuraciones indicadas anteriormente.

## Replicar una Base de Datos MySQL usando mysqldump
Antes de realizar la copia del archivo SQL de nuestra BD hemos de evitar que esta pueda ser modificada durante el proceso de copia con el siguiente comando:

    mysql> FLUSH TABLES WITH READ LOCK;

Ahora usamos mysqldump para guardar los datos de nuestra BD usando el siguiente comando en el servidor principal:

    $ mysqldump contactos -u root -p > /tmp/contactos.sql

Para desbloquear las tablas que antes habíamos bloqueado, usamos en mysql el siguiente comando:

    mysql> UNLOCK TABLES;

Hacemos la copia, desde nuestro esclavo hemos de usar el siguiente comando para copiar los datos guardados:

    scp 192.168.56.101:/tmp/contactos.sql /tmp/
