Haremos los reconocimientos de siempre:
![[Pasted image 20230126111303.png]]
Ya que es una máquina Windows, debemos enumerar con crackmapexec qué máquina es y qué windows tiene instalado, el cual se trata de un Windows Server 2016: [[Crackmapexec#Detectar dominio y hostname con crackmapexec]]
![[Pasted image 20230126111311.png]]
Intentamos listar los recursos compartidos con una null session de esta máquina pero no sale nada:
![[Pasted image 20230126111320.png]]
Lo que podemos hacer es intentar enumerar usuarios válidos haciendo uso de la herramienta enum4linux:
![[Pasted image 20230126112406.png]]
![[Pasted image 20230126112436.png]]
Ahora que tenemos todos estos usuarios, vamos a guardarlos todos en un documento para poder usarlos como ataque de fuerza bruta, esto podemos hacerlo a través de expresiones regulares en bash o copiando y pegando cada usuario:
![[Pasted image 20230126113248.png]]
Y lo guardamos dentro del documento users:
![[Pasted image 20230126113324.png]]
Ahora que tenemos este listado de usuarios, hay una herramienta que se llama GetNPusers de impacket que sirve para obtener Ticket Granting Ticket (TGT) de credenciales de aquellos usuarios que no requieren autenticación Kerberos; por tanto debemos pasarle el dominio y el diccionario con los usuarios, y vemos que nos encuentra un hash de uno de los usuarios:
![[Pasted image 20230126114019.png]]
Ahora guardamos este hash en un documento de texto y con john intentamos deshashearla con un ataque de fuerza bruta utilizando el diccionario rockyou.txt:
![[Pasted image 20230126111407.png]]
Y tras ejecutar este comando, obtenemos una contraseña, la cual es s3rvice:
![[Pasted image 20230126114314.png]]
Ahora con crackmapexec podemos validar si estas credenciales son válidas, las cuales vemos que sí:[[Crackmapexec#Comprobar y validar credenciales con crackmapexec]]
![[Pasted image 20230126111426.png]]
Y con estas credenciales ya podemos listar recursos compartidos y los permisos que tenemos:[[Crackmapexec#Listar recursos compartidos con crackmapexec]]
![[Pasted image 20230126111434.png]]
Y ahora podemos ver si este usuario forma parte del grupo win managament users:
![[Pasted image 20230126111442.png]]
Ahora con toda esta información ya podemos ganar una consola interactiva utilizando las credenciales de este usuario utilizando evil-winrm:
![[Pasted image 20230126111450.png]]
Y navegando por los directorios, obtenemos la flag:
![[Pasted image 20230126111458.png]]
Podemos conocer detalles sobre este usuario con el comando net user svc-alfresco:
![[Pasted image 20230130110201.png]]
Ahora para escalar los privilegios podemos utilizar una herramienta llamada bloodhound y neo4j, que nos va a recopilar mucha información del sistema para elevar los privilegios. Por lo que primero iniciamos neo4j para que bloodhound pueda funcionar correctamente:
![[Pasted image 20230130111946.png]]
Ahora tenemos que configurar por primera vez la configuración de bloodhound, donde deberemos de configurar una password:
![[Pasted image 20230130112147.png]]
Le ponemos primero la password neo4j en la captura anterior y luego nos pedirá una password nueva, que en mi caso le puse 123123:
![[Pasted image 20230130112315.png]]
Ahora vamos a conectarnos a bloodhound con la nueva password de 123123:
![[Pasted image 20230130112357.png]]
![[Pasted image 20230130112440.png]]
Y ya tenemos el bloodhoudn cargado para subirle la data posteriormente para que nos la analice:
![[Pasted image 20230130112627.png]]
Ahora para obtener el archivo con los datos recopilados para luego poder subirlo a esta plataforma, debemos de utilizar un script, que por ejemplo podemos usar este:
![[Pasted image 20230130112849.png]]
Usaremos este script, que es el segundo enlace:
![[Pasted image 20230130112951.png]]
Nos lo bajamos:
![[Pasted image 20230130113033.png]]
Compartimos con impacket este archivo a través de un recurso compartido:
![[Pasted image 20230130114121.png]]
Y ahora nos lo bajamos en la máquina víctima:
![[Pasted image 20230130114409.png]]
![[Pasted image 20230130114423.png]]
Ahora ya podremos recopilar toda la info del sistema ejecutando un módulo de este script que es el siguiente:
