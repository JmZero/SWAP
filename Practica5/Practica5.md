# Práctica 5 - Replicación de bases de datos MYSQL


### 1. Crear una base de datos e insertar datos.
Vamos a llevar a cabo la creación de una BD de Mysql. Para ello primero accederemos a Mysql mediante el comando `mysql -uroot -p` e introduciremos la contraseña que le asignamos el crear la máquina.

Una vez dentro ejecutaremos los comandos que veremos en el proceso realizado en la siguiente imagen:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica5/creaBase1.png" title="creaBase1.png"> </p>

También en la imagen anterior vemos cómo introducir datos dentro de la base de datos que hemos creado.

### 2. Replicar una base de datos
Mysql ofrece una herramienta para la clonación de las bases de datos que tenemos. Esta herramienta es mysqldump.

Para tener la misma base de datos en las 2 máquinas vamos a relizarlo de forma manual para este caso. En primer lugar tendremos que evitar el acceso a esa base de datos para que no se modifique y cause fallos. Para ello usaremos el comando `FLUSH TABLES WITH READ LOCK;` dentro de Mysql.
Después tendremos que sacar la copia de la base de datos con el comando `mysqldump [nombre-BD] -u root -p > /[ruta]/[nombre-archivo].sql` y desbloquearemos las tablas que bloqueamos en el paso anterior con `UNLOCK TABLES;` dentro de Mysql.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica5/creaBase2.png" title="creaBase2.png"> </p>

Por último iremos a la segunda maquina virtual y mediante el comando  `sudo scp [nombre]@[ip-máquina-maestra]:/tmp/[archivo-bd] [ruta-de-copia]` realizaremos la copia de la base de datos. Tras esto entraremos en Mysql y crearemos la base de datos con el mismo nombre `CREATE DATABASE '[nombre-BD]';`, saldremos y ejecutaremos el siguiente comando que volcará toda la información en la base de datos creada `mysql -u root -p [nombre-BD] < /[ruta]/[nombre-archivo].sql`
En la siguente imagen se muestra el correcto funcionamiento del proceso:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica5/copiaManual1.png" title="copiaManual1.png"> </p>

### 3. Replicar la Base de Datos mediante maestro-esclavo.
El proceso anterior esta hecho a mano y lo que pretendemos es que las copias se hagan automáticamente.
En primer lugar modificaremos el archivo **/etc/mysql/mysql.conf.d/mysqld.cnf** y modificaremos los siguentes parámetros en la máquina maestro:
```
Comentaremos la linea #bind-address 127.0.0.1
Si tenemos comentada la linea log_error = /var/log/mysql/error.log la descomentaremos.
Estableceremos el identificador del servidor server-id = 1
Si tenemos comentada la linea log_bin = /var/log/mysql/bin.log la descomentaremos.
```
Tras esto reiniciaremos el servicio con el comando `/etc/init.d/mysql restart` y realizaremos el mismo proceso en la máquina esclava con la salvedad de modificar **server-id = x** siendo **x** un número distinto de 1.

Luego en la máquina maestra entraremos dentro de Mysql y ejecutaremos el proceso que se muestra a continuación:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica5/replicacion1.png" title="replicacion1.png"> </p>

Tras esto usaremos el comando `SHOW MASTER STATUS;` para ver el archivo y la posición que usaremos posteriormente en la máquina esclavo.

Despues volveremos a la máquina esclavo y ejecutaremos la siguiente línea dentro de Mysql:

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica5/replicacion2.png" title="replicacion2.png"> </p>

Tras esto iniciaremos el esclavo con el comando `START SLAVE`, volveremos a la máquina maestra y desbloquearemos las tablas con el comando `UNLOCK TABLES;` y volveremos una vez más a la máquina esclavo para ver si todo está bien configurado ejecutando el comando `SHOW SLAVE STATUS\G` donde la variable **Seconds_Behind_Master** no debe ser **NULL**.
Si todo ha ido bien podremos realizar inserciones en la tabla de maestro y podríamos visualizarlas en el esclavo como se muestra a continuación.

<p align="center"> <img src="https://github.com/JmZero/SWAP/blob/master/Practica5/replicacion3.png" title="replicacion3.png"> </p>

**Nota: durante el proceso descrito en este punto puede que sea necesario deshabilitar el firewall si tenemos ciertas reglas configuradas o podremos añadirle otras nuevas.**