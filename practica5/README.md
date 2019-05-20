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
    
![imagen](https://github.com/Antobio17/swap1819/blob/master/practica5/imagenes/db4.png)

Habiendo terminado este proceso, restauramos los datos en la BD del esclavo. Primero hemos de crear la base de datos entrando en mysql:

    CREATE DATABASE contactos;

Y por último, restauramos los datos usando el comando:

    mysql -u root -p contactos < /tmp/contactos.sql

Comprobamos que la copia ha sido satisfactoria mostrando la información de la tabla:

![imagen](https://github.com/Antobio17/swap1819/blob/master/practica5/imagenes/db5.png)

## Replicar una BD mediante una configuración maestro esclavo


Como el anterior procedimiento debe realizarse de manera manual mostraremos otra herramienta que permite realizar esta tarea automáticamente. El siguiente proceso debe realizarse tanto para la máquina que hará de maestro y la que hará de esclavo. Para hacerlo simplemente debemos acceder al archivo de configuración /etc/mysql/mysql.conf.d/mysqld.cnf y comentamos la siguiente línea:

    #bind-address 127.0.0.1

También debemos indicar el archivo del log de errores así como el registro binario que contiene la información disponible en el registro de actualizaciones:

    log_error = /var/log/mysql/error.log log_bin = /var/log/mysql/bin.log

Por último, indicamos el identificador del servidor (1 para la máquina maestro y 2 para la máquina esclavo):

    server-id = 1

Guardamos el arhcivo de configuración y reiniciamos el servicio usando la siguiente orden:

    $ /etc/init.d/mysql restart

Si todo va bien, el mensaje mostrado al ejecutar lo anterior será un 'ok'. De otra forma, será un error:
Ahora accedemos a la máquina maestro y creamos un usuario:

![imagen](https://github.com/Antobio17/swap1819/blob/master/practica5/imagenes/db6.png)


