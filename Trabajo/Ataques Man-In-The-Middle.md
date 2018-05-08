#Ataques Man In The Middle

1. Definición.
2. Como prevenir estos ataques.
3. Ejemplos de ataques.
4. Simulaciones, robo de contraseñas y redirrección a otras páginas.

### 1. Definición.
En criptografía, un ataque man in the middle es un tipo de ataque informático en el que el atacante tiene conexiones independientes con las víctimas y trasmite mensajes entre ellos, haciéndoles creer que están hablando directamente entre sí a través de una conexión privada, cuando en realidad toda la conversación es controlada por el atacante. El atacante debe ser capaz de interceptar todos los mensajes que van entre las dos víctimas e inyectar nuevos, lo cual es sencillo en muchas circunstancias.

Un ataque man-in-the-middle puede tener éxito solo cuando el atacante puede hacerse pasar por cada punto final a satisfacción de la otra (esto es un ataque de autenticación mutua). La mayoría de los protocolos criptográficos incluyen alguna forma de autenticación de extremos específicamente para prevenir los ataques MITM. Por ejemplo: SSL autentifica al servidor utilizando una autoridad de certificación de confianza mutua.

###2. Como prevenir estos ataques
Existen varios tipos de defensa contra estos ataques, estas defensas emplean técnicas de autenticación basadas en:
- Infraestructura de claves públicas.
- Autenticación mutua fuerte tales como:
 - Claves secretas (entropía alta = más segura)
 - Contraseñas (passwords) (entropía baja = menos segura)
- El examen de latencia, que medirá el tiempo que tarda la información en llegar a cada parte. Si este proceso tarda normalmente 20 segundos y se calculan 60 segundos para llegar a cada parte, esto puede indicar la existencia de un tercero en la comunicación.
- Un segundo canal de verificación (seguro).
- Pads(almohadillas) de una sola vez son inmunes a los ataques MITM, en el supuesto caso de la seguridad y la confianza de la plataforma de una sola vez.
- Verificación hacia adelante.

La integridad de las claves públicas en general se deben asegurar de alguna manera, pero éstas no tienen que ser secretas. Las contraseñas (passwords) y claves secretas compartidas tienen el requerimiento de secreto adicional. Las claves públicas pueden ser verificadas por una autoridad de certificación (CA), cuya clave pública se distribuye a través de un canal seguro (por ejemplo: con un navegador web o instalación en el sistema operativo). Las claves públicas también pueden ser verificadas por una web de confianza que distribuye las claves públicas a través de un canal seguro (por ejemplo: reuniones cara a cara).

###3. Ejemplos de ataques
- #####Detenida en Pamplona una joven de 19 años por estafar 6.500 euros a una empresa
https://navarra.elespanol.com/articulo/sucesos/detenida-pamplona-joven-19-anos-estafar-6-500-euros-empresa/20160513113625041253.html

- #####Una vulnerabilidad en las principales aplicaciones bancarias permite realizar ataques Man-In-The-Middle
http://noticiasseguridad.com/vulnerabilidades/una-vulnerabilidad-en-las-principales-aplicaciones-bancarias-permite-realizar-ataques-man-middle/

- #####Descubren que se pueden realizar ataques Man-In-The-Middle contra carteras LEDGER
http://noticiasseguridad.com/hacking-incidentes/descubren-que-se-pueden-realizar-ataques-man-middle-contra-carteras-ledger/

###4. Simulaciones, robo de contraseñas y redirrección a otras páginas.
Para realizar una simulación de un ataque Man-In-The-Middle vamos a utilizar una herramienta llamada *Cain*. Para poder usarla lo primero que debemos hacer es desavilitar el firewall. Despues de esto accederemos a la herramienta y rpocederemos a configurarla: **Configure** --> **Seleccionamos el adaptador cuya IP corresponde a nuestra máquina anfitriona**. 
Una vez configurada accederemos a la pestana **Sniffer** y le daremos al boton `+` para añadir todos los host que se encuentrar en la red (deberemos tener activada la opción de sniffer). Postariormente comprobaremos la IP que corresponde a la máquina virtual que vamos a atacar.

imagen

Una vez obteniada accederemos a la apestana **ARP** dentro de **Sniffer** y le daremos a la opcion `+` y seleccionaremos en primer lugar la máquina víctima y posteriormente la máquina atacante (nuestro PC). 

Imagen

Despues activaremos la opcion Star/Stop ARP y ya procederemos al robo de la contraseña. Para ello vamos a usar una pagina que usa **HTTP** a la que accederemos en la máquina virtual. Una vez escrita la contraseña iremos a la pestaña **Passwords** a la opción **HTTP** y alli aparecera la contraseña que hemos utilizado:

Imagen

Ahora vamos a hacer otro ataque redireccionando una página a otra. Para ello accederemos dentro de la pestaña **ARP** a la opción **ARP-DNS** y volveremos a añadir una configuración con el boton `+`. En primer lugar escribiremos la direccion de la página a la que se accedera y en segundo lugar la página a la que se redirecciona.

imagen
