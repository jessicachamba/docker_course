# Manual instructivo de Docker
## Tabla de contenidos

 * [Que es Docker](#que-es-docker)
 * [Instalando Docker](#instalando-docker)
 * [Como trabaja Docker](#como-trabaja-docker)
 * [Contenedores](#contenedores)
 * [Ciclo de vida de un contenedor](#ciclo-de-vida-de-un-contenedor)
 * [Exponer contenedores a la maquina local](#exponer-contenedores-a-la-maquina-local)
 * [Informacion By Mount y Volumens](#informacion-by-mount-y-volumens)
 * [Las imagenes en Docker](#las-imágenes-en-docker)
 * [Docker Network](#docker-network)
 * [Asignando variables de entorno](#asignando-variables-de-entorno)
 * [Docker Compose](#docker-compose)
 * [Docker Ignore](#docker-ignore)
 * [Anexos](#anexos)



## Que es Docker

Docker es una plataforma que nos permite resolver los 3 grandes problemas del desarrollo de software, estos problemas son:

**Construir:** Este problema se refiere a inconvenientes relacionados al entorno de desarrollo, dependencias, entornos de ejecución (IIS, XAMPP, servidores, etc.), equivalencia entre entornos DEV y PROD, problemas con servicios externos (BD, API, etc.)

**Distribuir:** Estos problemas se refieren en el sentido de la divergencia de las versiones del proyecto en los repositorios (V.1, V.2, …, V. N), divergencia en artefactos (el código se convierte en artefactos para su transporte a donde se debe ejecutar, Eje .JAR, APK, DLL, etc.) y control de versiones de código.

**Ejecutar:** Son problemas relacionados a las maquinas donde se van a ejecutar los programas y muy regularmente las maquinas PROD son diferentes a las maquinas DEV, los problemas más comunes son: compatibilidad entre ambientes (PROD, DEV), dependencias, disponibilidad de servicios, recursos de hardware.

## Instalando Docker

El proceso de instalación de Docker tanto para Mac,Linus o Windows es sumamente simple y se detalla en el siguiente link:

* (Win/Mac)[https://docs.docker.com/desktop/]
* (Cent OS)[https://docs.docker.com/engine/install/centos/]
* (Debian)[https://docs.docker.com/engine/install/debian/]
* (Fedora)[https://docs.docker.com/engine/install/fedora/]
* (Ubuntu)[https://docs.docker.com/engine/install/ubuntu/]

> **Nota:** Adicional si se esta utilizndo Windows 10 en su version Home se debe realizar el siguiente proceso:

1. Activa estas casillas de Hypervisor, Maquina Virtual y Subystem for Linux.
 
 ![image](https://user-images.githubusercontent.com/6383659/136433180-07d46873-7096-4048-b331-79c53dec8764.png)

 
2. Sigue este (tutorial}(https://docs.microsoft.com/en-us/windows/wsl/install) y utiliza wsl2 la nueva versión mejorada lanzada en 2020 WSL2.

3. [Instala Docker Desktop](https://www.docker.com/products/docker-desktop) , te dara un breve tutorial y te pedira cerrar sesión al terminar.

4. Prueba los comandos de docker desde la terminal de wsl2.


## Como trabaja Docker

Docker enfrenta los problemas del desarrollo utilizando un enfoque que originalmente fue concebido con las antiguas máquinas virtuales, pero llevando la solución a un nivel muy superior en eficiencia y optimización de recursos.

En el enfoque tradicional de la "Virtualización" como solución de los problemas de la construcción de software la idea era muy simple, utilizar maquinas virtuales que permitieran a los desarrolladores emular los ambientes productivos o de desarrollo según fuera necesario, sin embargo, este enfoque tiene múltiples problemas entre los cuales destacan:

**Peso:** Las máquinas virtuales son pesadas y lentas, ocupando varias GB de espacio, sumado a que si se desea disponer de dos ambientes que implementen el mismo SO es necesario duplicar todos los archivos del sistema origen, dando como resultado mucha información repetida y no relevante. Además, si imaginamos un contexto donde tenemos la necesidad de trabajar con múltiples sistemas operativos, nos encontraremos en una situación sumamente compleja, en la cual muchas veces no contaremos con el espacio necesario para trabajar.

**Costo de administración:** Al administrar múltiples máquinas virtuales cada una debe ser administrada por separado y en circunstancia en las cuales se trabaja con una cantidad considerable se maquinas virtuales el costo necesario de mantener el sistema operativo actualizado, gestionar usuarios, permisos, etc. es un punto muy importante a considerar.

**Diferencia entre formatos:** A lo largo de los  años las diferentes corporaciones han ideado un conjunto de formatos para trabajar con máquinas virtuales (VMDK, VDI, VHD) sin embargo la inexistencia de un estándar en la industria  puede ser un problema en especial cuando se quiere migrar el contenido de una maquina visual a otra.

Ante estos problemas la respuesta de Docker fue muy sencilla "estandarizar la conceptualización de la virtualización mediante el uso de contenedores" y aunque esto parezca una trabalenguas no es más que el utilizar contenedores como unidad fundamental de la virtualización, remplazando la necesidad de tener que virtualizar el Sistema Operativo de cada maquina virtual y brindando la posibilidad de virtualizar los componentes netamente necesarios de cada SO/programa que fuera de interés para el desarrollador.  

Las ventajas de brinda Docker al utilizar los Contenedores como medio para efectuar la virtualización son los siguientes:

**Flexible:** Al utilizar contenedores se puede virtualizar cualquier programa o sistema operativo, dando gran flexibilidad y operatividad.

**Portable:** Los contenedores pueden ser ejecutados en cualquier computador con Docker instalado y su funcionamiento será exactamente el mismo en todos los computadores.

**Liviano:** Docker funciona con una tecnología de virtualización en capas lo que permite ahorrar espacio reutilizando el Kernel del propio sistema operativo.

**Bajo acoplamiento:** Cada contenedor es autocontenido y posee todo lo q se necesita para su ejecución.

**Escalable:** Es fácil de escalar pues solo se necesita crear más instancias del mismo contenedor para incrementar los recursos.

**Seguro:** Los contenedores de Docker al permitir la virtualización de manera autocontenida limitan el acceso a partes del sistema operativo anfitrión a las cuales no se debería tener acceso.

![image](https://user-images.githubusercontent.com/6383659/136452562-281c2c21-7a9a-43f6-a88a-ff6d5cfaef1a.png)


## Componentes de Docker

Docker como plataforma se componen de 3 componente principales que gestionan su funcionamiento, dichos componentes se articulan de manera anidada y sus principales funcionalidades son:

**Docker Daemon:**  Es el encargado de gestionar los contenedores y la comunicación con el SO del computador anfitrión (normalmente se correlaciona con un servicio que se levanta para trabajar con Docker).

**Rest API:**  Es la forma en la que **Docker Daemon** permite comunicación entrante, esto lo realiza mediante API que no solo se expone a nivel de aplicación (para que sea consumido por otros programas dentro del computador) si no que también puede ser configurado para ser expuesto a nivel de red (para agentes externos, aunque esta práctica no se recomienda).

**Client Docker CLI:**  Es la terminal mediante la cual se consume el API y permite comunicación con el Docker Daemon (debido a ser un simple CLI, esta comunicación se puede realizar con cualquier otro cliente configurado correctamente).

Al mismo tiempo los componentes de Docker permiten realizar la gestión de las múltiples entidades utilizadas por Docker para la virtualización, estas entidades son:

**Conteiners:** Es el corazón de Docker en el cual se ejecutan las aplicaciones.

**Images:** Son artefactos usados para empaquetar contenedores (son como las plantillas usadas para generar contenedores).

**Data volumens:** Son la forma acceder a los archivos de la maquina anfitriona o de otros contenedores de manera segura (son completamente gestionados por Docker).

**Network:** Permite a los contenedores comunicación con el mundo exterior.


![image](https://user-images.githubusercontent.com/6383659/136436888-33488a2c-54ae-4850-867a-f4f988a1d762.png)

## Contenedores

- Son el concepto más importante de Docker
- A nivel de virtualización es el equivalente a una máquina virtual pero más liviana
- Se las puede definir como un conjunto de procesos agrupados en contenedores que ocurren en una maquina local
- El contenedor esta limitado según la decisión (configuración) usada para su creación
- La forma más sencilla de crear un contenedor es mediante el siguiente comando:
```sh
docker run {image-name}
```

## Ciclo de vida de un contenedor

Al crear un contendor se ejecuta un conjunto de procesos que dictan el flujo de vida de contenedor y que comprende desde los siguientes pasos:

1. Creación del contenedor (mediante línea de comando)
2. Ejecución del main process
3. Terminación del main process (Proceso principal)
4. Stop del contenedor (detención del contenedor y todos sus procesos internos)

> **Nota:** El main process es el proceso principal configurado que será lanzado cuando el contenedor se inicia, se destaca que cuando el main process falla o termina el contenedor termina su ejecución y se realiza un **Stop** del mismo.

## Exponer contenedores a la maquina local

El acceso de un contenedor a los componentes de una maquina local se encuentra limitado según la definición que fue usada para su creación sin embargo, se puede exponer un acceso al contenedor mediante la configuración de puertos (tal como se realizaría con un servidor) mediante el siguiente comando:

```sh
docker run --name {container-name} -p {local-port}:{container-port} {image-name}
```
**Ejemplo**
```sh
docker run --name ubuntu_container -p 8080:80 ubuntu
```

> **Nota:** Esto es sumamente útil cuando se realiza trabajo de desarrollo con servidores tipo Node, XAMPP, etc.


## Informacion By Mount y Volumens

La información de los archivos y carpetas es gestionada por Docker de dos formar distintas según las necesidades de cada caso, las opciones disponibles son:


### By Mount
- Esta opción permite compartir una carpeta local con el contenedor creado.
- Permite acceso completo tanto a los archivos desde la maquina local al contenedor y viceversa.
- Es una opción que debe ser usada con cuidado pues permite acceso total bidireccional y un contenedor con código maliciosos podría aprovecharse de esta característica.
- Permite que una persona pueda gestionar de manera natural los archivos compartidos, efectuando CRUD en cualquier archivo.
- Esta opción normalmente es utilizada en ambientes DEV y se des aconseja fuertemente su uso en PROD.
- La ejecución de un contenedor usando “By Mount” se realiza con el siguiente comando:
 
```sh
docker run --name {name-container} -v {local-route}:{container-route} {image-name}
```
**Ejemplo**
```sh
docker run --name ubuntu_container -v C/users/example:/example ubuntu
```

### Volumens

- En esta opción Docker se encarga de crear un "Volumen" completamente y únicamente gestionado por Docker.
- El volumen no tiene acceso a componentes de los archivos locales del computador anfitrión.
- Es una opción mucho más segura que By Mount, por cuanto es la opción que normalmente se usa en PROD.
- Los archivos están convertidos para ser gestionados por Docker, por cuanto una persona normalmente no los puede editar ni modificar desde fuera del contendor.
- La ejecución de un contenedor usando “Volumens” se realiza con el siguiente comando:

```sh
docker run --name {name-container} --mount src={volumn name}, dst={container-route} {image-name}
```
**Ejemplo**
```sh
docker run --name ubuntu_container --mount src=ubun_volum, dst=/example ubuntu
```

Adicional se debe considerar que los volúmenes se crean de manera automática cuando se crea un contenedor, sin embargo los volúmenes también se pueden crear mediante el siguiente comando:

```sh
docker volume {name-volume}
```

### Bonus - Copiar archivos

En el caso de utilizar volúmenes la única forma de enviar archivos desde la computadora local hasta el contenedor y viceversa es efectuando una copia explicita de los mismos, para poder efectuar esta copia se realiza con el siguiente comando:

De local a contenedor
```sh
docker cp {local file/folder} {container-name/id}:{container-route (incluye el mismo nombre del archivo copiado)}
```
**Ejemplo**
```sh
docker cp foo.txt container_id:/foo.txt
```

De contenedor a local

```sh
docker cp {container-name/id}:{container-route file/folder} {local route (incluye el mismo nombre del archivo copiado)}
```
**Ejemplo**
```sh
docker cp container_id:/foo.txt foo.txt
```

> **Nota** Se puede ejecutar el copiado, aunque el contenedor no se encuentre en ejecución

## Las imágenes en Docker

- Desde el punto de vista de la programación son las clases que permiten traer o crear contenedores
- Cada imagen se encuentra creada por un conjunto de capas que contiene cierta información (SO, programa, etc.)
- El objetivo de cada capa es agregar información a la imagen
- Las capas son inmutables por cuanto si se cambia la estructura de una imagen es necesario volver a compilar para que el cambio tenga efecto 
- Lo cambios al momento de ejecutar la compilación de una imagen se agregan de manera secuencial una tras otra
- Docker posee un sistema de comparación de capas para eficiencia de compilación que permite que en compilación de capas posteriores si un componente ya fue ejecutado este no se modifique y se compile solo lo que ha cambiado
- Los contenedores tienen acceso a todas las capas de la imagen
- Docker siempre agrega una última capa mutable para permitir cambios por parte del contenedor durante su ejecución
- El archivo utilizado para la creación de imágenes es el "Dockefile" donde se especifica todas las capas usadas dentro de la imagen

La estructura clásica de un Dockerfile es la siguiente:

![image](https://user-images.githubusercontent.com/6383659/136439064-cc6c069b-496d-42dd-9ad3-3fdc650c7670.png)

Comandos útiles para gestión de imágenes

**Buid de imágenes**
la bandera -t indica que la imagen será de DEV y el '.' indica el contexto (directorio) usado para la creación.
```sh
docker build -t {image-name} .
```

> **Nota:** Para poder aprovechar el sistema de capas y el cacheado de Docker utilizado durante la compilación de imágenes se debe colocar las capas de la imagen de tal forma que se vaya de lo más genérico a lo más específico, y en las capas inferiores se realicen los cambios más específicos según sea necesario.

### Bonus

Adicional se debe mencionar que al crear una imagen esta será tagueada asignado una versión de forma automática, esta versión por defecto siempre será "latest".

Para cambiar el nombre de un tag se puede usar el siguiente comando:

```sh
docker tag {old-name} {new-name}
```

Adicional se debe mencionar que las imágenes generadas de forma local pueden ser almacenadas en Docker Hub para que puedan ser usadas por el público en general. Para efectuar este proceso es necesario tener una cuenta creada dentro de Docker Hub y efectuar el push correspondiente de la imagen tal como se realiza con la herramienta de control de versiones Git, los pasos para ejecutar el push de imagen son los siguientes:


Loguin en docker
```sh
docker login
```

Push a repositorio público (se debe seguir el estándar username/program_name:name_image)
```sh
docker push {image}
```
**Ejemplo**

```sh
docker push victor0402/ubuntu:ejemplo
```

Para revisar el historial de versiones de una imagen se puede utilizar el siguiente comando 

```sh
docker history {name}
```

## Docker Network

Uno de los puntos más importantes al utilizar Docker es poder tener la capacidad de que múltiples contendores se comuniquen entre sí, sin embargo, como ya se mencionó cada contenedor es autocontenido y autosuficiente, por cuanto para lograr una comunicación entre ellos se utiliza una nueva herramienta proporcionada por Docker llamada "Docker Network".

Tal como el nombre lo sugiere Docker Network es una red de contendores que intenta simular una red real mediante la cual se cominearían los ordenadores en ambientes PROD.

Para crear una red de Docker se realiza con el siguiente comando:

```sh
docker network create --attachable {network-name}
```

Para conectar un contendor a la red se utiliza:

```sh
docker network connect {network-name} {container-name}
```

Además si se desea revisar en que red se encuentra un contenedor se puede realizar mediante:

```sh
docker inspect {container-name/id}
```

## Asignando variables de entorno

Un punto importante de Docker es la posibilidad de definir variables de entorno que pueden ser utilizadas por los contenedores tal como sucedería con un servidor en ambiente de PROD o DEV, la definición de varíales de entorno se efectúa al momento de ejecución del contenedor mediante la bandera --env, un ejemplo de esta configuración se muestra a continuación:

```sh
docker run -d -name {container_name} --env {varible_name}={variable_value} {image_name}
```
Ejemplo de implementación con Mongo DB (la sección **db** del valor configurado en la variable se corresponde con el nombre del contenedor en la red)
```sh
docker run -d -name mongo_db --env  MONGO_URL=mongodb://db:27017/test mongo
```

## Docker Compose

Es la forma de hacer todas las configuraciones previamente mencionadas de manera fácil y eficaz. las características de este elemento son:

- Se utiliza un archivo tipo yml el cual debe tener el siguiente nombre docker-compose.yml 
- El archivo debe estar en el directorio raíz en el cual se levanta el proyecto
- Dentro de este archivo se configuran servicios dentro de las cuales se especifican los contendores he imágenes usados
- Un servicio puede tener uno o más contendedores configurados

Los siguientes comandos se utilizan para gestionar el Docker compose

Se encarga de crear los elementos mencionados en el archivo yml y permite levantar el entorno especificado en el archivo 
```sh
docker-compose up
```

Permite revisar los logs de los servicios levantados, la parte -f {name} es opcional y permite especificar el nombre del servicio
```sh
docker-compose logs -f {name}
```

Ejecutar comandos en los contenedores de los servicios
```sh
docker-compose exec {name} {comand}
```

Revisar componentes levantados
```sh
docker-compose ps
```

Detener y eliminar componentes
```sh
docker-compose down
```

Ejecutar build dentro de compose
```sh
docker-compose build
```

### Bonus 

En el uso de control de versiones para evitar cambios indeseados en el trabajo colaborativo se utiliza el compose-overide el cual permite sobre escribir para poder tener distinción entre PROD y DEV.

Este archivo "docker_compose.override.yml" tiene precedencia sobre el original al momento de ejecutar la herramienta, además solo se deben usar los ports de la maquina local en uno de los 2 archivos.

## Docker Ignore

Es un archivo utilizado para evitar montar archivos no deseados dentro de la imagen utilizada para la creación del contendor



### Bonus

Al momento de ejecutar instrucciones en la imagen se recomienda utilizar el formato Exec form, puesto que de esta forma se permite enviar señales al contenedor de forma directa (en cambio al usar la forma **shell form** el main process se ejecuta como un shell hijo por cuanto no pasan señales al sistem operativo). 

Ejemplo de formas de ejecución:

**Exec form**            
cmd ["/loop.sh"]          

**Shell form**
cmd "/loop.sh"
## Anexos

### Comandos útiles


Ejecutar y crear contenedores especificando el nombre de imagen
```sh
docker run {image-name}
```   
---
Ejecutar, crear y nombre a un contenedor
```sh
docker run --name {container-name} {image-name}
```
---
Ejecutar un contenedor en modo iterativo de terminal
```sh
docker run -it {image-name}
```
---
Ejecutar, crear y nombrar un contenedor en modo background (-d) sin que su ejecución termine (por conclusión del main proces), para eso se envía a ejecutar el siguiente comando (tail -f dev/null)
```sh
docker run --name {container-name} -d {image-name} {comand}
```
---
Ejecutar, crear, nombrar un contenedor y exponer los puertos de un contenedor (la bandera -d se puede usar para no enchufar la terminal)
```sh
docker run --name {container-name} -p {local-port}:{container-port} {image-name}
```
---
Ejecutar, crear, nombrar un contenedor y montar una carpeta mediante "By Mount" (se usa la bandera -v) junto con modalidad background de terminal (-d)
```sh
docker run --name {container-name} -d -v {local-route}:{image-route} {image-name}
```
---
Ejecutar, crear, nombrar un contenedor y montar un volumen mediante "Volumen" (se usa la bandera --mount)
```sh
docker run --name {container-name} --mount src={volumen-name},dst={route-image} {image-name}
```
---
Ejecutar, crear, nombrar un contenedor limitando la memoria de contendor (tener cuidado porque si una app no tiene la memoria suficiente puede fallar)
```sh
docker run -d --name {container-name} --memory {memory-space} {image-name}
```
---
Asignar variables de entorno
```sh
docker run -d -name {container_name} --env {varible_name}={variable_value} {image_name}
```
---
Ejecutar, crear, nombrar un contenedor y eliminar el contenedor al terminar el main proces
```sh
docker run --rm {image-name}
```
---
Ejecutar un comando en un contenedor que se encuentra en ejecución
```sh
docker exce -it {container-name} {comand}
```
---
Crear un volumen de docker
```sh
docker volume {volume-name}
```
---
Copiar archivos desde ruta local hasta contenedor (el formato de la ruta del contenedor es **name:ruta**)
```sh
docker cp {local file} {container route}
```
---
Copiar archivos desde contenedor hasta ruta local (el formato de la ruta del contenedor es **name:ruta**)
```sh
docker cp {container route} {Re-name local file}
```
---
Crear imagen, la bandera -t indica el nombre o tag usado en la imagen (el formato usado en el nombre del tag es username/program), el contexto por lo regular se usa "." para la carpeta en la que uno se encuentra
```sh
docker build -t {image-name} {context route}
```
---
Cambiar de Tag a una imagen
```sh
docker tag {old tag name} {new tag name}
```
---
Login docker en docker hub
```sh
docker login
```
---
Efectuar push a docker hub
```sh
docker push {image}
```
---
Crear red de docker
```sh
docker network create --attachable {network-name}
```
---
Conectar un contenedor a una red
```sh
docker network connet {network-name} {container name}
```
---
Revisar historial de la imagen (el nombre de la imagen es opcional para efectuar la búsqueda)
```sh
docker history {image-name}
```
---
Revisar logs de los contenedores
```sh
docker logs {container-name}
```
---
Renombrar nombre del contenedor
```sh
docker rename {old-name} {new-name}
```
---
Mostrar los contenedores activos (usando la bandera **-a** se muestran todos los contendores incluso los detenidos) 
```sh
docker ps
```
---
Eliminar un contendor
```sh
docker rm {container-name/id}
```
---
Eliminar todos los contenedores que estén detenidos
```sh
docker container prune
```
---
Inspeccionar los contenedores (sobre todo útil para revisar errores) 
```sh
docker inspect {container-name/id}
```
---
Inspeccionar los contenedores utilizando filtro (se destaca que la búsqueda se realiza en un formato JSON)
```sh
docker inspect --format '{{.Key.Key}}' {container-name/id}
```
---
Revisar los recursos que consumen los contenedores
```sh
docker stats
```
---
Detener contenedores (Enviando Sig Term)
```sh
docker stop {container-name}
```
---
Detener contenedores (Enviando Sig Kill)
```sh
docker kill {container-name}
```
---
Eliminar contenedores no usados (la bandera -q permite mostrar solo los ids de los contenedores)
```sh
docker rm -f $(docker ps -aq)
```
---
Eliminar elementos ml tageados o tags asignados con "none"
```sh
docker system prune
```
---
Se encarga de crear los elementos mencionados en el archivo yml y permite levantar el entorno especificado en el archivo 
```sh
docker-compose up
```
---
Permite revisar los logs de los servicios levantados, la parte -f {name} es opcional y permite especificar el nombre del servicio
```sh
docker-compose logs -f {name}
```
---
Ejecutar comandos en los contenedores de los servicios
```sh
docker-compose exec {name} {comand}
```
---
Revisar componentes levantados
```sh
docker-compose ps
```
---
Detener y eliminar componentes
```sh
docker-compose down
```
---
Ejecutar build dentro de compose
```sh
docker-compose build
```
