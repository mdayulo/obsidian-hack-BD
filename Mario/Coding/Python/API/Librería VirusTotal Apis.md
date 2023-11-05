Hay una librería en Python que nos permite utilizar la API de virustotal.
## ANALIZAR ARCHIVO NO EXISTENTE EN LA BASE DE DATOS DE VIRUSTOTAL
En caso de que el archivo no se encuentre subido a la base de datos de virustotal, primero debemos utilizar otra librería de python para realizar primero esa subida y luego poder analizarlo. Por tanto con este código podemos subir un archivo desde Python a virustotal:
```python
import virustotal_python
import os.path

archivo = "prueba.py"
# Crea un diccionario que contenga el archivo para enviarlo para la codificación de varias partes
files = {"file": (os.path.basename(archivo), open(os.path.abspath(archivo), "rb"))}

with virustotal_python.Virustotal("API") as vtotal:
    resp = vtotal.request("files", files=files, method="POST")
    print(resp.json())
```
Aunque también podemos hacer este mismo código mucho más simple de esta forma:
```python
import virustotal_python

archivo = "prueba.py"
files = {"file": ((archivo), open((archivo), "rb"))}

with virustotal_python.Virustotal("API") as vtotal:
    resp = vtotal.request("files", files=files, method="POST")
    print(resp.json())
```
Y ya está subido:
![[Pasted image 20230417215638.png]]
También podríamos analizar varios archivos que no se encuentren en la base de datos de virustotal a la vez:
```python
import virustotal_python
from hashlib import md5
from virus_total_apis import PublicApi

# AQUÍ SUBIMOS LOS ARCHIVOS

archivos = ["prueba.py", "direcciones_ip.txt"]

for i in archivos:
    files = {"file": ((i), open((i), "rb"))}
    with virustotal_python.Virustotal("API") as vtotal:
        resp = vtotal.request("files", files=files, method="POST")

# A PARTIR DE AQUÍ ES DONDE OBTENEMOS EL REPORTE

API_KEY = "API" # Importamos la API de virustotal.
api = PublicApi(API_KEY)

for a in archivos:
    with open(a, "rb") as f:
        file_hash = md5(f.read()).hexdigest() # Convertimos en hash todo el contenido del archivo.
    response = api.get_file_report(file_hash) # Obtenemos la respuesta de virustotal
    print(response)
```
Y este es el resultado:
![[Pasted image 20230417220915.png]]
## ANALIZAR ARCHIVO EXISTENTE EN LA BASE DE DATOS DE VIRUSTOTAL
Si el archivo que queremos analizar ya se encuentra dentro de la base de datos de virustotal, podemos utilizar la librería virus_total_apis para hacer el análisis. Por tanto con este código podríamos conectarnos a virustotal y obtener el reporte de un archivo:
```python
from hashlib import md5
from virus_total_apis import PublicApi

API_KEY = "API" # Importamos la API de virustotal.
api = PublicApi(API_KEY)

with open("pruebas.py", "rb") as f:
    file_hash = md5(f.read()).hexdigest() # Convertimos en hash todo el contenido del archivo.
response = api.get_file_report(file_hash) # Obtenemos la respuesta de virustotal

print(response)
```
![[Pasted image 20230403141732.png]]
También podemos utilizar sentencias condicionales que evalúen los resultados en forma de diccionario que nos ofrece virustotal:
```python
import hashlib
from virus_total_apis import PublicApi

API_KEY = "API" # Importamos la API de virustotal.
api = PublicApi(API_KEY)

with open("pruebas.py", "rb") as f:
    file_hash = hashlib.md5(f.read()).hexdigest()
response = api.get_file_report(file_hash) 

# Aquí evalúa los resultados del análisis.

if response["response_code"] == 200: # Entramos en este if si el escaneo fue exito.
    if response["results"]["positives"] > 0: # Detecta que no haya ningún positivo, por lo que es malicioso.
        print("Archivo malicioso.")
    else:
        print("Archivo seguro.") # Si encuentra que es positivo se imprime esto.
else:
    print("No ha podido obtenerse el análisis del archivo.")
```
## DETECTAR LOS REPORTES QUE TIENE UN ARCHIVO
También podemos hacer que el script nos diga si un archivo tiene más de 10 o 15 reportes por ejemplo, simplemente accediendo al diccionario:
```python
import hashlib
from virus_total_apis import PublicApi

API_KEY = "API"
api = PublicApi(API_KEY)

with open("pruebas.py", "rb") as f:
    file_hash = hashlib.md5(f.read()).hexdigest()
response = api.get_file_report(file_hash) 

# Aquí evalúa los resultados del análisis.

if response["response_code"] == 200: 
    if response["results"]["positives"] >= 15: # Detecta que no haya ningún positivo, por lo que es malicioso.
        print("El archivo tiene 15 o más reportes.")
    else:
        print("El archivo tiene menos de 15 reportes.") 
else:
    print("No ha podido obtenerse el análisis del archivo.")
```
![[Pasted image 20230403152057.png]]
Ahora otro ejemplo con otro archivo que sí tiene más de 15 reportes:
```python
import hashlib
from virus_total_apis import PublicApi

API_KEY = "API" # Importamos la API de virustotal.
api = PublicApi(API_KEY)

with open("virus.exe", "rb") as f:
    file_hash = hashlib.md5(f.read()).hexdigest()
response = api.get_file_report(file_hash) 

# Aquí evalúa los resultados del análisis.

if response["response_code"] == 200: 
    if response["results"]["positives"] >= 15: # Detecta que no haya ningún positivo, por lo que es malicioso.
        print("El archivo tiene 15 o más reportes.")
    else:
        print("El archivo tiene menos de 15 reportes.") 
else:
    print("No ha podido obtenerse el análisis del archivo.")
```
![[Pasted image 20230403152032.png]]
## ANALIZAR UN HASH VIRUSTOTAL PYTHON
Si quisiéramos analizar un hash, simplemente tendríamos que proporcionarlo de esta forma:
```python
from virus_total_apis import PublicApi

API_KEY = "API" # Importamos la API de virustotal.
api = PublicApi(API_KEY)

hash_to_check = "81dc9bdb52d04dc20036dbd8313ed055" # Este es el hash que queremos analizar
response = api.get_file_report(hash_to_check) # Obtenemos la respuesta de virustotal

print(response)
```
Y esta es la respuesta:
![[Pasted image 20230417142230.png]]
