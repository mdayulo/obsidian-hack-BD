Vamos a tener el siguiente mapa de red con estas máquinas:
![[Pasted image 20230724191824.png]]
El objetivo va a ser llegar el puerto 80 de la máquina windows 10 final, por tanto, configuramos la red de la misma forma que hicimos en la parte de [[Pivoting con Metasploit Linux]], de tal forma que una vez hecho eso, creamos un servicio HTTP montado con python en la máquina Windows 10 objetivo final:
![[Pasted image 20230724194308.png]]
A continuación, debemos obtener una sesión de meterpreter de la máquina intermedia windows 7, por lo que explotamos un eternalblue y obtenemos una sesión de meterpreter:
![[Pasted image 20230724201944.png]]
Una vez dentro podemos consultar las interfaces de red con el comando ipconfig:
![[Pasted image 20230726093617.png]]
Ahora, pulsamos control + Z:
![[Pasted image 20230724202214.png]]
Y usamos el siguiente módulo:
```bash
use post/multi/manage/autoroute
```
![[Pasted image 20230726093809.png]]
Nos pedirá que pongamos la sesión de la máquina windows 7:
![[Pasted image 20230726093844.png]]
Y obtendremos esta salida:
![[Pasted image 20230726094217.png]]
Con el comando route podemos ver las dos direcciones entrelazadas:
![[Pasted image 20230726094244.png]]
Ahora simplemente tenemos que hacer el port forwarding y ya lo tendremos:
```bash
portfwd add -l 5000 -p 80 -r 20.20.20.6
```
![[Pasted image 20230717190710.png]]
Desde nuestra máquina Kali veremos el puerto 80 de la máquina windows 10 final:
![[Pasted image 20230726094659.png]]
## OPCIÓN 2 CON PORTPROXY
Para este ejemplo, desde metasploit tenemos que usar un módulo llamado portproxy:
```bash
use post/windows/manage/portproxy
```
El cual nos pide configurar lo siguiente:
![[Pasted image 20230724202259.png]]
Una vez dentro de este módulo, tendremos que configurar la dirección de la máquina intermedia Windows 7 como de la máquina objetivo final Windows 10, utilizando estos parámetros:
```bash
set CONNECT_ADDRESS 10.10.10.9 # IP máquina objetivo final.
set CONNECT_PORT 80 # Puerto de la máquina objetivo.

set LOCAL_ADDRESS 0.0.0.0
set LOCAL_PORT 5000 # Esto va a correr en la máquina atacante el puerto 445 de la máquina objetivo final.
```
Lo configuramos:
![[Pasted image 20230724202419.png]]
Y por último pondremos la sesión donde esté corriendo el windows 7 de meterpreter:
![[Pasted image 20230724202453.png]]
Lo ejecutamos y ya tenemos la regla configurada:
![[Pasted image 20230724202516.png]]
Y ahora si ponemos el puerto correspondiente y la IP de la máquina windows 7 intermedia, estaremos accediendo al puerto 80 de la máquina objetivo final windows 10:
![[Pasted image 20230726095611.png]]
Podemos eliminar estas tablas de enrutamiento con el comando route flush:
![[Pasted image 20230726095824.png]]
# EJEMPLO ATAQUE PIVOTING
Vamos a atacar a la máquina objetivo final explotando un eternalblue usando pivoting en el siguiente escenario:
![[Pasted image 20230726100815.png]]
El primer paso será levantar un servicio en el puerto 80 con python dentro de la máquina final windows 7, pero al tratarse de python 2 (ya que python 3 no es compatible), tendremos que ejecutar el siguiente comando:
![[Pasted image 20230726101914.png]]
Nos establecemos con una sesión de meterpreter en la máquina intermedia:
![[Pasted image 20230727094322.png]]
Para este ejemplo, desde metasploit tenemos que usar un módulo llamado portproxy:
```bash
use post/windows/manage/portproxy
```
El cual nos pide configurar lo siguiente:
![[Pasted image 20230724202259.png]]
Una vez dentro de este módulo, tendremos que configurar la dirección de la máquina intermedia Windows 7 como de la máquina objetivo final Windows 10, utilizando estos parámetros:
```bash
set CONNECT_ADDRESS 10.10.10.11 # IP máquina objetivo final.
set CONNECT_PORT 445 # Puerto de la máquina objetivo.

set LOCAL_ADDRESS 0.0.0.0
set LOCAL_PORT 5000 # Esto va a correr en la máquina atacante el puerto 445 de la máquina objetivo final.
set SESSION 1
```
Lo configuramos:
![[Pasted image 20230727101405.png]]
Y por último pondremos la sesión donde esté corriendo el windows 7 de meterpreter:
![[Pasted image 20230724202453.png]]
Lo ejecutamos y ya tenemos la regla configurada:
![[Pasted image 20230727101626.png]]
De tal forma que si ahora atacamos al puerto 5000 de la máquina intermedia, estaríamos atacando al puerto 445 de la máquina objetivo final:
![[Pasted image 20230727101516.png]]
![[Pasted image 20230727101527.png]]
Y vemos que la IP es correcta:
![[Pasted image 20230727101542.png]]
Incluso también podríamos hacer un escaner con nmap de este puerto:
```bash
nmap -p 5000 --open -sS -sC -sV --min-rate 2500 -n -vvv -Pn 192.168.0.48
```
![[Pasted image 20230729120041.png]]
