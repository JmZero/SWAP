# Práctica 1 - Preparación de las herramientas

En esta primera práctica vamos a realizar las preparaciones previas para llevar a cabo las prácticas posteriores.

### 1. Instalación
En primer lugar tendremos que crear nuestras máquinas virtulaes, bien una por una o configurando completamente una de ellas y posteriormente clonándola. Al clonar nuestra máquina evitaremos que las MAC sean las mismas.
Se ha instalado Ubuntu Server 16.04 en una máquina virtual con la configuración por defecto que nos ofrece VirtualBox (1GB de memoria RAM y 10 GB de almacenamiento. Para configurar la máquina se ha usado la configuración por defecto y se ha usado la herramienta para instalar LAMP y SSH (como se explica en el guión).

### 2. Configuración de la red Host-Only
Para poder hacer uso de los servicios de SSH tendremos que configurar una nueva tarjeta de red, en mi caso he usado la tarjeta host-only que viene por defecto y consta de los siguiente parámetros:

- **Dirección IPv4 del servidor**: 182.168.56.1
- **Dirección del servidor**: 192.168.56.100
- **Máscara del servidor**: 255.255.255.0
- **Límite inferior de direcciones**: 192.168.101
- **Límite superior de direcciones**: 192.168.254

Para usarla en las diferentes máquinas virtuales la he colocado en el _Adaptador2_. En ese momento iniciaremos nuestras máquinas y ejecutaremos el comando `ifconfig` de tal manera que se nos muestren las diferentes interfaces de red. En este caso veremos que no disponemos de la interfaz de red Host-Only que hemos añadido. Para poder verla tendremos que modificar el archivo `/etc/network/interfaces` de la siguiente manera: 
```
auto enp0s8
iface enp0s8 inet static
address 192.168.56.110 / 192.168.56.120 (máquina 1 / máquina 2)
netmask 255.255.255.0
```
Posteriormente ejecutaremos el comando `/etc/init.d/networking restart` para reiniciar los servicios de red y comprobaremos que ahora aparece la nueva interfaz al usar el comando `ifconfig`.

### 3. Acceso por SSH
Como ya hemos instalado ssh y hemos configurado la interfaz de red vamos a comprobar su funcionamiento conectándonos desde la primera máquina a la segunda y viceversa.
A continuación se muestran las capturas del procedimiento en las dos máquinas haciendo uso del comando `ssh [nombre_usuario]@[ip_máquina]`.

![SSH.png](https://github.com/JmZero/SWAP/blob/master/Practica1/SSH.png)

### 4. Acceso con Curl y comprobar Apache
Comprobaremos que ya tenemos el comando curl instalado, cuya función es transferir archivos con sintaxis URL. Para comprobar que Apache funciona correctamente vamos a crear un archivo `hola.html` en el directorio `/var/www/html`.
A continuación se muestran las capturas del procedimiento en las 2 máquinas haciendo uso del comando `curl http://direccionIPdelservidor/hola.html`.

![Curl.png](https://github.com/JmZero/SWAP/blob/master/Practica1/Curl.png)
