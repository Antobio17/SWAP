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
    


