#### Per començar, farem un escaneig del rang de la xarxa, en el meu cas, amb un `netdiscover -r 10.0.22.0/24` 
![[Pasted image 20241111153059.png]]

#### En aquest cas, no caldria ja que la mateixa màquina ja ens dona la direcció IP.
![[Pasted image 20241111153111.png]]

#### Seguidament, farem un `nmap -A (ip) -T5`. Això ens mostrarà la informació sobre els ports vulnerables de la màquina.
![[Pasted image 20241111153344.png]]

#### Trobem que el port 21 d'ftp està obert, i que podem accedir com a usuari **anonymous**.
![[Pasted image 20241111153358.png]]
![[Pasted image 20241111153641.png]]

#### Fem un `ls` per a llistar els continguts que hi ha al directori.
![[Pasted image 20241111153703.png]]

#### Trobem un arxiu anomenat **secret.txt**. Farem un `get` per importar l'arxiu a la màquina amfitrió.
![[Pasted image 20241111153719.png]]
#### Sortirem amb un `exit`.

#### Per veure el contingut de l'arxiu importat, farem un `cat secret.txt` per llistar el contingut
![[Pasted image 20241111153735.png]]

#### Però trobarem que aquest contingut no ens serveix de res. Per això:

## **NOU MÈTODE**

#### Quan hem fet l'nmap, al port 80 està obert, i hi ha una web. Llavors, guardarem a `/etc/hosts` la direcció ip amb l'url que visualitzem.
![[Pasted image 20241111160153.png]]
![[Pasted image 20241111160201.png]]

#### Seguidament, buscarem al navegador l'url i ens apareixerà aquesta pantalla.
![[Pasted image 20241111160237.png]]

#### Visualitzant la pantalla, trobarem que hi ha un usuari "admin".
![[Pasted image 20241111160718.png]]

#### Buscarem en una altre pestanya posibles contrasenyes per defecte per poder accedir com a admin. Fent búsqueda, trobarem que hi ha una funcional: **password**. 
Posteriorment, accedirem a la pantalla de login.
![[Pasted image 20241111161734.png]]

#### Fent una altre búsqueda, trobem que la versió es vulnerable a un **reverseshell** accedint per l'apartat de "*themes*". Per això, accedim al següent link de github i copiem el codi, cambiant la ip per la ip de l'equip vulnerable.
- #### Github: https://github.com/WireSeed/exploits/blob/main/php-reverse-shell/php-reverse-shell.php

![[Pasted image 20241111162242.png]]
![[Pasted image 20241113190345.png]]

#### Posteriorment, comprimim l'arxiu .php a un zip.
![[Pasted image 20241111162258.png]]

#### Un cop ho tinguem comprimit, pujarem l'arxiu .zip a l'apartat de *"themes"*.
![[Pasted image 20241111162442.png]]

#### Obrirem una escolta al port 1234 per obrir una consola.
![[Pasted image 20241113191822.png]]

#### Un cop estigui pujat, accedirem a l'url `papaya.thl/themes/test`, i apareixierà la següent pantalla. Donem click a sobre de la part de *php-reverse-shell.php*
![[Pasted image 20241111163457.png]]

#### Un cop li haguem donat click, si tot ha anat correctament, s'obrirà la consola. Fent un `ls` ens llista els directoris.
![[Pasted image 20241111165345.png]]

#### Ens fixarem en el directori **opt**. Aquest directori guarda programes opcionals. Accedirem al directori i farem un `ls` per llistar els continguts. Trobem un arxiu anomenat **pass.zip**. Intentarem importar l'arxiu, però com no som root, no podem rebre l'arxiu. Per això, buscarem una altre manera.
#### Ho probarem amb python3. Executarem `export TERM=XTERM` i després `python3 -c 'import pty; pty.spawn("/bin/bash")'` 
![[Pasted image 20241111164306.png]]

#### En una consola nova, farem un `wget http://10.0.22.10:8021/pass.zip`. D'aquesta manera, obtindrem l'arxiu.
![[Pasted image 20241111164326.png]]

#### Movem l'arxiu a una carpeta, en el meu cas, una carpeta amb el nom de la màquina per facilitar el treball.

![[Pasted image 20241111164346.png]]
#### La següent comanda, el **zip2john**, extreu el hash d'un arxiu ZIP protegit amb contrasenya per trencar-lo amb John the Ripper.
![[Pasted image 20241111164400.png]]
#### Farem servir el john per trencar la contrasenya de l'arxiu utilitzant una llista de paraules.
![[Pasted image 20241111164414.png]]
#### Ara ja tindrem l'usuari _**papaya**_ amb la contrasenya _papayarica_. Entrarem per SSH. A més, trobarem la flag de user.
![[Pasted image 20241111164426.png]]
#### Trobarem la primera *flag* de l'usuari al mateix directori.
![[Pasted image 20241111170743.png]]

#### Com no tindrem permisos de *root*, hem de buscar alguna carpeta compartida amb root per poder fer un "exploit" i accedir com a tal. Trobarem una carpeta compartida on no es necessari posar la contrasenya de root.
![[Pasted image 20241111164544.png]]
#### Buscarem a GTFOBINS la carpeta **scp** per sudo par a accedir com a root.
![[Pasted image 20241111164535.png]]
#### Executem les comandes anteriors per aconseguir ser *root*.
![[Pasted image 20241111164653.png]]

#### Un cop dins de root, buscarem l'altre *flag*.
![[Pasted image 20241111164720.png]]

## FI