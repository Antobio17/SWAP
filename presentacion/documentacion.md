# Ataques Man-In-The-Middle

## Concepto de ataque MITM

Un ataque Man-in-the-middle (MITM) es un tipo de ataque, no neceseramiente en el ámbito de la informática, donde un cierto usuario altera la comunicación entre dos partes de manera secreta. Un ejemplo de este tipo de ataque dentro del ámbito de la seguridad informática, es el denominado 'eavesdropping' (escucha a escondidas) en el que el atacante intercepta los mensajes entre dos usuarios pudiendo insertar los que le convenga.

Este tipo de ataques no se detectan hasta que la transacción finaliza. Los objetivos más habituales de los ataques de este tipo son los sitios web de compras, banca o cualquier otra web en la que haya que introducir información sobre nuestra cuenta así como de nuestra tarjeta de crédito. En resumen, buscan obtener dinero de los usuarios afectados.

## Funcionamiento de los ataques de intermediario

1. ___Interceptación___: Lo primero que hacen los atacantes que quieren perpetrar un ataque de intermediario es interceptar el tráfico de Internet antes de que llegue a su destino. Para conseguirlo, existen varios métodos:

    1. **Suplantación de IP:** Igual que las bandas de ladrones de bancos ponen matrículas falsas en el coche que usan para escapar, la suplantación de IP consiste en que los hackers falsifican la fuente real de los datos que envían desde su equipo y la camuflan como si fuera una fuente de confianza. La idea es que cualquier usuario termine hablando con un impostor disfrazado de algo real.
    
    2. **Suplantación de ARP:** Para llevar a cabo este método, los hackers envían un ARP falso a una LAN para que la dirección del MAC del hacker pueda vincularse a su dirección IP y recibir toda la información del usuario afectado.
    
    3. **Suplantación de DNS:** Cuando se realiza una suplantación de DNS o un ataque de envenenamiento de la caché del DNS, los hackers acceden a esta caché y cambian las traducciones que hay en el mismo DNS, lo que se le redirige automáticamente a un sitio falso en lugar de al real al que se quería ir.
    
2. ___Descifrado___: Una vez que los hackers han interceptado el tráfico del sitio web, tienen que descifrarlo. Algunos de los métodos de descifrado habituales que se usan en los ataques MITM son los siguientes:

    1. **Suplantación de HTTPS:** Durante mucho tiempo, las letras HTTPS delante de una dirección de Internet significaban que se estaba en buenas manos. El HTTPS es la clave del certificado de un sitio web que indica que las transacciones en ese sitio están cifradas y que, por lo tanto, los datos están a salvo. Sin embargo, en un ataque MITM de HTTPS, un hacker puede instalar un certificado raíz de seguridad falso para que su navegador crea que es uno de confianza. Como el navegador confía en él, le proporciona la clave de cifrado necesaria para descifrar los datos que se envían y así, el hacker puede recibir y descifrar todo, leerlo, volver a cifrarlo y enviarlo a su destino. Haciendo esto, ni el usuario afectado ni el sitio web saben que se interceptó la comunicación. Así es cómo correos electrónicos y  chats en línea son leídos por los atacantes.
    
    2. **BEAST en SSL:** BEAST (Browser Exploit Against SSL/TLS) sobre SSL (Secure Sockets Layer) permite a los atacantes aprovechar los puntos débiles cifrado por bloques, pudiendo atrapar y descifrar los datos que se mueven entre un navegador y un servidor web.
    
    3. **Secuestro de SSL:** Un ataque de intermediario de SSL funciona así: cuando un usuario se conecta a un sitio web, el navegador se conecta primero a la versión HTTP (no segura) del sitio. El servidor le redirige a la versión HTTPS (segura) del sitio y el nuevo servidor seguro proporciona al navegador un certificado de seguridad conectándose. El secuestro de SSL se produce justo antes de que se conecte al servidor seguro. Los hackers cambian la ruta de todo su tráfico a su equipo para que su información (correos electrónicos, contraseñas, información de pago, etc.) les llegue a ellos primero.
    
    4. **Stripping de SSL:** Este ataque consiste en cambiar la categoría de un sitio web de HTTPS (segura) a HTTP (no segura). Un hacker usa un servidor proxy o la suplantación de ARP para situarse entre el usuario y una conexión segura, y le ofrece una versión no segura (HTTP) del sitio al que intenta acceder para que todos sus datos, contraseñas, pagos, etc., le lleguen en forma de texto descifrado y sin formato.
    

