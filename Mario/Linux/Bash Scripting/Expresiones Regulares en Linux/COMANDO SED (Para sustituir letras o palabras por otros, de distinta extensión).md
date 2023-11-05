Por ejemplo vamos a sustituir la palabra Hola por la palabra Mario con el comando sed:
![[Pasted image 20230128181636.png]]
Incluso también podemos sustituir un espacio por lo que queramos, por ejemplo donde había un espacio ponemos un guión:
![[Pasted image 20230128181721.png]]
Lo que pasa que sólo nos sustituye con la primera coincidencia, por lo que si queremos hacer una sustitución en todo el documento debemos poner una 'g' al final:
![[Pasted image 20230128181813.png]]
También puedo eliminar o modificar el último elemento de una cadena de texto, escribiendo sed ‘s/.$//’
![[Pasted image 20230515145932.png]]