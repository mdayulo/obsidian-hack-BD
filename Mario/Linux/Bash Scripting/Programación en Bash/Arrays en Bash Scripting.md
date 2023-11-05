Es como si fuera una lista en python, pero su similar en bash; y aquí un ejemplo:
```bash
#!/bin/bash

# Declaración de un array
frutas=("manzana" "naranja" "plátano" "uva")

# Accediendo a elementos del array
echo "La primera fruta es ${frutas[0]}"
echo "La tercera fruta es ${frutas[2]}"

# Modificando un elemento del array
frutas[1]="limón"
echo "La segunda fruta es ${frutas[1]}"

# Añadiendo elementos al array
frutas+=("mango" "papaya")
echo "Las frutas disponibles son: ${frutas[@]}"

# Recorriendo el array con un bucle for
echo "Recorriendo el array con un bucle for:"
for fruta in "${frutas[@]}"; do
    echo "- $fruta"
done
```
Y este sería el resultado:
![[Pasted image 20230414120544.png]]
Otro ejemplo de array sería el siguiente, donde guardamos en una lista todos los archivos del actual directorio:
```bash
#!/bin/bash

archivos=$(ls)

for archivo in $archivos; do
        echo $archivo
done
```
Y este sería el resultado:
![[Pasted image 20230817111758.png]]
También podemos integrar esto con un bucle for, de tal forma que podremos saber el tamaño de cada uno de los archivos del escritorio:
```bash
#!/bin/bash

archivos=$(ls)

for archivo in $archivos; do
    espacio_archivo=$(du -m "$archivo" | awk '{print $1}')
    echo "El archivo $archivo ocupa $espacio_archivo megabytes"
done
```
![[Pasted image 20230817113039.png]]
## AÑADIR ELEMENTOS A UNA LISTA A PARTIR DE UN BUCLE FOR
También podemos añadir elementos a una lista vacía a partir de un bucle for de la siguiente forma:
```bash
#!/bin/bash

cosas=("hola" "que tal" 5)
lista=("${cosas[@]}")

for i in "${lista[@]}"
do
  echo "$i"
done
```
Y este es el resultado:
![[Pasted image 20230613220036.png]]

