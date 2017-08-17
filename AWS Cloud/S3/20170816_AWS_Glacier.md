Amazon Glacier
===

*Fuentes:*
- [Documentación oficial](https://aws.amazon.com/es/documentation/s3/)
- [Página de AWS S3](https://aws.amazon.com/es/s3/)
- [Precios de AWS S3](http://aws.amazon.com/s3/pricing/)
- [AWS S3 Master Class](https://youtu.be/VC0k-noNwOU)
- [AWS re:Invent 2016: Deep Dive on Amazon S3 (STG303)](https://youtu.be/bMhWWkhydFQ)


## Indice.
---
- [Introducción](#introduccion)
- [Conceptos Básicos](#conceptos-básicos)
- [Primeros Pasos](#primeros-pasos)
- [Linea de Comandos de Amazon S3](#línea-de-comandos-de-amazon-s3)
- [Folders](#folders)

---
## Introducción ##
---
¿Qué es Amazon S3?      
---
Amazon S3 es un **almacenamiento de objetos** creado para almacenar y recuperar cualquier cantidad de datos desde cualquier ubicación: sitios web y aplicaciones móviles, aplicaciones corporativas y datos de sensores o dispositivos IoT.

Permite recopilar, almacenar y analizar datos de forma cómoda y sencilla, independientemente de su formato y a escala masiva. Es durable, seguro, y altamente escalable. Puede ser accedido desde la interface web, desde la línea de comando (Amazon CLI) y/o desde APIs. Puede utilizarse en forma aislada como un repositorio de datos, o en forma integrada con otros servicios de AWS.

### Características
* Fácil de usar
* Bajo costo
* Disponible (cuatro 9s)
* Durable (once 9s)
* Seguro
* Escalable
* Integrado con otros servicios AWS

### Casos de uso
* Backup & Archive
* Almacenar y distribuir contenido (fotos, videos, etc.)
* Static Website Hosting
* Big Data & Analytics
* Almacenamiento de nube híbrida
* Datos de aplicaciones Cloud-native
* Distaster recovery

Para soportar estos tipos de uso, Amazon S3 ofrece diferentes tipos de storages (*Storage Classes*), designados para diferentes modalidades de uso: *General purpose*, *Infrequent access*, y *Archive*.

Para ayudar a gestionar los datos, cuenta con un gestor de políticas (*Lifecycle Policies*) que permite mover los datos en forma automática entre las diferentes clases de storage.

También provee seguridad, control de acceso, y encriptación.

### Object Storage vs Traditional Storage

Existen varias diferencias entre las soluciones de almacenamiento tradicional (Block Storage, File Sotage) y las soluciones de almacenamiento de objetos (Object Storage).

En el siguiente artículo se puede encontrar información adicional:

   * [Introduction To Object Storage](https://blog.rackspace.com/introduction-to-object-storage)

---
## Conceptos Básicos ##
---

### Buckets
Son los depósitos donde se almacenan los objetos en S3. Representan el nivel mas alto de jerarquía dentro del almacenamiento. Cada objeto encuentra dentro de un *bucket*.
Se pueden crear y utilizar hasta 100 *buckets* por cada cuenta por defecto, y cada *bucket* puede contener miles de objetos.

El nombre del *bucket* debe ser único dentro de todos los existentes en Amazon S3 (no solo dentro de mi cuenta). Debe cumplir con una serie de reglas, debe tener entre 3 y 63 caracteres, no puede tener mayúsculas, ni espacios, ni caracteres especiales salvo guiones y puntos, entre otros.  

El nombre del *bucket* será visible en la URL que remite a los objetos almacenados en él. Una vez creado, el nombre no puede ser modificado.

Referencias:
    [Working with Amazon S3 Buckets](http://docs.aws.amazon.com/es_es/AmazonS3/latest/dev/UsingBucket.html)
    [Restricciones y limitaciones en los Buckets](http://docs.aws.amazon.com/es_es/AmazonS3/latest/dev/BucketRestrictions.html)


### Objects
Son los objetos (archivos) almacenados en Amazon S3.
Un objeto puede contener cualquier tipo de datos en cualquier formato.
El tamaño máximo para un objeto es de 5TB, y un *bucket* puede contener una cantidad ilimitada de objetos.

Cada objeto consiste de *datos* (el archivo propiamente dicho) y *metadatos* (una serie de información acerca del archivo). La porción de *datos* es opaca a S3, es decir, es tratada como un simple conjunto de bytes sin importar su contenido. Los *metadatos* son pares de valores nombrados, que describen el objeto.

### Keys
Cada objeto almacenado dentro de *bucket* es identificado en forma única por un clave (*Key*). Se podría pensar en la *key* como si fuera el *filename* del objeto.
La *key* puede contener hasta 1024 caracteres, incluyendo barra (/), retrobarra (\\), punto, y guión.

La clave debe ser única dentro de un *bucket*, pero diferentes *buckets* pueden contener objetos con la misma clave.
La combinación de *bucket* + *key* + *version ID* (opcional) identifica en forma única a un objeto almacenados en S3.

Ejemplo de clave: */datos/informes/2017/01/reporte-de-horas.doc*

### URL del objeto
Cada uno de los objetos almacenados en S3 puede ser accedido mediante una URL única, la cual se conforma del *Amazon web services endpoint*, el nombre del *bucket*, y la *key* del objeto.

La URL puede tener estos dos formatos:
http(s)://*\<bucket-name\>*.s3.amazonaws.com/*\<object-key\>*
http(s)://s3.amazonaws.com\/*\<bucket-name\>*/*\<object-key\>*

Esto puede cambiar sensiblemente, dado que el dominio de aws generalmente incluye también la region (ej. s3-us-west-2.amazonaws.com) y la *bucket-key* puede incluir una serie de carpetas dentro (*folders*).

Por ej.:
https://s3-us-west-2.amazonaws.com/my-bucket/document.doc
https://bucket-auditoria.s3-us-west-2.amazonaws.com/datos/informes/2017/01/reporte-de-horas.doc


### Regiones
Es la región geográfica donde Amazon S3 almacenara el *bucket* que se está creando.
Elegir una región permite minimizar los costos, optimizar la latencia, o cumplir con requisitos legales o regulatorios. Amazon S3 permite replicar objetos entre regiones, lo veremos más adelante.

---
## Primeros pasos ##
Eso es todo lo que debemos saber (por ahora) para comenzar a utilizar las funciones básicas de S3.

Amazon S3 se accede desde la Consola de Administración de Amazon Web Services.
Una vez que se ingresa a la consola, en la barra de búsqueda escribir "S3" y seleccionar la consola de AWS S3.

![alt text](./images/S3_Console.png)

### Crear un *bucket*
* En el panel de S3, haga click en *Create Bucket*

![alt text](./images/S3_bucket_01.png)

* Introduzca el nombre del *bucket* y seleccione la región.
* Con esta información ya puede crear el *bucket* clickeando *Create*.
* O puede clickear *Next* para configurar Propiedades adicionales (control de versiones, etiquetas, logging) y/o Permisos. Dejemos todas esas opciones por defecto por ahora y complete la creación del *bucket*.
![alt text](./images/S3_bucket_02.png)


* Listo, ya puede ver la lista de sus *buckets*

![alt text](./images/S3_bucket_03.png)


### Subir objetos
* Seleccionar el *bucket* donde se quiere subir el objeto
![alt text](./images/S3_upload_01.png)

* Click en *Upload*
![alt text](./images/S3_upload_02.png)

* Seleccionar los archivos a subir (browse / drag&drop)
![alt text](./images/S3_upload_03.png)

* Clickear *Upload*.
* La barra de estado en la parte baja de la pantalla muestra el progreso. Una vez terminado, el objeto queda almacenado en el *bucket*.
![alt text](./images/S3_upload_04.png)

En forma opcional, al momento de realizar el upload se pueden configurar otras opciones sobre el objeto tales como:
* Permisos
* Permitir el acceso público al objeto
* Especificar la clase de storage donde se almacenará el objeto
* Opciones de cifrado
* Metadatos

Veremos estas opciones mas adelante, por lo cual por ahora las dejaremos por defecto.


### Descargar objetos
* Seleccionar el objeto que se encuentra dentro del *bucket* (con el check-box a la izquierda del objeto).
* Se abre sobre la derecha el panel de propiedades.
* Click en *Download*
![alt text](./images/S3_download_01.png)

## Acceder a un objeto (acceso público)
Podemos darle permisos a nuestros objetos para que los mismos puedan accederlos en forma pública, por ej. desde un navegador web. Esto puede resultar útil a la hora de compartir información con otras personas que no tengan cuentas es AWS.
Para esto debemos habilitar los permisos necesarios, que por defecto están deshabilitados.

Como vimos anteriormente, todo objeto que tenemos en un *bucket* es accesible mediante una *key*.
* Seleccionar el objeto dentro del *bucket* (con el chek-box).
* En el panel de propiedades copiar el *Link* y abrirlo en un browser.
![alt text](./images/S3_public_01.png)

* El navegador nos da error y no podemos acceder al objeto. Esto es porque el objeto por defecto no tiene el acceso público habilitado.
![alt text](./images/S3_public_02.png)

* Si queremos dar acceso público a este objeto, podemos hacerlo de varias formas. Una es seleccionando el objeto para ver sus propiedades (ahora dando click en el nombre, no en el check-box), y luego seleccionar *Make public*
![alt text](./images/S3_public_03.png)

Listo! ahora podemos volver a ingresar al link que habíamos copiado antes en el navegador y el objeto podrá ser accedido.
![alt text](./images/S3_public_04.png)

Es normal en la consola de AWS poder hacer lo mismo de varias formas, por ejemplo en este caso:
![alt text](./images/S3_public_05.png)

El acceso público también se puede dar al momento de subir el objeto al *bucket*.

---
## Línea de Comandos de Amazon S3 ##

Ahora realizaremos operaciones básicas desde la línea de comando de Amazon S3 (CLI).

Requisito: se debe contar con un usuario creado en el AWS IAM, para poder contar con las credenciales necesarias para acceder a S3 desde línea de comando (*Access Key ID* y *Secret Access Key*)

### Descargar e instalar la línea de comandos
Es necesario descargar la línea de comandos desde la página de Amazon AWS (disponible para Windows, Linux y Mac).

Link: [Interfaz de línea de comando de AWS](https://aws.amazon.com/es/cli/)

Tanto desde Linux como Windows, si ya se tiene Python instalado, se puede instalar la AWS CLI mediante el comando pip:
```bash
$ python --version
Python 3.6.1

$ pip install awscli
Collecting awscli
  Using cached awscli-1.11.130-py2.py3-none-any.whl
(...)
Installing collected packages: awscli
Successfully installed awscli-1.11.130

$ aws --version
aws-cli/1.11.130 Python/3.6.1 Windows/7 botocore/1.5.93
```


### Configuración inicial
Abra una consola (terminal en Linux o cmd en Windows), y luego:

```bash
$ aws configure
AWS Access Key ID [None]: AKIAWOINCOKAO3UZB4TN
AWS Secret Access Key [None]: 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
Default region name [None]: us-east-1
Default output format [None]: json
```

Donde:
- *AWS Access Key ID [None]:* clave de acceso de su usuario (generada por IAM)
- *AWS Secret Access Key [None]:* clave secreta de su usuario (generada por IAM)
- *Default region name [None]:* el nombre de la región, ej: us-east-1
- *Default output format [None]:* introduzca json

(las claves incluidas más arriba son ejemplos y no son válidas para el acceso)

### Utilizando la AWS CLI

**Trabajando con *buckets***
Primero podemos listar la lista de *buckets* que tenemos actualmente:
```bash
$ aws s3 ls
2017-08-08 16:33:33 iot-cloud-bucket-01
```
En este caso ya tenemos creado el *iot-cloud-bucket-1* que habíamos creado con la consola web.
Vamos a crear el *iot-cloud-bucket-2* mediante el comando *mb (make_bucket)*

```bash
$ aws s3 mb s3://iot-cloud-bucket-02
make_bucket: iot-cloud-bucket-02

$ aws s3 ls
2017-08-08 16:33:33 iot-cloud-bucket-01
2017-08-08 16:34:27 iot-cloud-bucket-02
```

Y podemos eliminar un *bucket* mediante *rb (remove_bucket)*:
```bash
$ aws s3 rb s3://iot-cloud-bucket-02
remove_bucket: iot-cloud-bucket-02

$ aws s3 ls
2017-08-08 16:33:33 iot-cloud-bucket-01
```

**Trabajando con *objetos***
Para cargar el archivo *logo.png* del directorio local de nuestra máquina a un nuevo *bucket*, utilizamos el comando *cp*:

```bash
$ aws s3 mb s3://iot-cloud-bucket-02
make_bucket: iot-cloud-bucket-02

$ aws s3 cp logo.png s3://iot-cloud-bucket-02
upload: .\logo.png to s3://iot-cloud-bucket-02/logo.png

$ aws s3 ls s3://iot-cloud-bucket-02
2017-08-09 15:02:04       1753 logo.png
```

Para descargar el objeto *logo.png* desde S3 a nuestro disco local, utilizamos también el comando *cp* simplemente alternando origen/destino. En este caso lo bajamos a nuestra máquina local con otro nombre *logo-2.png* para no sobrescribir el existente (opcional):
```bash
$ aws s3 cp s3://iot-cloud-bucket-02/logo.png ./logo-2.png
download: s3://iot-cloud-bucket-02/logo.png to .\logo-2.png

$ ls
logo.png  logo-2.png
```

Para eliminar un objeto del *bucket* utilizamos el comando *rm* :
```bash
aws s3 rm s3://iot-cloud-bucket-02/logo.png
delete: s3://iot-cloud-bucket-02/logo.png
```

Refs:
[AWS CLI Command References S3](http://docs.aws.amazon.com/cli/latest/reference/s3/)

---
## Folders

Amazon S3 es una solución de *object storage*, y tiene por tanto una estructura plana, sin la jerarquía de directorios que podemos encontrar en un típico filesystem.
Los *buckets* y los *objects* son los recursos principales, donde los objetos se almacenan dentro de los buckets.
Pero, con el objetivo de poder organizar mejor los datos, Amazon S3 soporta el concepto de *folders*, en el entendido que las mismas agrupan los objetos (pero sin crear una jerarquía como tal). Esto se realiza utilizando ***prefixes*** (prefijos) en las *keys* de los objetos.

Por ejemplo, dentro de un *bucket* se puede crear una carpeta llamada "fotos", y almacenar un ella un objeto llamado "mifoto.jpg". El objeto es entonces almacenado con el *key name* "fotos/mifoto.jpg", donde "fotos/" es el prefijo.

El concepto de prefijo es importante, dado que luego podremos utilizar diferentes funcionalidades / servicios realizando operaciones sobre ciertos objetos que contengan determinado prefijo (espero que mas adelante esto se entienda mejor).

Se pueden crear carpetas dentro de carpetas, pero no *buckets* dentro de *buckets*. Se pueden subir o copiar objetos directo a una carpeta, y los objetos se pueden mover de una carpeta a otra. Las carpetas se pueden crear, borrar, y hacer públicas, pero no se pueden renombrar.

Desde la consola web podemos crear un folder fácilmente, cuando estamos dentro de un *bucket*:
![alt text](./images/S3_folders_01.png)

Y podemos subir objetos de la misma forma que lo hicimos antes.
![alt text](./images/S3_folders_02.png)

También podemos subir objetos a carpetas utilizando la CLI, pero esta vez lo haremos de otra. Supongamos queremos subir una estructura de carpetas y archivos que ya tenemos en nuestro equipo. Subir los objetos uno a la vez podría resultar bastante tedioso.

Vamos a crear una estructura de ejemplo local en nuestra máquina primero:
```bash
$ mkdir carpeta-01; cd carpeta-01
$ mkdir docs
$ touch docs/file1.txt docs/file2.txt docs/file3.txt

```
Ahora subamos esos archivos de una forma más fácil.
Esto podemos hacerlo mediante el mismo comando *aws s3 cp* agregándole la opción *--recursive*
```bash
$ aws s3 cp . s3://iot-cloud-bucket-01/carpeta-01/ --recursive
upload: docs\file1.txt to s3://iot-cloud-bucket-01/carpeta-01/docs/file1.txt
upload: docs\file2.txt to s3://iot-cloud-bucket-01/carpeta-01/docs/file2.txt
upload: docs\file3.txt to s3://iot-cloud-bucket-01/carpeta-01/docs/file3.txt
```

O podemos sincronizar una carpeta local con todo su contenido, con el comando *aws s3 sync*:
```bash
$ mkdir logs
$ touch logs/log1.out logs/log2.out logs/log3.out

$ aws s3 sync . s3://iot-cloud-bucket-01/carpeta-01/
upload: logs\log3.out to s3://iot-cloud-bucket-01/carpeta-01/logs/log3.out
upload: logs\log1.out to s3://iot-cloud-bucket-01/carpeta-01/logs/log1.out
upload: logs\log2.out to s3://iot-cloud-bucket-01/carpeta-01/logs/log2.out

$ aws s3 ls s3://iot-cloud-bucket-01 --recursive
2017-08-15 21:20:24          0 carpeta-01/
2017-08-15 21:25:05          0 carpeta-01/docs/file1.txt
2017-08-15 21:25:05          0 carpeta-01/docs/file2.txt
2017-08-15 21:25:06          0 carpeta-01/docs/file3.txt
2017-08-15 21:28:31          0 carpeta-01/logs/log1.out
2017-08-15 21:28:31          0 carpeta-01/logs/log2.out
2017-08-15 21:28:30          0 carpeta-01/logs/log3.out
2017-08-15 21:19:27       1753 logo.png
```

El comando *sync* solo actualiza los archivos actuales y sube los nuevos, pero no borra objetos salvo que le agreguemos *--delete*:
```bash
$ rm logs/log3.out

$ aws s3 sync . s3://iot-cloud-bucket-01/carpeta-01/
((no hace nada))

$ $ aws s3 sync . s3://iot-cloud-bucket-01/carpeta-01/ --delete
delete: s3://iot-cloud-bucket-01/carpeta-01/logs/log3.out
```


Refs.:
[Working with Folders](http://docs.aws.amazon.com/es_es/AmazonS3/latest/UG/FolderOperations.html)
[AWS CLI Command References S3](http://docs.aws.amazon.com/cli/latest/reference/s3/)


---
[Siguiente >](https://github.com/conapps/conapps-iot/blob/master/AWS%20Cloud/S3/20170810_AWS_S3_Parte_2.md)