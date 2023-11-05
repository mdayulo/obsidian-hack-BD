# PREPARATIVOS
Vamos a tener un servidor DHCP con Ubuntu Server donde va a tener como cliente una máquina Windows 11; los dos bajo una red interna de virtualbox:
![[Pasted image 20230115151052.png]]
Y en la máquina servidor utilizaremos este comando para cambiar la IP del servidor Linux, indicando el adaptador que queramos cambiar:
![[Pasted image 20230115151201.png]]
Y ahora en la máquina Windows vamos a poner una IP dentro del rango de la IP del servidor, para que haya conectividad:
![[Pasted image 20230115153912.png]]
![[Pasted image 20230115153852.png]]
Y ahora veremos como desde la máquina Linux no podemos enviar un PING a la Windows; y esto ocurre porque el firewall está impidiendo que hagamos peticiones ICMP a la máquina Windows, por lo que crearemos una nueva regla en el firewall:
![[Pasted image 20230115154109.png]]
Crearemos una nueva regla:
![[Pasted image 20230115154203.png]]
Aceptando peticiones del protoclo ICMP:
![[Pasted image 20230115154231.png]]
Y le ponemos por ejemplo este nombre:
![[Pasted image 20230115154304.png]]
Y ahora si hacemos un PING desde el servidor a la máquina Windows, veremos que ya tenemos total conectividad:
![[Pasted image 20230115154359.png]]
Ahora por último será configurar una IP estática en Linux utilizando este comando para editar el fichero de configuración:
![[Pasted image 20230115182059.png]]
Y dejamos esta configuración con la IP que queramos dentro del adaptador que hayamos elegido configurar una IP estática:
![[Pasted image 20230115182413.png]]
Y ahora con el comando sudo netplan apply aplicaremos estos cambio:
![[Pasted image 20230115182441.png]]
Y ya tenemos una IP estática en nuestra máquina:
![[Pasted image 20230115182520.png]]
# CONFIGURACIÓN SERVIDOR DHCP
Lo primero será abrir el fichero de configuración de DHCP:
![[Pasted image 20230115182611.png]]
Y en este punto tenemos que poner el nombre de la interfaz que queramos editar, que en este caso es enp0s3:
![[Pasted image 20230115182715.png]]
Y ahora tendremos que editar este otro fichero que será donde estableceremos los ajustes necesarios para nuestro servidor DHCP:
![[Pasted image 20230115182841.png]]
![[Pasted image 20230115182903.png]]
Y en la parte de abajo establecemos esta configuración; donde pondremos un grupo que por ejemplo será grupo red windows, luego ponemos la IP acabada en 0 para representar la totalidad de equipos y su submáscara de red. Más tardes ponemos la frecuencia por la que se aplicará esta configuración, y luego ponemos la dirección de la puerta de enlace donde pone option routers; y por último el servidor DNS:
![[Pasted image 20230115183731.png]]
Ahora guardamos este fichero y reiniciamos el servicio DHCP:
![[Pasted image 20230115183959.png]]
Y con el comando systemctl status isc-dhcp-server veremos que se encuentra activo:
![[Pasted image 20230115184050.png]]
Ahora vamos a la máquina Windows que la tenemos sin conexión y sin ninguna IP configurada:
![[Pasted image 20230115184200.png]]
Por lo que vamos a abrir el menú de centro de redes y recursos compartidos donde podremos configurar conectarnos a un servidor DHCP:
![[Pasted image 20230115184602.png]]
Y tras dejar activado que nos den una IP utilizando el servidor DHCP, vemos que si utilizamos un ipconfig en la terminal veremos que nos ha asignado una IP comprendida entre el 10 y 200 que configuramos anteriormente en el fichero de configuración de DHCP en el servidor de Linux, además de habernos dado correctamente la IP de la puerta de enlace:
![[Pasted image 20230115184721.png]]