## Como defenderse de un ataque MITM

Es difícil de detectar mientras se produzcan, por la que mejor manera de mantenernos a salvo es realizar una buena prevención. 
Aun así, podemos detectar si se es o no víctima de un ataque de este tipo si el tiempo de carga de una página es repentinamente lento o si la URL pasa de ser HTTPS a HTTP. Sin embargo, es difícil de detectar un ataque de este estilo a tiempo real, por lo que es mejor usar las siguientes medidas de prevención:

* Activar la verificación de dos pasos en los servicios que lo permitan
* En la medida de lo posible, detectar y evitar estafas de phishing
* Evitar conectarse directamente a redes públicas
* Usar HTTPS para evitar que ataques menos sofistifcados intercepten las comunicaciones
* Usar redes VPN para establecer túneles de comunicación seguros

## Ejemplo teórico de ataque MITM

Supongamos que dos usuarios, Ana y Fran, intentan comunicarse entre sí, y mientras tanto un atacante anónimo trata de interceptar la conversación para escuchar y alterar el mensaje que recibe Fran.

En primer lugar, Ana le preguntará a Fran por su clave pública. El el caso de que el atacante la intercepte, entonces se prodrá realizar un ataque MITM. El atacante entonces, envía un mensaje falsificado a Fran que dice ser de Ana, pero sustituyendo la clave pública por la suya propia. Fran creerá que la clave es de Ana y cifrará su mensaje con la clave pública recibida enviando el mensaje cifrado a Ana. En este punto, el atacante interceptará de nuevo el mensaje descifrándolo con su clave privada. El atacante podría o no modificar el mensaje descifrado y posteriormente lo enviaría a Ana usando la clave pública de este último la cual había obtenido en la primera intercepción para cifrarlo. Así, cuando Ana reciba el mensaje, creerá que vino de Fran.

1. Ana envía un mensaje no cifrado a Fran, que es interceptado:

        [Ana] "Hola Fran, soy Ana. Dame tu clave pública." → [Atacante] [Fran]

2. El atacante reenvía este mensaje a Fran con su clave pública; Fran no puede decir que no es realmente de Ana:

        [Ana] [Atacante] → "Hola Fran, soy Ana. Dame tu clave pública." → [Fran]

3. Fran responde con su clave pública:

        [Ana] [Atacante] ← *Clave de Fran* ← [Fran]

4. El atacante reemplaza la clave de Fran por la suya, y transmite la clave a Ana, afirmando que es la clave de Fran:

        [Ana] ← *Clave del atacante* ← [Atacante] [Fran]

5. Ana encripta un mensaje con lo que cree que es la clave de Fran, pensando que sólo Fran lo puede leer, pero en realidad es la clave del atacante:

        [Ana] → "He recibido tu clave" → [Atacante] [Fran]

6. Sin embargo, debido a que en realidad el mensaje estaba cifrado con la clave del atacante, este puede descifrarlo, leerlo, si desea, modificarlo, y posteriormente cifrarlo con la clave de Fran, remitiéndoselo al mismo:

        [Ana] [Atacante] → "Emosido engañado" → [Fran]

Fran en todo momento creerá que este mensaje proviene de una comunicación segura de Ana.
        
Es por tanto necesario asegurar que las claves públicas no son alteradas o de lo contrario, este tipo de ataques son muy posibles, en principio, contra cualquier mensaje enviado utilizando la tecnología de clave pública. Afortunadamente, podemos usar las técnicas mencionadas anteriormente para ayudar a defenderse de los ataques MITM.
