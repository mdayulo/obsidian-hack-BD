**DETECTAR VERSIÓN DE WORDPRESS**

En wordpress existe una etiqueta que si la buscamos dentro del código fuente de la página nos dice qué versión se trata:

![[Pasted image 20221217093426.png]]

Pero también podemos detectar la versión de wordpress con python:

![[Pasted image 20221217093444.png]]
![[Pasted image 20221217093452.png]]
**PARA ENUMERAR LOS USUARIOS DE WORDPRESS**
Hay una ruta dentro de la dirección de una web de wordpress que nos permite enumerar los usuarios; y es la ruta /wp-json/wp/v2/users:
![[Pasted image 20221217093509.png]]
Y nos lo encuentra:
![[Pasted image 20221217093519.png]]

Vamos a hacer esto desde Python:
![[Pasted image 20221217093535.png]]
![[Pasted image 20221217093540.png]]

