Esta librería de python sirve para establecer una barra de progreso dentro de un script, de la siguiente manera:
```python
from tqdm import tqdm
import time

# Crear una barra de progreso para un bucle for
for i in tqdm(range(10)):
    time.sleep(0.1)
```
![[Pasted image 20230917121851.png]]
