
Descargar de dockerlabs.es el archivo de breakmyssh.zip: https://dockerlabs.es/

Mover a una carpeta:
![[Pasted image 20241113154536.png]]

Instalar docker.io
![[Pasted image 20241113171216.png]]

Instalar la máquina:
![[Pasted image 20241113154733.png]]

Probar el ping:
![[Pasted image 20241113154906.png]]

Hacer nmap:
![[Pasted image 20241113155147.png]]
![[Pasted image 20241113155200.png]]

Vemos que está el puerto 22 abierto, del **ssh**.

Probaremos con un **hydra**.
![[Pasted image 20241113155710.png]]
Nos saldrá una contraseña:
![[Pasted image 20241113171450.png]]
Antes de que acabe de ejecutar, probaremos con otro método.

Tiramos otro nmap:
![[Pasted image 20241113155917.png]]
![[Pasted image 20241113155926.png]]

Buscaremos por el **cve-mitre** la versión:
![[Pasted image 20241113160104.png]]

Nos sale 2 posibles vulnerabilidades.

Miraremos el segundo: **CVE-2018-15473.**

Lo buscaremos por github:
![[Pasted image 20241113160317.png]]

Probaremos el primero. **No lo podemos usar ya que se trata de un docker**.

Probaremos el tercero: https://github.com/r3dxpl0it/CVE-2018-15473
![[Pasted image 20241113160611.png]]
![[Pasted image 20241113160654.png]]

Entramos:
![[Pasted image 20241113161208.png]]

Ejecutamos el comando que dice en el github:
![[Pasted image 20241113161231.png]]
![[Pasted image 20241113161345.png]]
No funciona, así que probaremos otro: https://github.com/Sait-Nuri/CVE-2018-15473

Borraremos la anterior e instalaremos este:
![[Pasted image 20241113162608.png]]

Instalaremos el "requirements.txt":
![[Pasted image 20241113162713.png]]
Daremos los permisos necesarios:
![[Pasted image 20241113164314.png]]
![[Pasted image 20241113164323.png]]
Ejecutamos el comando:
![[Pasted image 20241113164336.png]]
Al final, nos dará una lista de posibles usuarios:
![[Pasted image 20241113171608.png]]

Con los usuarios creamos una librería para después usarla para otro hydra.
![[Pasted image 20241113164350.png]]

Probaremos otro hydra usando la librería:
![[Pasted image 20241113163828.png]]
![[Pasted image 20241113163843.png]]

Entraremos por ssh por root:
![[Pasted image 20241113164421.png]]

Probaremos con la contraseña encontrada:
![[Pasted image 20241113170743.png]]

Ya estaríamos dentro.
