Para detectar equipos o hosts conectados a mi red privada lo haría de la siguiente forma:
```bash
#!/bin/bash

for i in {1..255}; do
    timeout 1 bash -c "ping -c 1 192.168.0.$i" >/dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "El host 192.168.0.$i está activo"
    fi
done
```
Y este sería el resultado:
![[Pasted image 20230414123535.png]]
