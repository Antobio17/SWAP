# Práctica 3: Balanceo de Carga

En esta práctica el objetivo es configurar un balanceador que reparta la carga en nuestros servidores. Vamos a balancear los servidores con protocolo HTTP configurados para conseguir una infraestructura redundante y de alta disponibilidad.

Para comenzar instalaremos mediante VirtualBox una nueva Maquina donde instalaremos nuestro balanceador de carga. Configuraremos nuestra maquina virtual sin apache y la añadiremos a la red exactamente como en la práctica 2.

## Instalar nginX

Una vez configurada nuestra máquina con IP **192.168.56.103** procederemos a instalar nginX:
    
    sudo apt-get update && sudo apt-get dist-upgrade && sudo apt-get autoremove
    sudo apt-get install nginx
    
Y arrancamos el servidor:

    sudo systemctl start nginx
    
Ahora vamos a pasar a su configuración modificando/creando el archivo **/etc/nginx/conf.d/default.conf**. Definimos en la sección *upsctream* las maquinas que forman el cluster web y en la sección *server* indicamos que la conexión entre los servidores finales y nginX es por HTTP 1.1. Hay multiples opciones en el apartado upstream como weight=X para indicar a que servidor se da más prioridad, ip_hash para hacer uso de round robin, keepalive=X para mantener establecida la conexión X segundos y no crear múltiples conexiones nuevas...

    upstream apaches {
     server 192.168.56.101;
     server 192.168.56.102;
     keepalive 5;
    }

    server{
     listen 80;
     server_name balanceador;
  
     access_log /var/log/nginx/balanceador.access.log;
     error_log /var/log/nginx/balanceador.error.log;
     root /var/www/;
  
     location /
      {
        proxy_pass http://apaches;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
      }
    }
    
Ahora reiniciamos el servidor y si lo hemos configurado correctamente no debería saltar ningún error.

    sudo systemctl restart nginx

Debemos comentar la última linea del archivo **/etc/nginx/nginx.conf** para que funcione como balanceador.
Para comprobar que todo funciona correctamente basta con hacer peticiones a la IP del balanceador desde la terminal de nuestra maquina principal por ejemplo:

    curl http://192.168.56.103
    
Para que nos salga una cosa diferente cuando accede a una maquina servidora u otra podemos tener un index.html diferente en cada una que ponga *Servidor 1/2* respectivamente.

![imagen](https://github.com/Antobio17/swap1819/blob/master/practica3/imagenes/comprobacion.png)

## Instalar haproxy

Tras instalar el sistema básico y configurar la red **(IP 192.168.56.104)**, sólo tenemos que usar apt-get para instalar haproxy:

    sudo apt-get install haproxy
    
Ahora vamos a pasar a su configuración modificando/creando el archivo **/etc/haproxy/haproxy.conf**. Al contenido del archivo se le añadirá lo siguiente:

    global
        daemon
        maxconn 256
        
    defaults
        mode http 
        contimeout 4000
        clitimeout 42000
        srvtimeout 43000

    frontend http-in
        bind *:80
        default_backend servers
        
    backend servers
        server m1 192.168.56.101:80 maxconn 32
        server m1 192.168.56.102:80 maxconn 32

Una vez salvada la configuración en el fichero, lanzamos el servicio haproxy mediante el comando:

    sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg
    
Si no nos sale ningún error o aviso, todo ha ido bien, y ya podemos comenzar a hacer peticiones a la IP del balanceador (como hicimos en el caso del nginx con cURL).

![imagen](https://github.com/Antobio17/swap1819/blob/master/practica3/imagenes/comprobacion.png)

## Someter a una alta carga el servidor balanceado

Por último vamos a someter a una gran carga nuestra graja web. Para ello esaremos el comando:

    ab -n 50000 -c 50 http://192.168.56.10*/index.html //n = numero de peticiones; c = concurrecia
    
Para realizar una comparación más justa he eliminado los pesos y el keepalive en el archivo de configuración de nginx. Los resultados son los siguientes:

### Servidor con nginX

    ab -n 50000 -c 50 http://192.168.56.103/index.html
    
![imagen](https://github.com/Antobio17/swap1819/blob/master/practica3/imagenes/ServerNginx.png)

### Servidor Haproxy

    ab -n 50000 -c 50 http://192.168.56.104/index.html
    
![imagen](https://github.com/Antobio17/swap1819/blob/master/practica3/imagenes/ServerHaproxy.png)

Como podemos observar, el servidor de balanceo con haproxy sirve en una media de 16 segundos, mucho menor que los 20 de nginX. Con estos datos podemos afirmar que el software de balanceo haproxy es el que mejor funciona de los dos.
