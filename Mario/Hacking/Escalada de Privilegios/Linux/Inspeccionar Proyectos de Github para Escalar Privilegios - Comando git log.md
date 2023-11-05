Una vez dentro podemos inspeccionar la carpeta /opt, donde vemos que hay una carpeta que se llama my-app, por lo que suponemos que alguien estuvo desarrollando una aplicación y seguramente estaría haciendo commits a github de la misma:  
![Pasted image 20230308075022.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075022.png?1678258222476)  
Por tanto vamos a inspeccionar el commit más reciente con el siguiente comando, donde vemos un servicio que se llama consul.sh y un token que usaremos más adelante:  
![Pasted image 20230308075134.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075134.png?1678258294073)  
Por tanto vamos a buscar vulnerabilidades asociadas a este servicio; y encontramos un exploit en github:  
![Pasted image 20230308075224.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075224.png?1678258344245)  
![Pasted image 20230308075328.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075328.png?1678258408279)  
Vamos a compartir este exploit con la máquina víctima dentro del directorio /tmp para tener los permisos de escritura:  
![Pasted image 20230308075356.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075356.png?1678258436512)  
![Pasted image 20230308075443.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075443.png?1678258483654)  
Y ahora siguiendo las instrucciones del repositorio de github, tenemos que ejecutar esta herramienta, poniendo el token que hemos podido ver antes y nuesta IP de la máquina atacante y puerto donde estaremos escuchando con netcat: [0 - Consideraciones Previas > CONSEGUIR REVERSE SHELL DE MÁQUINA VÍCTIMA A NUESTRO EQUIPO](app://obsidian.md/0%20-%20Consideraciones%20Previas#CONSEGUIR%20REVERSE%20SHELL%20DE%20M%C3%81QUINA%20V%C3%8DCTIMA%20A%20NUESTRO%20EQUIPO)  
![Pasted image 20230308075857.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075857.png?1678258737743)  
Y ahora con netcat habremos recibido la conexión como el usuario root:  
![Pasted image 20230308075921.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075921.png?1678258761790)  
![Pasted image 20230308075941.png](app://local/C:/Users/mario/Documents/conocimientoo/conocimiento/conocimiento/images/Pasted%20image%2020230308075941.png?1678258781706)