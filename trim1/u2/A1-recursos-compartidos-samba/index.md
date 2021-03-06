#1. Introducción

Samba son un conjunto de aplicaciones para Linux, que implementan el protocolo de comunicación SMB utilizado por los sistemas operativos Microsoft Windows para compartir carpetas e impresoras. En esta práctica trabajaremos con este programa.

Vamos a necesitar las siguientes 3 MVs:

Un servidor GNU/Linux con IP estática (172.18.8.53).
 
Un cliente GNU/Linux con IP estática (172.18.8.54). 

Un cliente Windows con IP estática (172.18.8.13). 
 
#2. Servidor Samba

##2.1. Preparativos

Capturar salida de los comandos siguientes en el servidor:

![imagen](images/1.png)

![imagen](images/2.png)

![imagen](images/3.png)

##2.2. Usuarios locales

Vamos a GNU/Linux, y creamos los siguientes grupos y usuarios. Podemos usar comandos o entorno gráfico Yast:

* Grupo 'jedis' con 'jedi1', 'jedi2' y 'supersamba'.
* Grupo 'siths' con 'sith1', 'sith2' y 'supersamba'.
* Crear el usuario smbguest. Para asegurarnos que nadie puede usar smbguest para entrar en nuestra máquina mediante login, vamos a modificar en el fichero /etc/passwd de la siguiente manera: "smbguest: x :1001:1001:,,,:/home/smbguest:/bin/false".

![imagen](images/5.png)

* Crear el grupo 'starwars', y dentro de este poner a todos los 'siths', 'jedis', 'supersamba' y a 'smbguest'.

![imagen](images/4.png)

##2.3. Instalar Samba

##2.4. Carpetas para los recursos compartidos

Creamos las carpetas:

![imagen](images/7.png)

Asignamos permisos:

![imagen](images/8.png)

Este es el resultado final:

![imagen](images/11.png)

##2.5. Configurar Samba

<<<<<<< HEAD
Copiamos el fichero de configuración:

![imagen](images/12.png)

=======
Primero copiaremos el fichero de configuración para tener una copia de seguridad:

![imagen](images/12.png)

Debemos configurar el servidor Samba con una serie de opciones, usando el Yast o directamente modificando el fichero. Debe quedar de la siguiente manera, estos son los ficheros que compartiremos:

![imagen](images/13.png)

![imagen](images/14.png)

>>>>>>> 3d797dd77100c7e9558e4ab247a8b851898ad4ff
##2.6. Usuarios Samba

Despues de crear los usuarios en el sistema, debemos añadirlos a Samba, usaremos el comando 'smbpasswd -a nombredeusuario'. Comprobamos con el comando siguiente:

![imagen](images/15.png)

##2.7. Reiniciar

Después de reiniciar el servidor con el comando 'sustemctl stop smb' y 'systemctl start smb', comprobamos:

![imagen](images/16.png)

![imagen](images/17.png)

Vemos que el servicio SMB está a la escucha:

![imagen](images/19.png)

#3. Windows

Desde un cliente Windows trataremos de acceder a los recursos compartidos del servidor Samba. Comprobaremos accesos de diferentes formas y con diferentes usuarios, al finalizar comprobaremos los resultados con ciertos comandos.

Para consultar todas las conexiones hacemos 'net use'. En caso de que hubiera alguna conexión abierta ejecutamos 'net use * /d/y' para cerrarla.

![imagen](images/20.png)

Una vez hayamos accedido con un usuario y contraseña se creará la conexión:

![imagen](images/22.png)

Con el siguiente comando se elimina dicha conexión:

![imagen](images/23.png)

Comprobamos las conexiones realizadas desde el servidor:

![imagen](images/25.png)

![imagen](images/26.png)

Ejecutamos 'net use /?' como consulta.

![imagen](images/27.png)

#4. Cliente Linux

##4.1. GUI

Desde el entorno gráfico, comprobamos el acceso a los recursos compartidos:

Probamos a crear archivos dentro de 'corusant' con usuarios con permisos:

![imagen](images/28.png)

Dentro de 'tatooine' igual:

![imagen](images/29.png)

Sim embargo, intentamos crear algo dentro de public con cualquier usuario y no nos deja:

![imagen](images/30.png)

Comprobamos con los comandos usados anteriormente:

![imagen](images/31.png)

![imagen](images/32.png)

##4.2. Comandos

Accedemos a un equipo linux que será nuestro cliente Samba, mostraremos los recursos SMB de un equipo concreto, en este caso del servidor:

![imagen](images/33.png)

Crearemos en el local la carpeta '/mnt/samba-remoto/corusant' y la montamos como si fuera una carpeta más de nuestro sistema:

![imagen](images/34.png)

Comprobamos ejecutando el comando 'df -hT':

![imagen](images/35.png)

Comprobamos nuevamente con los comandos el resultado en el servidor:

![imagen](images/36.png)

![imagen](images/37.png)

#5. Montaje automático

Si queremos volver a acceder a los recursos remotos debemos repetir el proceso de montaje manual, a no ser que hagamos una configuración de montaje permanente o automática.
Para configurar acciones de montaje automáticos cada vez que se inicie el equipo, debemos configurar el fichero '/etc/fstab', añadiendo las siguientes líneas:

![imagen](images/38.png)

Comprobamos al reiniciar:

![imagen](images/39.png)

#6. Preguntas para resolver

* ¿Las claves de los usuarios en GNU/Linux deben ser las mismas que las que usa Samba?

No, la constraseña que asignamos cuando añadimos los usuarios a Samba son las que autenticarán al usuario cuando se conecte desde un cliente remoto utilizando el protocolo SMB.

* ¿Puedo definir un usuario en Samba llamado sith3, y que no exista como usuario del sistema?

No, antes debemos haber creado el usuario en Unix.

* ¿Cómo podemos hacer que los usuarios sith1 y sith2 no puedan acceder al sistema pero sí al samba? (Consultar /etc/passwd)

El contenido del fichero '/etc/passwd' decide quien puede acceder al sistema y que se puede hacer una vez dentro, por lo tanto deduzco que si no queremos que los usuarios accedan al sistema solo tendrémos que eliminar la línea de sith1 y de sith2.

* Añadir el recurso 'homes' al fichero 'smb.conf' según los apuntes. ¿Qué efecto tiene?

El recurso 'homes' configura los parámetros para compartir la carpeta home de cada usuario, es opcional ponerlo en 'smb.conf', si no existe no se compartirán las carpetas home de cada usuario. De lo contrario, si lo añadiéramos crearíamos un perfil móvil de esta manera, cuando se identifique el usuario en cualquiera de los PCs de la red, su perfil se creará de forma automática.
