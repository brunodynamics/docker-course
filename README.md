# Docker

## ¿Qué es un contenedor?

Un contenedor es un conjunto de procesos que corren en la máquina que los aloja y están aislados de los demás procesos.
A diferencia de las VM, un contenedor es únicamente una agrupación lógica y no incluye un Sistema Operativo, ni virtualizaciones de hardware.
Los contenedores no tienen alcance más allá de sí mismos, a menos que nosotros como desarrolladores se lo permitamos.

## ¿Cómo correr un contenedor?

Para correr un contenedor se utiliza el comando:

> docker run \<container_name\>

Como primer paso, luego de la instalación podríamos correr el contenedor "hello-world" y así validar que esta se hizo correctamente.

> docker run hello-world

Al correr un contenedor, Docker le asignará un nombre por defecto. Si deseamos asignarle nosotros mismos ese nombre, debemos usar lo siguiente:

> docker run --name \<container_own_name\> \<container_name\>

## Renombrar un contenedor

Docker no te permite tener dos contenedores con un mismo nombre.
Si deseáramos renombrar uno de nuestros contenedores, es tan sencillo como colocar:

> docker rename \<container_old_name\> \<container_new_name\>

## Mostrar contenedores activos

Para mostrar los contenedores que tenemos corriendo en Docker, se lo debemos preguntar al Docker Daemon. Esto se hace utilizando el comando:

> docker ps

Ahora bien, si deseamos ver todos los contenedores que tenemos _(incluso los que están detenidos)_, utilizamos:

> docker ps -a

## Ver detalles de un contenedor

Para ver los detalles específicos de un contenedor podemos usar el comando:

> docker inspect \<container_ID\>

o

> docker inspect \<container_name\>

## Eliminar contenedores

Para eliminar contenedores que no usaremos más o ya no nos sirvan utilizamos lo siguiente:

> docker rm \<container_ID\>

o

> docker rm \<container_name\>

Si tenemos muchos contenedores que esten parados, ya no usemos y los queremos borrar, Docker nos da una instrucción para eliminarlos.

> docker container prune

Si deseo parar y borrar un contenedor a la vez se puede utilizar lo siguiente:

> docker rm -f \<container_name\>

## Correr Ubuntu

Con Docker se puede llamar la repositorio público y correr un contenedor que tenga Ubuntu con un solo comando:

> docker run ubuntu

Esto traerá el contenedor y lo detendrá.

El hecho de que hayamos instalado el contenedor y que no esté corriendo no nos sirve, por lo cual, si queremos correrlo y mantenerlo ejecutándose debemos utilizar:

> docker run -it ubuntu

Esto nos ejecutará el contenedor y nos posicionará en el bash de Ubuntu.

Si deseamos salir basta con colocar _exit_ en la terminal y darle _ENTER_.

## Ciclo de vida de un contenedor

Debemos tener claro que cada vez que se ejecuta un contenedor, este se encarga únicamente de ejecutar un proceso principal. Si este proceso se detiene, el contenedor también se detendrá.
En pocas palabras, un contenedor corre siempre y cuando su proceso principal esté corriendo.

Supongamos que estamos en el caso del contenedor de Ubuntu, queremos ejecutarlo sin que se cierre automáticamente. Para ello ejecutamos el siguiente comando:

> docker run --name \<container_own_name\> -d ubuntu tail -f /dev/null

<small>Nota: El flag <i>-d</i> hace que Docker corra el contenedor en segundo plano, sin comprometer a nuestros stdinput o stdoutput</small>

Con esto, le damos un nombre a nuestro contenedor y cambiamos su proceso principal a _tail -f /dev/null_.

Este contenedor de Ubuntu ya está corriendo y podría servirnos para conectarnos a él. Si deseáramos ejecutar el bash de este contenedor, lo podríamos hacer de la siguiente manera:

> docker exec -it \<container_name\> bash

Nos daremos cuenta de que si ahora ejecutamos el comando _exit_, se cierra el bash, pero el contenedor sigue corriendo.

Si deseáramos detener el contenedor, debemos encontrar el ID de su proceso. Para ello podemos usar una variante del comando _inspect_

> docker inspect --format '{{.State.Pid}}' \<container_name\>

Con el Pid obtenido como respuesta ejecutamos lo siguiente:

> kill -9 \<pid\>

<small>Psdt: Este comando solo funciona si estás trabajando en Linux.</small>

En caso estés usando otro sistema operativo, deberás utilizar lo siguiente:

> docker stop \<container_name\>

o

> docker stop \<container_ID\>

## Exponer un contenedor

Para exponer un contenedor correremos un contenedor de _NGINX_ de la siguiente manera:

> docker run -d --name proxy -p \<pc_port\>:\<container_port\> nginx

<small>Nota: Se agrega el flag -p (publish) y se colocan el puerto de la PC anfitriona que se desea utilizar y el puerto del contenedor que se desea exponer.</small>

