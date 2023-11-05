

```bash
#!/bin/bash

# Verifica que se proporcionen dos argumentos: el diccionario y la URL
if [ $# -ne 2 ]; then
    echo "Uso: $0 <diccionario> <URL>"
    exit 1
fi

diccionario="$1"
url="$2"

# Verifica si el diccionario existe
if [ ! -f "$diccionario" ]; then
    echo "El diccionario '$diccionario' no existe."
    exit 1
fi

# Itera sobre cada línea en el diccionario
while read -r linea; do
    # Concatena la línea del diccionario con la URL y realiza una solicitud HTTP
    respuesta=$(curl -s -o /dev/null -w "%{http_code}" "$url/$linea")

    # Verifica si el código de respuesta es 200 (OK)
    if [ "$respuesta" -eq 200 ]; then
        echo "URL accesible: $url/$linea"
    fi
done < "$diccionario"
```
![[Pasted image 20230927130534.png]]