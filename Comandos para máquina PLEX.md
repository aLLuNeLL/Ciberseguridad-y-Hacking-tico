Primero de todo, al iniciar la máquina, ya nos dará la dirección ip.
![[Pasted image 20241111191357.png]]

Tiraremos un nmap para encontrar una vulnerabilidad por script.
![[Pasted image 20241111184440.png]]

Probaremos el acceso por ftp, ya que el puerto está abierto, pero al entrar, nos saldrá que no estamos conectados.
![[Pasted image 20241111184506.png]]

Para probar otra manera, iniciaremos un servicio http por el puerto de la ip.
![[Pasted image 20241111184542.png]]

Una vez tengamos el servicio iniciado, haremos un **gobuster** con los parámetros que se ven a continuación y la librería en cuestión.
![[Pasted image 20241111185738.png]]

Veremos que existen unos servicios activos. Vamos a probar con la ruta de **robots.txt**, donde nos dará otro parámetro.
![[Pasted image 20241111185746.png]]

Volveremos a hacer el curl anterior, pero modificando la ruta con la nueva que nos ha salido
![[Pasted image 20241111185756.png]]

Cuando ejecutamos el curl, veremos una serie larga cifrada.
![[Pasted image 20241111185807.png]]

Con la serie anterior, intentaremos descifrarla, pero nos va a saltar un error.
![[Pasted image 20241111185817.png]]

El error es porque la serie anterior está separada por puntos:
- Hasta el primer punto es el "head".
- Desde el primer punto hasta el segundo es la carga.
- A partir del segundo punto hasta el final es el "form".

Por lo tanto, solo necesitamos la carga del medio para poder descifrar la serie.
![[Pasted image 20241111190325.png]]

El **jq** te lo hace para ponerlo más acorde o "bonito".

Haremos una conexión por ssh con el usuario y la contraseña anterior por el puerto 21 aprovechando que estaba abierto.
![[Pasted image 20241111190509.png]]

Una vez dentro, deberemos hacer la escalada de privilegios. Por ello, con el comando **`sudo -l`** buscaremos la carpeta compartida entre un usuario sin privilegios y el usuario root de la máquina.
![[Pasted image 20241111190953.png]]

Buscaremos información sobre **mutt**, donde encontraremos que podremos ejecutar el programa como una shell:
B
![[Pasted image 20241111191015.png]]
![[Pasted image 20241111191053.png]]
Una vez entremos con el comando anterior, sabiendo que se puede usar como una shell,  ejecutamos ! y luego /bin/bash. De esta manera, podremos entrar a root.
![[Pasted image 20241111191115.png]]
![[Pasted image 20241111191137.png]]
![[Pasted image 20241111191153.png]]
**YA SOMOS ROOT**

Para la primera flag, en la carpeta /home/mauro encontraremos la flag de usuario.
![[Pasted image 20241111191220.png]]

Para la segunda flag, indagaremos un poco más. La encontraremos en el directorio de root.
![[Pasted image 20241111191244.png]]
![[Pasted image 20241111191251.png]]
