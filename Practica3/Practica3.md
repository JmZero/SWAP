# Práctica 3 - Balanceo de carga
Para esta práctica vamos a configurar la red de manera que tengamos un balancedor que dividira la carga entre los dos servidores que hemos creado con anterioridad.
Con este fin solucionaremos la sobrecarga en los servidores, en nuestro caso para los servidores HTTP.
Tendremos diferente maneras de hacer un balanceador, en esta prácicac vamos a utilizar **nginx** y **haproxy**.

### 1. Crear una nueva máquina que hara la labor de balanceador
En primer lugar tendremos que crear una nueva máquina virtual de la misma manera que lo hicimos en la Práctica 1, pero esta vez no seleccionaremos la opcion **LAMP** dado que como vamos a utilizar el puerto 80 no queremos que Apache lo ocupe, por lo que en este caso no lo instaremos.

En este caso editaremos el fichero `/etc/network/interfaces` de la siguiente manera una vez que la instalacion se haya completado:
```
auto enp0s8
iface enp0s8 inet static
address 192.168.56.130
netmask 255.255.255.0
```
### 2. Balanceo de carga usando nginx
Como hemos dicho antes, no hemos instalado apache debido a que **nginx** sera el que use el puerto **80** en este caso.
Para instalar **nginx** tendremos que utilizar la siguiente lista de comandos:
`sudo apt-get update && sudo apt-get dist-upgrade && sudo apt-get autoremove`
`sudo apt-get install nginx`

Una vez instalado procederemos a comprobar su funcionamiento. Para ello iniciaremos el servicio haciendo uso del comando `sudo systemctl start nginx` y comprobaremos que se encuentra activo con el comando `sudo systemctl status nginx`.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/nginx1.png" title="nginx1.png"> </p>

Como podemos ver en la imagen, el servicio se ha instalado con exito y funciona correctamente. Como muestra de su funionamiento tenemos las siguiente imagen:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/nginx2.png" title="nginx2.png"> </p>

Esta imagen es una captura realizada desde el navegador de la máquina anfitriona introduciendo en el navegador la IP del balanceador de carga `192.168.56.130`.

Ahora procederemos a configurar **nginx**. Para ello tendremos que editar el fichero `/etc/nginx/conf.d/default.conf` o en su defecto (si este no esta creado) lo crearemos. Editaremos el fichero de la siguiente manera:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/nginx3.png" title="nginx3.png"> </p>

- **upstream**: esta sección es donde se indican las IP de todos los servidores finales de nuestra granja web.
- **server**: esta sección se colocara postreriormente de la anterior y nos indicara como se distribuira el tráfico.

En este caso hemo utilizado balanceamiento de carga aplicando el algoritmo **Round-Robin**, aunque no es la única manera de hacerlo.
Antes de reiniciar el servicio tentremos que modificar el archivo `/etc/nginx/nginx.conf` comentando una línea de la forma en la que se muestra a continuación:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/nginx4.png" title="nginx4.png"> </p>

Despues de esto procederemos a reiniciciar **nginx** con el comando `sudo systemctl restart nginx` y pocederemos a comprobar su funcionamiento.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/nginx5.png" title="nginx5.png"> </p>

***NOTA: como en la práctica se nos requiere que el primer servidor tenga el doble de capacidad que la segunda la imagen muestra que 2 de cada 3 peticiones seran atendidas por el primer servidor***
***NOTA 2: para que lo anterior suceda hemos editado el fichero `/etc/nginx/nginx.conf` cambiando el upstream por el siguiente:***
```
upstream apaches {
	server 172.16.168.130 weight=1;
	server 172.16.168.131 weight=2;
}
```

### 3. Balanceo de carga con haproxy
En este punto vamos a instalar a nuestra máquina otro balanceador de carga, **haproxy**.
Su instalación es muy sencilla, unicamente debemos usar el comando `sudo apt-get install haproxy` y tendremos que modificar el archivo `/etc/haproxy/haproxy.cfg` añadiendo en el las seis últimas líneas que se muestran en la imagen:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/haproxy1.png" title="haproxy1.png"> </p>

Una vez editado el fichero iniciaremos el servicio usando el comando `sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg`, si no sale ningun error o aviso al ejecutarlo es que todo ha ido bien.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/haproxy2.png" title="haproxy2.png"> </p>

Volveremos a comprobar su funcionamiento haciendo uso de **curl**

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/haproxy3.png" title="haproxy3.png"> </p>

### 4. Someter a una alta carga el servidor balanceado
Vamos a someter a nuestro balanceador a una carga para comprobar su funionamiento, para ello instalaremos en nuestra máquina cliente **Apache Benchmark**. Por defecto debería de esta instalado con el servidor Apache, por si acoso es necesario se debera instalar con el comando `sudo apt-get install apache2-utils`.
Ahora proceferemos a ejecutar **Apache Benchmark** tanto para **nginx** como para **haproxy** haciendo uso del comando `ab -n 1000 -c 10 http://[IP del balanceador]/index.html`.

**Usando nginx:**

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/benchmark2.png" title="benchmark2.png"> </p>

**Usando haproxy:**

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica3/benchmark1.png" title="benchmark1.png"> </p>

Como podremos comprobar, la prueba en **nginx** se ejecutará mas rápido y atenderá un mayor numero de peticiones por segundo, por lo que podremos concluir que la configuración que poseemos de **nginx** como balanceador sera mejor que al usar **haproxy**.