Si deseáramos ver los logs que generó el contenedor podemos utilizar el comando siguiente:

> docker logs \<container_name\>

Y si los quisieramos ver a medida que se van generando:

> docker logs -f \<container_name\>

Ahora bien, a medida que tenemos un contenedor corriendo mucho tiempo, la lista de logs se puede hacer muy extensa. Por ello, si quisiéramos limitar el número de logs mostrados en pantalla podemos ejecutar lo siguiente:

> docker logs --tail \<logs_limit\> -f \<container_name\>

## Bind mounts

Supongamos que tenemos un contenedor con una base de datos. Trabajamos en ella, la poblamos de información, pero un dia la decidimos borrar. Como se puede suponer, cuando se borre el contenedor, también se borrará toda la data que estaba en él, incluídos los datos de nuestra base.

Para evitar esto existen los _Bind Mounts_.
Estos te permiten guardar una réplica de algún directorio del contenedor en nuestra PC. Para ello usamos el siguiente comando:

> docker run -d --name \<container_own_name\> -v \<pc_dir_path\>:\<container_dir_path\> \<container_name\>

Con ello, cuando eliminemos el contenedor, podemos volver a crear otro con el comando líneas arriba, referenciando a la carpeta que creamos y así tendrá la data de nuestro antiguo contenedor.

## Volúmenes

Los volúmenes son una forma más segura de tener data persistente en nuestros contenedores.

Para listar los volúmenes de Docker podemos colocar el comando;

> docker volume ls

Para crear uno:

> docker volume create \<volume_name\>

Con el volumen creado ya podemos correr un contenedor contenedor y asociarlo a este.

> docker run -d --name \<container_own_name\> --mount src=\<volume_name\>,dst=\<container_dir\> \<container_name\>

Finalmente, si deseamos eliminar un contenedor podemos utilizar lo siguiente:

> docker volume rm \<volume_name\>

## Insertar y extraer archivos de un contenedor

Para insertar un archivo desde nuestra PC al contenedor de Docker podemos utilizar el siguiente comando:

> docker cp \<file_path\> \<container_name\>:\<dest_path\>

Para hacer la operación inversa:

> docker cp \<container_name\>:\<dest_path\> \<file_path\>

<small>Psdt: No hace falta que el contenedor esté corriendo para realizar estas operaciones.</small>

## Imágenes

Una imagen es un compilado de todo lo que un contenedor necesita para funcionar correctamente.
Son plantillas las cuales se utilizarán para crear nuevos contenedores.

Para listar las imágenes que tenemos en local utilizamos el siguiente comando:

> docker image ls

Para instalar una imagen sin correr un contenedor podemos usar el comando:

> docker pull \<image_name\>

<small>Nota: El repositorio de todas las imágenes de Docker se encuentra en <a href="https://hub.docker.com">Docker Hub</a></small>

Para eliminar una imagen de Docker utilizamos lo siguiente:

> docker rmi \<image_ID\>

<small>En caso Docker nos indique que no se puede eliminar la imagen porque hay repositorios que dependen de ella, podemos agregar el flag <i>-f</i> al comando</small>

## Construir una imagen propia

Lo primero que debemos hacer para esto es crear un archivo llamado _Dockerfile_. Este contendrá una serie de comandos que le indicarán a Docker como proceder para crear la imagen requerida.

Para correr este _Dockerfile_ usamos el siguiente comando:

> docker build -t \<iamge_name\>:\<tag\> \<Dockerfile path\>

Con esto nuestra imagen ya fue creada y guardada en local. Ahora podríamos crear contenedores que tengan esta imagen como base.

Ahora, si quisiéramos publicar esta imagen en Docker Hub es tan sencillo como hacer lo siguiente:

Primero nos autenticamos en Docker:

> docker login

Luego debemos asegurarnos de cambiar el tag de la imagen, pues esta muy probablemente esté basada en una imagen privada de alguna organización.

> docker tag \<image_name\>:\<tag\> \<dockerhub_username\>/\<image_name\>:\<tag\>

Finalmente, ejecutamos el comando _push_ de Docker para enviarlo al repositorio remoto.

> docker push \<dockerhub_username\>/\<image_name\>:\<tag\>

## Sistema de capas

Las imágenes de Docker son conjuntos de capas. Cada capa muestra una operación realizada para que la imagen pueda tener la forma y funcionalidad requerida.

En [Docker Hub](https://hub.docker.com) podremos encontrar los _Dockerfile_ de cada imagen alojada, esto puede ser consultado por nosotros para entender las buenas prácticas al crear estos archivos y entender un poco de las dependencias y acciones de cada imagen.

En caso no tengamos como ver ese Dockerfile, también podemos ver la historia de la imagen con un simple comando:

> docker history \<image_name\>:\<tag\>
