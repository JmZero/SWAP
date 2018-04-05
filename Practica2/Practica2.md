# Práctica 2 - Clonar la información de un sitio web

En esta segunda práctica el objetivo es configurar las máquinas virtuales para trabajar en modo espejo, consiguiendo que una máquina secundaria mantenga siempre actualizada la información que hay en la máquina servidora principal.

### 1. Crear un tar con ficheros locales en un equipo
Para llevar a cabo este apartado lo primero que debemos hacer es crear un archivo **tar** de algunos ficheros locales de una de las máquinas, y haciendo uso de ssh pasarlos a otra.

Unicamente requeriremos del siguiente comando: `tar czf - [directorio] | ssh [nombre_usuario]@[ip_máquina] 'cat > ~/tar.tgz'`

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/tar1.png"> </p>

Una vez ejecutado el comando en la primera máquina procederemos a comprobar que en la segunda se ha creado dicho archivo y si su contenido corresponde con el que hemos comprimido de la primera máquina.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/tar2.png"> </p>

Como vemos todo ha funcionado correctamente.

### 2. Instalar la herramienta rsync
Lo primero que tendremos que hacer es instalar **rsync** en ambas máquinas haciendo uso del comando `sudo apt-get install rsync`
Tras esto podremos trabajar en modo usuario sin ningún problema.
Como vamos a trabajar haciendo uso de la carpeta **/var/www/**, que es donde residen los archivos del espacio web, tendremos que hacer dueño de la misma al usuario mediante el comando `sudo chown [nombre_usuario]:[grupo_usuario] –R /var/www`.

Para probar el funcionamiento de **rsync** vamos a realizar una copia de los archivos dentro del directorio **/var/www/** de nuestra primera máquina al mismo directorio de la segunda. Para ello usaremos el comando `rsync -avz -e ssh [nombre_usuario]@[ip_máquina]:/var/www/ /var/www/` en la segunda máquina (ya que es donde queremos copiarlos). Una vez ejecutado nos pedirá la introducción de la contraseña del usuario de la primera máquina para poder acceder a ella (ya que estamos usando ssh y necesitamos identificarnos).

Ahora realizaremos una comprobación para asegurarnos de que se han copiado todos los archivos y directorios de la primera máquina en la segunda.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/rsync.png"> </p>

En la imagen anterior podemos comprobar el correcto funcionamiento del proceso. Dado que contamos con un archivo **hola.html** en cada máquina, cada uno con un contenido diferente, se muestra en primer lugar el contenido del archivo en la segunda máquina, después se ejecuta el comando **rsync** y volvemos a mostrar el mismo archivo. Como podemos observar, el contenido del archivo se ha modificado y corresponde con el contenido del archivo de la primera máquina.

### 3. Acceso sin contraseña a SSH
Como hemos visto en el apartado anterior, el uso de **rsync** nos permitirá tener una copia del contenido de la primera máquina en la segunda por si esta se cae. A pesar de su utilidad aún presenta un problema, para poder mantener el contenido actualizado en todo momento necesitaremos de alguna persona que use los comandos e introduzca las contraseñas requeridas. En este aparatado solventaremos el problema de introducción de las contraseñas cada vez que queramos realizar una copia en el servidor.

Para resolver el problema haremos uso de las claves **públicas-privadas**. Usando el siguiente comando `ssh-keygen -b 4096 -t rsa` en la segunda máquina generaremos la clave pública y privada para esa máquina.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/keygen1.png"> </p>

**Nota: Para toda opción que se nos dé en la creación de las claves daremos a Intro para no escribir nada, dado que podría suponer un problema en el futuro.**

Tras generar las claves, tendremos que copiar la clave pública en el equipo remoto, en nuestro caso la primera máquina. Para realizar la copia de la clave únicamente tendremos que ejecutar el comando `ssh-copy-id [nombre_usuario]@[ip_máquina]`.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/keygen2.png"> </p>

**Nota: Puede que nos encontremos un problema al copiar la clave pública en el otro equipo. Esto puede deberse a que el fichero ~/.ssh/authorized_keys carecerá de permisos 600, por lo que usaremos el comando `chmod 600 ~/.ssh/authorized_keys` para solventarlo.**

En este punto ya deberíamos poder realizar las copias de los archivos sin necesidad de introducir ninguna contraseña.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/keygen3.png"> </p>

En la imagen anterior comprobamos como accediendo de la segunda máquina a la primera no se nos pide ninguna contraseña al ejecutar **ssh**.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/keygen4.png"> </p>

En la imagen anterior podemos comprobar cómo también podemos ejecutar órdenes en la primera máquina sin necesidad de acceder a ella directamente sin que nos pidan una contraseña.

### 4. Programar tareas con crontab
Ya hemos solucionado el acceso sin hacer uso de contraseña, pero aun así necesitaríamos que alguien use los comandos convenientes para poder realizar las copias de la primera máquina en la segunda. Para solucionarlo, vamos a editar el fichero **/etc/contab**.

En nuestro caso vamos a modificar el fichero para que realice la actualización de los archivos de **/var/www/** en la segunda máquina cada hora. Podemos observar como se realiza esto en la siguiente imagen:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/crontab2.png"> </p>

Como vemos en la siguiente demostración, el primer fichero que aparece es el que originalmente tiene la segunda máquina. Tras editar el fichero **/etc/crontab** (como en la imagen anterior) y que pase de las **:00** de la siguente hora podremos observar que si volvemos a mostrar el contenido del fichero éste se habrá modificado siendo una copia del fichero que originalmente pertenece a la primera máquina.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica2/crontab1.png"> </p>
