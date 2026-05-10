page_title: Mejores prácticas para escribir Dockerfiles
page_description: Sugerencias, consejos y directrices para escribir Dockerfiles limpios y fiables
page_keywords: Ejemplos, Uso, imagen base, docker, documentación, dockerfile, mejores prácticas, hub, repositorio oficial

# Mejores prácticas para escribir Dockerfiles

## Introducción

Docker puede construir imágenes automáticamente leyendo las instrucciones de un
`Dockerfile`, un archivo de texto que contiene todos los comandos, en orden, necesarios para
construir una imagen determinada. Los `Dockerfile`s siguen un formato específico y utilizan
un conjunto específico de instrucciones. Puedes aprender los conceptos básicos en la página
[Referencia de Dockerfile](https://docs.docker.com/reference/builder/). Si
eres nuevo escribiendo `Dockerfile`s, deberías empezar por ahí.

Este documento cubre las mejores prácticas y métodos recomendados por Docker,
Inc. y la comunidad Docker para crear `Dockerfile`s efectivos y fáciles de usar.
Recomendamos encarecidamente que sigas estas recomendaciones (de hecho,
si estás creando una Imagen Oficial, *debes* seguir estas prácticas).

Puedes ver muchas de estas prácticas y recomendaciones en acción en el [`Dockerfile` de buildpack-deps](https://github.com/docker-library/buildpack-deps/blob/master/jessie/Dockerfile).

> Nota: para obtener explicaciones más detalladas de cualquiera de los comandos de Dockerfile
>mencionados aquí, visita la página [Referencia de Dockerfile](https://docs.docker.com/reference/builder/).

## Directrices y recomendaciones generales

### Los contenedores deben ser efímeros

El contenedor producido por la imagen que define tu `Dockerfile` debe ser tan
efímero como sea posible. Con "efímero" queremos decir que puede detenerse y
destruirse, y que se puede construir uno nuevo y ponerlo en su lugar con un mínimo
absoluto de configuración y ajustes.

### Usa [un archivo .dockerignore](https://docs.docker.com/reference/builder/#the-dockerignore-file)

Para acelerar la subida y mejorar la eficiencia durante `docker build`, deberías usar
un archivo `.dockerignore` para excluir archivos o directorios del contexto de
construcción y de la imagen final. Por ejemplo, a menos que `.git` sea necesario para tu proceso
de construcción o scripts, deberías añadirlo a `.dockerignore`, lo que puede ahorrar muchos
megabytes de tiempo de subida.

### Evita instalar paquetes innecesarios

Para reducir la complejidad, las dependencias, el tamaño de los archivos y los tiempos de construcción,
deberías evitar instalar paquetes extra o innecesarios solo porque podrían ser
"útiles tener". Por ejemplo, no necesitas incluir un editor de texto
en una imagen de base de datos.

### Ejecuta solo un proceso por contenedor

En casi todos los casos, solo deberías ejecutar un único proceso en un único
contenedor. Desacoplar las aplicaciones en múltiples contenedores hace que sea mucho
más fácil escalar horizontalmente y reutilizar contenedores. Si ese servicio depende de
otro servicio, haz uso del [enlace de contenedores](https://docs.docker.com/userguide/dockerlinks/).

### Minimiza el número de capas

Necesitas encontrar el equilibrio entre la legibilidad (y por tanto el mantenimiento a largo plazo)
del `Dockerfile` y minimizar el número de capas que utiliza. Sé estratégico y
prudente con el número de capas que usas.

### Ordena los argumentos de múltiples líneas

Siempre que sea posible, facilita los cambios posteriores ordenando los argumentos de múltiples líneas
alfanuméricamente. Esto te ayudará a evitar la duplicación de paquetes y hará que la
lista sea mucho más fácil de actualizar. Esto también hace que los PRs sean mucho más fáciles de leer y
revisar. Añadir un espacio antes de una barra invertida (`\`) también ayuda.

Aquí hay un ejemplo de la [imagen `buildpack-deps`](https://github.com/docker-library/buildpack-deps):

    RUN apt-get update && apt-get install -y \
      bzr \
      cvs \
      git \
      mercurial \
      subversion

### Caché de construcción

Durante el proceso de construcción de una imagen, Docker recorrerá las
instrucciones de tu `Dockerfile` ejecutando cada una en el orden especificado.
A medida que se examina cada instrucción, Docker buscará una imagen existente en su
caché que pueda reutilizar, en lugar de crear una nueva imagen (duplicada).
Si no quieres usar la caché en absoluto, puedes usar la opción `--no-cache=true`
en el comando `docker build`.

Sin embargo, si permites que Docker use su caché, es muy importante
entender cuándo encontrará, y cuándo no, una imagen coincidente. Las reglas básicas
que Docker seguirá se describen a continuación:

* Comenzando con una imagen base que ya está en la caché, la siguiente
instrucción se compara con todas las imágenes secundarias derivadas de esa imagen base
para ver si alguna de ellas fue construida usando exactamente la misma instrucción. Si
no, la caché se invalida.

* En la mayoría de los casos, simplemente comparar la instrucción en el `Dockerfile` con una
de las imágenes secundarias es suficiente. Sin embargo, ciertas instrucciones requieren
un poco más de examen y explicación.

* En el caso de las instrucciones `ADD` y `COPY`, se examina el contenido del archivo o archivos
que se van a incluir en la imagen. Concretamente, se realiza una suma de verificación
del archivo o archivos y luego esa suma de verificación se usa durante la búsqueda en la caché.
Si algo ha cambiado en el archivo o archivos, incluidos sus metadatos,
entonces la caché se invalida.

* Aparte de los comandos `ADD` y `COPY`, la verificación de caché no mirará los
archivos en el contenedor para determinar una coincidencia de caché. Por ejemplo, al procesar
un comando `RUN apt-get -y update`, los archivos actualizados en el contenedor
no se examinarán para determinar si existe un acierto de caché. En ese caso, solo
se usará la cadena del comando en sí para encontrar una coincidencia.

Una vez que la caché se invalida, todos los comandos `Dockerfile` posteriores
generarán nuevas imágenes y la caché no se utilizará.

## Las instrucciones del Dockerfile

A continuación encontrarás recomendaciones sobre la mejor manera de escribir las
distintas instrucciones disponibles para su uso en un `Dockerfile`.

### [`FROM`](https://docs.docker.com/reference/builder/#from)

Siempre que sea posible, usa los Repositorios Oficiales actuales como base para tu
imagen. Recomendamos la [imagen Debian](https://registry.hub.docker.com/_/debian/)
ya que está muy controlada y se mantiene extremadamente minimal (actualmente por debajo de
100 mb), siendo al mismo tiempo una distribución completa.

### [`RUN`](https://docs.docker.com/reference/builder/#run)

Como siempre, para que tu `Dockerfile` sea más legible, comprensible y
mantenible, pon las instrucciones `RUN` largas o complejas en múltiples líneas separadas
con barras invertidas.

Probablemente el caso de uso más común para `RUN` es una aplicación de `apt-get`.
Al usar `apt-get`, hay algunas cosas a tener en cuenta:

* No hagas `RUN apt-get update` en una sola línea. Esto causará
problemas de caché si el archivo referenciado se actualiza, lo que hará que tu
`apt-get install` posterior falle sin comentarios.

* Evita `RUN apt-get upgrade` o `dist-upgrade`, ya que muchos de los paquetes
"esenciales" de las imágenes base no podrán actualizarse dentro de un
contenedor sin privilegios. Si un paquete base está desactualizado, deberías contactar a sus
mantenedores. Si sabes que hay un paquete en particular, `foo`, que necesita ser
actualizado, usa `apt-get install -y foo` y se actualizará automáticamente.

* Escribe instrucciones como:

    RUN apt-get update && apt-get install -y package-bar package-foo package-baz

Escribir la instrucción de esta manera no solo la hace más fácil de leer
y mantener, sino que también, al incluir `apt-get update`, garantiza que la caché
se invalidará de forma natural y que se instalarán las últimas versiones sin
más código ni intervención manual.

* Una mayor invalidación natural de la caché se puede lograr fijando versiones de paquetes
(p. ej., `package-foo=1.3.*`). Esto forzará la recuperación de esa versión
independientemente de lo que haya en la caché.
Escribir tu código `apt-get` de esta manera facilitará enormemente el mantenimiento y reducirá
los fallos debidos a cambios imprevistos en los paquetes requeridos.

#### Ejemplo

A continuación se muestra una instrucción `RUN` bien formada que demuestra las
recomendaciones anteriores. Nótese que el último paquete, `s3cmd`, especifica una versión
`1.1.0*`. Si la imagen usaba anteriormente una versión más antigua, especificar la nueva
causará una invalidación de caché de `apt-get update` y garantizará la instalación de
la nueva versión (que en este caso tenía una característica nueva y necesaria).

    RUN apt-get update && apt-get install -y \
        aufs-tools \
        automake \
        btrfs-tools \
        build-essential \
        curl \
        dpkg-sig \
        git \
        iptables \
        libapparmor-dev \
        libcap-dev \
        libsqlite3-dev \
        lxc=1.0* \
        mercurial \
        parallel \
        reprepro \
        ruby1.9.1 \
        ruby1.9.1-dev \
        s3cmd=1.1.0*

Escribir la instrucción de esta manera también te ayuda a evitar la posible duplicación de
un paquete determinado, ya que es mucho más fácil de leer que una instrucción como:

    RUN apt-get install -y package-foo && apt-get install -y package-bar
    
### [`CMD`](https://docs.docker.com/reference/builder/#cmd)

La instrucción `CMD` debe usarse para ejecutar el software contenido en tu
imagen, junto con cualquier argumento. `CMD` casi siempre debe usarse en
la forma `CMD ["ejecutable", "param1", "param2"…]`. Por tanto, si la imagen es para un
servicio (Apache, Rails, etc.), ejecutarías algo como
`CMD ["apache2","-DFOREGROUND"]`. De hecho, esta forma de la instrucción es
recomendada para cualquier imagen basada en servicios.

En la mayoría de los otros casos, `CMD` debe recibir un shell interactivo (bash, python,
perl, etc), por ejemplo, `CMD ["perl", "-de0"]`, `CMD ["python"]`, o
`CMD ["php", "-a"]`. Usar esta forma significa que cuando ejecutas algo como
`docker run -it python`, accederás directamente a un shell utilizable, listo para usar.
`CMD` rara vez debe usarse en la forma `CMD ["param", "param"]` en
combinación con [`ENTRYPOINT`](https://docs.docker.com/reference/builder/#entrypoint), a menos que
tú y tus usuarios esperados ya estéis bastante familiarizados con cómo funciona `ENTRYPOINT`.

### [`EXPOSE`](https://docs.docker.com/reference/builder/#expose)

La instrucción `EXPOSE` indica los puertos en los que un contenedor escuchará
conexiones. En consecuencia, deberías usar el puerto común y tradicional para
tu aplicación. Por ejemplo, una imagen que contiene el servidor web Apache usaría
`EXPOSE 80`, mientras que una imagen que contiene MongoDB usaría `EXPOSE 27017` y
así sucesivamente.

Para el acceso externo, los usuarios pueden ejecutar `docker run` con un indicador que especifica
cómo mapear el puerto especificado al puerto de su elección.
Para el enlace de contenedores, Docker proporciona variables de entorno para la ruta desde
el contenedor receptor de vuelta al origen (es decir, `MYSQL_PORT_3306_TCP`).

### [`ENV`](https://docs.docker.com/reference/builder/#env)

Para facilitar la ejecución de nuevos programas, puedes usar `ENV` para actualizar la
variable de entorno `PATH` para el software que instala tu contenedor. Por
ejemplo, `ENV PATH /usr/local/nginx/bin:$PATH` garantizará que `CMD ["nginx"]`
funcione correctamente.

La instrucción `ENV` también es útil para proporcionar las variables de entorno requeridas
específicas de los servicios que deseas contenerizar, como `PGDATA` de Postgres.

Por último, `ENV` también puede usarse para establecer números de versión de uso común para que
las actualizaciones de versión sean más fáciles de mantener, como se ve en el siguiente ejemplo:

    ENV PG_MAJOR 9.3
    ENV PG_VERSION 9.3.4
    RUN curl -SL http://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /usr/src/postgress && …
    ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH

De manera similar a tener variables constantes en un programa (en contraposición a valores
codificados directamente), este enfoque te permite cambiar una única instrucción `ENV` para
actualizar automáticamente la versión del software en tu contenedor.

### [`ADD`](https://docs.docker.com/reference/builder/#add) o [`COPY`](https://docs.docker.com/reference/builder/#copy)

Aunque `ADD` y `COPY` son funcionalmente similares, en términos generales, `COPY`
es preferido. Esto se debe a que es más transparente que `ADD`. `COPY` solo
admite la copia básica de archivos locales al contenedor, mientras que `ADD` tiene
algunas características (como la extracción automática de tar local y soporte de URL remota) que no son
inmediatamente obvias. En consecuencia, el mejor uso de `ADD` es la extracción automática
de archivos tar locales en la imagen, como en `ADD rootfs.tar.xz /`.

Si tienes múltiples pasos en tu `Dockerfile` que usan diferentes archivos de tu
contexto, cópialos con `COPY` individualmente, en lugar de todos a la vez. Esto garantizará que
la caché de construcción de cada paso solo se invalide (forzando que el paso se vuelva a ejecutar) si los
archivos específicamente requeridos cambian.

Por ejemplo:

    COPY requirements.txt /tmp/
    RUN pip install /tmp/requirements.txt
    COPY . /tmp/

Resulta en menos invalidaciones de caché para el paso `RUN` que si pusieras el
`COPY . /tmp/` antes.

Dado que el tamaño de la imagen importa, se desaconseja encarecidamente usar `ADD` para obtener paquetes
de URLs remotas; en su lugar deberías usar `curl` o `wget`. De esta manera puedes
eliminar los archivos que ya no necesitas después de extraerlos y no tendrás
que añadir otra capa a tu imagen. Por ejemplo, deberías evitar hacer
cosas como:

    ADD http://example.com/big.tar.xz /usr/src/things/
    RUN tar -xJf /usr/src/things/big.tar.xz -C /usr/src/things
    RUN make -C /usr/src/things all

Y en su lugar, hacer algo como:

    RUN mkdir -p /usr/src/things \
        && curl -SL http://example.com/big.tar.gz \
        | tar -xJC /usr/src/things \
        && make -C /usr/src/things all

Para otros elementos (archivos, directorios) que no requieren la capacidad de
extracción automática de tar de `ADD`, siempre deberías usar `COPY`.

### [`ENTRYPOINT`](https://docs.docker.com/reference/builder/#entrypoint)

El mejor uso de `ENTRYPOINT` es establecer el comando principal de la imagen, permitiendo que
la imagen se ejecute como si fuera ese comando (y luego usar `CMD` como
las opciones predeterminadas).

Empecemos con un ejemplo de una imagen para la herramienta de línea de comandos `s3cmd`:

    ENTRYPOINT ["s3cmd"]
    CMD ["--help"]

Ahora la imagen puede ejecutarse así para mostrar la ayuda del comando:

    $ docker run s3cmd

O usando los parámetros correctos para ejecutar un comando:

    $ docker run s3cmd ls s3://mybucket

Esto es útil porque el nombre de la imagen puede funcionar como referencia al binario
como se muestra en el comando anterior.

La instrucción `ENTRYPOINT` también puede usarse en combinación con un script auxiliar,
permitiéndole funcionar de manera similar al comando anterior, incluso
cuando iniciar la herramienta puede requerir más de un paso.

Por ejemplo, la [Imagen Oficial de Postgres](https://registry.hub.docker.com/_/postgres/)
usa el siguiente script como su `ENTRYPOINT`:

```bash
#!/bin/bash
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"
```

> **Nota**:
> Este script usa [el comando `exec` de Bash](http://wiki.bash-hackers.org/commands/builtin/exec)
> para que la aplicación que se ejecuta finalmente se convierta en el PID 1 del contenedor. Esto permite
> que la aplicación reciba cualquier señal Unix enviada al contenedor.
> Consulta la ayuda de [`ENTRYPOINT`](https://docs.docker.com/reference/builder/#ENTRYPOINT)
> para más detalles.


El script auxiliar se copia en el contenedor y se ejecuta a través de `ENTRYPOINT` al
iniciar el contenedor:

    COPY ./docker-entrypoint.sh /
    ENTRYPOINT ["/docker-entrypoint.sh"]

Este script permite al usuario interactuar con Postgres de varias maneras.

Puede simplemente iniciar Postgres:

    $ docker run postgres

O puede usarse para ejecutar Postgres y pasar parámetros al servidor:

    $ docker run postgres postgres --help

Por último, también podría usarse para iniciar una herramienta completamente diferente, como Bash:

    $ docker run --rm -it postgres bash

### [`VOLUME`](https://docs.docker.com/reference/builder/#volume)

La instrucción `VOLUME` debe usarse para exponer cualquier área de almacenamiento de base de datos,
almacenamiento de configuración, o archivos/carpetas creados por tu contenedor docker. Se te
recomienda encarecidamente usar `VOLUME` para cualquier parte mutable y/o gestionable por el usuario
de tu imagen.

### [`USER`](https://docs.docker.com/reference/builder/#user)

Si un servicio puede ejecutarse sin privilegios, usa `USER` para cambiar a un usuario
que no sea root. Comienza creando el usuario y el grupo en el `Dockerfile` con algo
como `RUN groupadd -r postgres && useradd -r -g postgres postgres`.

> **Nota:** Los usuarios y grupos en una imagen obtienen un
> UID/GID no determinista en el sentido de que el "siguiente" UID/GID se asigna independientemente de las reconstrucciones de la imagen. Por tanto, si es crítico, deberías asignar un UID/GID explícito.

Deberías evitar instalar o usar `sudo` ya que tiene un comportamiento impredecible de TTY y
reenvío de señales que puede causar más problemas de los que resuelve. Si
absolutamente necesitas una funcionalidad similar a `sudo` (p. ej., inicializar el
daemon como root pero ejecutarlo como no-root), puedes usar
["gosu"](https://github.com/tianon/gosu).

Por último, para reducir capas y complejidad, evita cambiar `USER`
de un lado a otro con frecuencia.

### [`WORKDIR`](https://docs.docker.com/reference/builder/#workdir)

Para mayor claridad y fiabilidad, siempre deberías usar rutas absolutas para tu
`WORKDIR`. Además, deberías usar `WORKDIR` en lugar de proliferar
instrucciones como `RUN cd … && hacer-algo`, que son difíciles de leer,
depurar y mantener.

### [`ONBUILD`](https://docs.docker.com/reference/builder/#onbuild)

`ONBUILD` solo es útil para imágenes que van a construirse `FROM` una imagen
determinada. Por ejemplo, usarías `ONBUILD` para una imagen de stack de lenguaje que
construye software de usuario arbitrario escrito en ese lenguaje dentro del
`Dockerfile`, como puedes ver en las [variantes `ONBUILD` de Ruby](https://github.com/docker-library/ruby/blob/master/2.1/onbuild/Dockerfile).

Las imágenes construidas con `ONBUILD` deberían obtener una etiqueta separada, por ejemplo:
`ruby:1.9-onbuild` o `ruby:2.0-onbuild`.

Ten cuidado al poner `ADD` o `COPY` en `ONBUILD`. La imagen "onbuild" fallará
catastróficamente si el contexto de la nueva construcción no tiene el recurso que se está
añadiendo. Añadir una etiqueta separada, como se recomienda arriba, ayudará a mitigar esto
permitiendo al autor del `Dockerfile` tomar una decisión.

## Ejemplos de Repositorios Oficiales

Estos Repositorios Oficiales tienen `Dockerfile`s ejemplares:

* [Go](https://registry.hub.docker.com/_/golang/)
* [Perl](https://registry.hub.docker.com/_/perl/)
* [Hy](https://registry.hub.docker.com/_/hylang/)
* [Rails](https://registry.hub.docker.com/_/rails)

## Recursos adicionales:

* [Referencia de Dockerfile](https://docs.docker.com/reference/builder/#onbuild)
* [Más sobre imágenes base](https://docs.docker.com/articles/baseimages/)
* [Más sobre construcciones automatizadas](https://docs.docker.com/docker-hub/builds/)
* [Directrices para crear Repositorios
Oficiales](https://docs.docker.com/docker-hub/official_repos/)
