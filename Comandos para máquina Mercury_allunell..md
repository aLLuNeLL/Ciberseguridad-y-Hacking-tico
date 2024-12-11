
Hacemos un netdiscover -r 10.0.22.0/24

![[Pasted image 20241021152647.png]]

Realizamos un nmap para descubrir vulnerabilidades.

![[Pasted image 20241021154336.png]]

Ahora accedemos por web y por el puerto a ver si encontramos algo.

![[Pasted image 20241021154852.png]]

Miramos por robots.txt

![[Pasted image 20241021154924.png]]

Nos dice que la web en teoría está deshabilitada. Nos da una información clara: el **user-agent**. Provocaremos un error con ese error del asterisco.

![[Pasted image 20241021155228.png]]

Probando las url, con la **mercuryfacts/** 

![[Pasted image 20241021155356.png]]

Probando con el **Load a fact**, cargaremos datos.

![[Pasted image 20241021155447.png]]

Pero veremos que no hay datos infinitos:

![[Pasted image 20241021155516.png]]

Miramos la lista:

![[Pasted image 20241021155544.png]]

Esto nos indica que existe una tabla de usuarios.

Entendiendo lo ya visto, podemos saber que hay una base de datos funcionando por debajo. Necesitamos saber que tipo de base de datos es y si realmente existe.

Probamos una nueva herramienta: **mysqlmap**

![[Pasted image 20241021160606.png]]

- sqlmap: herramienta.
- -u: Indicamos que le vamos a pasar una URL.
- URL.
- -dbs: BBDD.
- --batch: no molestar.

Ejecutamos.
Nos devuelve estas 2 BBDD:

![[Pasted image 20241021161012.png]]

Ahora queremos ejecutar para buscar la información dentro de la base de datos. la information_schema la descartamos, ya que es la BBDD que contiene toda la configuración del motor. Así que vamos con la **mercury**.

![[Pasted image 20241021161106.png]]

Ejecutamos:
Nos devuelve lo siguiente.
![[Pasted image 20241021161220.png]]

Nos fijamos que hay 2 tablas.

Nos interesa el usuario **webmaster** con su contraseña.

Ahora haremos un ssh contra el usuario.

![[Pasted image 20241021161347.png]]


Haremos un ls -l para encontrar todo lo que hay dentro:

![[Pasted image 20241021161423.png]]

Encontramos la primera bandera, y luego encontramos otro directorio.

![[Pasted image 20241021161612.png]]
Primera bandera.

Entramos:

![[Pasted image 20241021161509.png]]

Encontramos un bloque de notas llamado **notes.txt**.

Lo visualizamos:

![[Pasted image 20241021161547.png]]

Los 2 iguales del final nos indica que es un base64. Así que vamos a traducir la contraseña:

![[Pasted image 20241021161754.png]]

Ejecutamos:

![[Pasted image 20241021161816.png]]

Ahora vamos a cambiar de usuario.

![[Pasted image 20241021162016.png]]

Miramos directorios.

![[Pasted image 20241021162103.png]]

Al estar vacío, y no tener permiso de root (![[Pasted image 20241021162141.png]]) , vamos a encontrar la carpeta compartida con el root:

![[Pasted image 20241021162217.png]]

Encontraremos una variable de entorno, siendo esta el **SETENV**.
Haremos a leer el archivo:

![[Pasted image 20241021162319.png]]

**Enlace simbólico**: Entra en juego 2 parámetros, **variable de entorno (ENV)** o un **programa/archivo**.
vi/vim: editores de texto que nos permiten dentro ejecutar instrucciones de shell. 

ENV PATH --> ENV Vim

ln: instrucción que nos permite trabajar con enlaces simbolicos.
-s: nos permite editar.

![[Pasted image 20241021162941.png]]

Con esta instrucción, se pide que se sitúe en la línea número 5 de ese programa.

![[Pasted image 20241021163120.png]]

Con esta instrucción, creas un enlace de entorno.

Exportaremos la variable de entorno, con una instrucción que la meta en PATH, que ahora PATH=/usr/bin/vim.

![[Pasted image 20241021163441.png]]

Con el siguiente código, pedimos que se preserve la variable de entorno guardada en PATH.

![[Pasted image 20241021163818.png]]

Dentro, escribimos: **:!/bin/bash** y presionamos enter. Con esto, se nos va a mantener un root.

**SQLMAP**: extracción e interpretación de bases de datos.

**ENV**: variables de entorno, donde las podemos explotar.

![[Pasted image 20241021163913.png]]

Una vez aquí, buscamos la otra bandera y finalizamos la máquina:

![[Pasted image 20241021164650.png]]

Comandos para encontrar la segunda bandera:

- cd ..
- cd ..
- ls
- cd root
- ls
- cat root_flag.txt

