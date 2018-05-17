# Práctica 3 - Balanceo de carga
Para esta práctica vamos a llevar a acbo la configuracion de la seguridad de la granja web.
Pare ello tendremos que instalar un certificado **SSL** para configurar el acceso por **HTTPS** a los servidores y cinfigurar reglas en el cortafuegos de la granja web.

### 1. Instalar certificado SSL para acceder via HTTPS
En primer lugar tendremos que activar el modulo **SSL** de Apache mediante el comando `a2enmod ssl` y posterior mente lo reiniciaremos `service apache2 restart`. 
Tras esto crearemos un fichero con la ruta `mkdir /etc/apache2/ssl` donde introduciremos nuestros certificados posteriormente. Una vez creado el fichero generaremos nuestros certificados haciendo uso del siguiente comando `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout
/etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt` y lo rellenaremo como pone en la imagen:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/SSL1.png" title="SSL1.png"> </p>

Una vez obtenidos los certificados editaremos el fichero `/etc/apache2/sites-available/default-ssl.conf` de la siguiente manera:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/SSL2.png" title="SSL2.png"> </p>

Tras esto activaremos el sitio **default-ssl** `a2ensite default-ssl` y reiniciaremos apache `service apache2 reload`. Ahora comprobaremos que todo esta correcto y funciona perfectamente, tando desde el navegador de nuestra máquina anfitriona como desde una maquina cliente haciendo uso de **curl**.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/SSL3.png" title="SSL3.png"> </p>
<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/SSL4.png" title="SSL4.png"> </p>

Tras esto copiaremos el certificados dentro de la otra máquina que hace de servidor y nuestro balanceador haciendo uso de comando `scp [nombre_usuario]@[ip_máquina]:/etc/apache2/ssl/* [/etc/apache2/ssl/ o /etc/nginx/ssl/]`. 

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/SSL5.png" title="SSL5.png"> </p>

Como extra para el balanceadr tendremos que modificar un fichero `/etc/nginx/conf.d/default.conf` para poder activar **HTTPS** de la siguiente forma: 

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/SSL6.png" title="SSL6.png"> </p>

Tras esto vamos a comprobar que todo funciona correctamente:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/SSL7.png" title="SSL7.png"> </p>

Vemos como todo el proceso funciona a la perfección.


### 2. Configuracion de as reglas del cortafuegos
En esta segunda parte vamos vamos a configurar el cortafuego para permitir solo el acceso de cierto protocolos a traves de ciertos puertos. Para ello crearemos dentro del directorio `/etc/init.d` un archivo que tendra el siguiente contenido:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/IpTables.png" title="IpTables.png"> </p>

Le daremos permisos de ejecución a ese archivo y lo ejecutaremos de tal manera que esta sera nuestra configuracion: 

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica4/IpTables2.png" title="IpTables2.png"> </p>

Como algo adicional vamos a modificar el archivo para poder ejecutarse automaticamente cada vez que iniciemos la configuracion al arrancar nuestra máquina. Para ello tendremos que poder al inicio del fichero anterior la siguiente configuracion:
```
### BEGIN INIT INFO
# Privides: Configuracion
# Required-Start: $syslog
# Required-Stop: $syslog
# Default-Status: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: blabla
# Description: 

### END INIT INFO
```

Una vez modificador haremos uso del comando `sudo update-rc.d [nombre_archivo].sh defaults` y ya podremos borrar la configuracion y apagar la máquina. Al volver a encenderla esta nos mostrara la configuracion que va ligada a dicho archivo.
