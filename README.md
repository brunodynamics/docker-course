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

Ahora bien, si deseamos ver todos los contenedores que tenemos *(incluso los que están detenidos)*, utilizamos:

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
