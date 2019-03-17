# PRACTICA 1: Preparación de las herramientas
En esta practica lo primero que haremos será instalar Ubuntu Server en dos maquinas virtuales mediante VirtualBox y conectarlas entre ellas mediante una subred.


## Conexión
Para que poder conectarlas entre ellas lo primero que tenemos que hacer es crearnos una red dentro de VirtualBox. Seleccionamos *Archivo -> Administrador de red anfitrión...* Hacemos click en *Crear* y nos aparecerá una red llamada **vboxnet()**.

![imagen](https://github.com/Antobio17/swap1819/blob/master/practica1/Imagenes/AdminRed.png)

Ahora vamos a crear una interfaz de red en nuestras dos máquinas virtuales. Para ello nos vamos a la configuración de cada una en el apartado de *Red* y seleccionamos *Adaptador 2*. Aquí habilitaremos el adaptador de red y seleccionaremos el **Adaptador solo-anfitrin** con el nombre de nuestra anterior red creada en VirtualBox.

![imagen](https://github.com/Antobio17/swap1819/blob/master/practica1/Imagenes/ConfigRed.png)

Una vez hecho esto con nuestras dos máquinas podremos pasar a la edición del archivo **interfaces** de cada una.
Arrancamos las maquinas, nos movemos al directorio donde está el archivo *cd /etc/network/* y ejecutamos **sudo nano interfaces**. Ahora añadimos las siguientes lineas al archivo:

    auto enp0s8
    iface enp0s8 inet static
    address 192.168.56.11
    netmask 255.255.255.0

Guardamos y ejecutamos en nuestra terminal: 
    
    sudo ifup enp0s8
    
Con la orden ifconfig podemos comprobar que todo se ha realizado correctamente.

## Acceso remoto con ssh y curl
Tenemos nuestra Maquina1 con IP **192.168.56.101** y nuestra Maquina2 con IP **192.168.56.102** y vamos a conectarnos desde la Maquina1 a la Maquina2 por ssh. Para ello ejecutamos en la terminal ssh 192.168.56.102

![imagen](https://github.com/Antobio17/swap1819/blob/master/practica1/Imagenes/ssh.png)

Como podemos ver al conectarnos por ssh y ejecutar ls y obtenemos el el archivo de prueba que se encuentra en la Maquina2.
También si creamos un archivo html de ejemplo (hola.html) en alguna de las dos maquinas (por ejemplo la Maquina2) dentro de la carpeta /var/www/html y ejecutamos la orden curl http://192.168.56.102/hola.html nos aparecerá el contenido del archivo.
