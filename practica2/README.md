# Práctica 2: Clonar la información de un sitio web
El objetivo de esta práctica es aprender a tener nuestras máquinas servidoras clonadas. Esto nos servirá para que a la hora de que el balanceador de carga pida un archivo de igual si es a una o a otra ya que tendrían el directorio **/var/www/html** iguales gracias a la sincronización.

## Crear un tar con ficheros locales en un equipo remoto
Esta opción no es operativa ya que en este caso lo que se haría sería comprimir un tar por ejemplo de la Maquina1, enviarlo por ssh a la Maquina2 y descomprimirlo.

    tar czf - directorio | ssh equipodestino 'cat > ~/tar.tgz'

Así estaríamos obligado a cada máquina a usar recursos del sistema para comprimir y descomprimir archivos pesados. Por esto existen otras maneras más optimas

## Instalar la herramienta rsync
Con la orden *rsync* podremos clonar nuestras carpetas de una forma muy sencilla. Por ejemplo para clonar la carpeta **/var/www/** de la Maquina1 en la Maquina2 usaríamos el siguiente comando (IP de la Maquina1 ejecutado desde la Maquina2).

    rsync -avz -e ssh 192.268.56.101:/var/www/ /var/www/

Hay que tener cuidado ya que si tenemos algun fallo sintáctico, aunque sea mínimo, no nos clonará las carpetas que hemos indicado si no que probablemente nos cree nuevas y no haga lo que deseamos.

## Acceso sin contraseña para ssh
Como *rsync* funciona por *ssh* siempre que ejecutemos el comando nos pedirá la contraseña del sistema. Esto no es del todo interesante para nosotros ya que si programamos un rysinc periodico en el tiempo no queremos estar pendientes de estar metiendo continuamente nuestras credenciales. Para ello vamos a configurar nuestra máquina que ejecute la orden para que acceda a la otra ssin necesidad de poner la contraseña.

Para ello ejecutamos lo siguiente en nuestra terminal (en nuestro caso desde la Maquina2 que será la encargada de clonar la carpeta de la Maquina1 a ella misma):

    ssh-keygen -b 4096 -t rsa

Nos pedirá datos pero pulsaremos *enter* sin introducir nada. Una vez hecho ejecutamos este otro:

    ssh-copy-id 192.168.56.101

Una vez hecho esto ya podremos conectarnos a nuestro equipo sin contraseña mediante *ssh*.

## Programar tareas con crontab
Por último vamos a una tarea **cron** que se ejecute cada media hora para tener nuestro directorio */var/www/* actualizado en todo momento para ello tendremos que irnos a */etc/crontab* y actualizar el archivo añadiendo:

    */30 * * * * antonio rsync -avz -e ssh 192.168.56.101:/var/www/ /var/www/

Para comprobar que esto funciona correctamente vamos a añadir otra que actualice cada minuto para crear un archivo en la Maquina1 y ver que al cabo de un minuto ya está en la Maquina2.
    
    */1 * * * * antonio rsync -avz -e ssh 192.168.56.101:/var/www/ /var/www/
    
![imagen](https://github.com/Antobio17/swap1819/blob/master/practica2/Imagenes/crontab.png)

