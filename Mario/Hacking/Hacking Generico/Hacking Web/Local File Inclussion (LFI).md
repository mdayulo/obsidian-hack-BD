### DIFERENCIAS LFI Y ARBITRARY FILE UPLOAD

la vulnerabilidad de Arbitrary File Upload se refiere a la capacidad del atacante para cargar un archivo malicioso en un servidor web, mientras que la vulnerabilidad de Local File Inclusion se refiere a la capacidad del atacante para incluir un archivo malicioso que ya existe en el servidor web.

--------------------------------------------

Esta vulnerabilidad consiste en poder acceder a archivos internos de la máquina, donde funciona principalmente con webs hechas en php. Por ejemplo, si estamos ante una web o un CMS vulnerable como en el siguiente caso, podemos ver que si vemos un exploit de esta vulnerabilidad nos explican como acceder a los archivos internos de la máquina:
![[pasted image 0 3.png]]
Vamos a examinar este exploit, donde vemos que se aprovecha de una vulnerabilidad de local file inclusion, lo que significa que podemos acceder a archivos locales de la máquina:
![[pasted image 0 4.png]]
Vamos a copiar y pegar esa línea y probemos cómo funciona, y si lo pegamos en el navegador accedemos al archivo que queramos, que en mi caso lo modifiqué y puse para ver el fichero etc/passwd:
![[pasted image 0 5.png]]
Pero lo interesante es acceder con el mismo comando del exploit, donde veremos que tenemos acceso a unas credenciales, donde presionamos CONTROL+U y veremos la información estructurada:
![[pasted image 0 6.png]]
Ahora con estas credenciales podremos iniciar sesión en muchos sitios.
## ADQUIRIR WP-CONIG.PHP DE WOPRDPRESS
Si encontramos un LFI dentro de un wordpress, podemos obtener el fichero de configuración donde se almacenan las credenciales llamado wp-config.php; y para ello tenemos que usar un wrapper (explotando el plugin que corresponda):
```bash
http://10.10.6.206/wordpress/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=php://filter/convert.base64-encode/resource=../../../../../wp-config.php
```
![[Pasted image 20230826170029.png]]
