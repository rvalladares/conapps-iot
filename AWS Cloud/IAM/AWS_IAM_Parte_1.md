Amazon Identity & Access Management (Amazon IAM)
===

*Fuentes:*

- [Documentación oficial](https://aws.amazon.com/iam)
- [Cloud academy](https://cloudacademy.com/amazon-web-services/overview-of-aws-identity-and-access-management-iam-course/)

---

## Indice

- [Introducción](#Introduccion)
- [Características](#Características)
- [_Identity vs Access Management_](#Identity-vs-Access-Management)
- [Identificación de usuarios y tipos de acceso](#Identificación-de-usuarios-y-tipos-de-acceso)
    - [_Access Key ID + Secret Access Key_](#Access-Key-ID-+-Secret-Access-Key)
- [El usuario root](#El-usuario-root)
- [Tipos de usuarios](#Tipos-de-usuarios)
    - [Usuarios IAM](#Usuarios-IAM)
    - [Usuarios Federados](#Usuarios-Federados)
    - [Caso de uso](#Caso-de-Uso-Usuarios)
- [IAM _Groups_](#IAM-Groups)
- [Ejercicio de Usuarios y Grupos](#Ejercicio-#-1-y-2)
- [IAM Roles](#Roles)
    - [Tipos de roles](#Tipos-de-roles)
    - [Caso de uso](#Caso-de-uso-Roles)
- [Cuando usar Roles, Grupos y/o Usuarios](#Cuando-usar-Roles,-Grupos-y/o-Usuarios)
- [IAM _Policies_](#IAM-_Policies_)
    - [Tipos de Políticas](#Tipos-de-Políticas)
- [Ejercicio de Políticas y Roles](#Ejercicio-#-3)
- [Ejercicio Integrador Final](https://github.com/conapps/conapps-iot/blob/master/AWS%20Cloud/IAM/ejercicios/AWS_IAM_EjercicioFinal.md)
- [IAM Best Practices](#_IAM-Best-Practices_)
- [Desafío 126 años de gloria]()
    

---
## Introduccion

¿Qué es Amazon IAM?

Amazon _Identity and Access Management_ (IAM) es un servicio que nos ayuda a controlar de forma segura el acceso a los servicios y recursos de AWS.

## Características

Algunas de las características más importantes que otorga IAM son las siguientes:

- **Acceso compartido**

Podemos conceder permisos a otros usuarios para administrar y utilizar recursos de AWS sin tener que compartir nuestra clave de acceso.

- **Permisos granulares**

Podemos otorgarles distintos tipos de permisos a diferentes usuarios sobre diferentes recursos. Ej; A algunos usuarios podemos darle _full access_ sobre EC2 y S3, y a otros usuaros de AWS podemos darle _read-only_ sobre algunos buckets de S3, o permisos para administrar algunas instancias de EC2.

- **Acceso seguro para aplicaciones sobre EC2**

Podemos utilizar las funciones de IAM para otorgar credenciales de acceso a aplicaciones que se ejecutan sobre EC2 y que necesitan acceder a otros recursos de AWS, por ejemplo; buckets de S3, una base RDS o  una base DynamoDB.

- **Multi-factor authentication (MFA)**

Podemos agregar autenticación en 2 pasos para mayor seguridad. Los usuarios no solo deberán otorgar la clave de acceso, sino que también, un código que será enviado a algún dispositivo pre-configurado.
Dicho dispositivo podrá ser un _token_ específico o un _virtual token_ como lo puede ser un _smartphone_.

- **Federación de identidad**

Podemos darle acceso a otros usuarios (usuarios que tengan cuenta en otro _tenant_ de AWS o estén autenticados en otros sistemas) y que no pertenezca a nuestra organización.

- **Información de auditoría**

Si estamos utilizando [AWS CloudTrail](https://aws.amazon.com/es/cloudtrail/) recibiremos logs que incluyen registros de peticiones de acceso a recursos de nuestra cuenta basadas en información de IAM.

- **_Payment Card Industry_ (PCI) & _Data Security Standard_ (DSS)**

IAM soporta el manejo de información y transacciones asociadas a las tarjetas de crédito.

- **Integración con otros servicios de AWS**

Es posible integrar las funciones de IAM con otros servicios de AWS. Generalmente utilizando la funcion de Roles.

- **Eventualmente Consistente**

Al igual que muchos otros servicios de AWS, IAM es eventualmente consistente. Esto quiere decir que pueden existir determinados períodos de tiempo donde la información no esté replicada 100% en las distintas _Availability Zones_ y por lo tanto, podrían existir inconsistencias de información mientras se ejecuta la replicación.

- **Sin cargos extra**

IAM es uno de los pocos servicios de AWS que se ofrecen sin cargo extra.


Refs: Más información sobre el standard de seguridad de los datos [PCI DSS](https://aws.amazon.com/es/compliance/pci-dss-level-1-faqs/).

---
## _Identity vs Access Management_

Es importante entender la diferencia entre el concepto de _Identity_ y el de _Access Management_.

- **_Identity_**

Cuando nos referimos a _Identity_, estamos hablando de identidad y de **como vamos a identificar al usuario**.

- **_Access Management_**

 Cuando hablamos de _Access Management_, estamos hablando de **qué es lo que el usuario va a poder hacer dentro de AWS**. Básicamente estamos hablando de qué es lo que el usuario va a poder realizar dentro de AWS.

**Por defecto, los usuarios (salvo root) no tienen permisos para acceder a ningún recurso**, salvo que se indique lo contrario mediante políticas.

---
## Identificación de usuarios y tipos de acceso

Podemos identificar a un usuario de IAM de varias formas.

- **_User + Password_**

    - Nos brinda acceso a AWS mediante la _Management Console_.

- **_Access Key ID + Secret Access Key_**

    - Nos brinda acceso a AWS mediante las siguientes modalidades:
        - AWS _Command Line Tools_
        - AWS SDKs
        - HTTPS API REST

---

## _Access Key ID + Secret Access Key_

En el caso que necesitemos que un usuario tenga acceso a los servicios de AWS de forma programática, es necesesario generar un par de claves llamadas: _access key pair_. 

Una _access key pair_ está integrada por:

- **_Access Key Id_:**
    - _Uppercase String_ alfanumerico de 20 digitos (numeros y letras mayúsculas).
    - Si se crea una, estará activa por defecto (se puede deshabilitar).
    - Se puede generar hasta 2 AKI por usuario (útil para rotación de _keys_).
    - Se pueden borrar (no se pueden recuperar).
    - Podemos darle permisos al _owner_ de las _keys_ para que pueda administrar sus propias _keys_.

- **_Secret Access Key_:**
    - _String_ de 40 caracteres de longitud.
    - Solo disponible cuando se crea el usuario. 
    - **Importante** no perderla porque no es posible recuperarla.


- **_Configuración Inicial_**
```bash
$ aws configure

AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-east-2
Default output format [None]: json
```

---

## El usuario root

Cuando crearmos por primera vez una cuenta en AWS, por defecto estamos creando una cuenta _root_. Con estas credenciales, podemos acceder a la consola de administración.

**Cuando utilizamos la cuenta root, tenemos acceso completo a todos los servicios y recursos de AWS, incluyendo la facturación. AWS recomienda no utilizar la cuenta _root_** para el trabajo diario, y recomienda crear otros usuarios con los permisos necesarios.

---
# Tipos de usuarios

## Usuarios IAM

Como vimos anteriormente, el concepto de _Identity_
nos permite responder la pregunta ¿quien es ese usuario?. En lugar de compartir la clave de _root_, podemos crear otras cuentas IAM que corresponderán a personas físicas en nuestra empresa, y que tendrán sus propias credenciales para acceder a la consola de administración y/o acceso de forma programática.

**Nota:** Los usuarios IAM no son cuentas separadas de AWS.

En la figura siguiente, Brad, Jim, DevApp1, DevApp2, TestApp1, y TestApp2 son usuarios IAM que fueron creados dentro de una sola cuenta de AWS, y cada usuario tiene sus propias credenciales.

![IAM Users](images/IAM_users.png)

Notarán que algunos usuarios son usuarios de aplicaciones. Por lo que **un usuario IAM no necesariamente tiene que representar a una persona física**. Podemos generar un usuario para una aplicación que corre en nuestra red empresarial y necesita acceso a recursos de AWS.

## Usuarios Federados

Si un usuario ya cuenta con una forma de autenticación habilitada, es posible federar dichos usuarios dentro de AWS. Otorgando acceso mediante _Management Console_ y mediante API, CLI o SDK. 

## ¿Cuando utilizar usuarios federados?

- Usuarios corporativos: 

Si tenemos un directorio de usuarios compatible con _Security Assertion Markup Language 2.0_ [(SAML 2.0)](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_enable-console-saml.html), podemos configurar _Single-Sign On_ (SSO) para que los usuarios de nuestra red empresarial puedan acceder a servicios y recursos de AWS.

- _Identity Providers_:

Si estamos creando una aplicación para celulares o una aplicación web, podemos permitirle al usuario que se autentique a travez de un _Identity provider_ como Facebook, Google o cualquier otro proveedor que sea compatible con [_OpenID Connect_](http://openid.net/connect/) (OIDC). AWS recomienda utilizar [Amazon Cognito](https://aws.amazon.com/es/cognito/).

---
## IAM _Groups_

Es posible organizar usuarios dentro de grupos IAM. Básicamente, un grupo es una colección de usuarios.

Todos los usuarios dentro de un grupo tienen los mismos permisos. Es una forma fácil de agrupar usuarios y permisos.

Por ejemplo: En la figura siguiente tenemos un grupo llamado **Admins**, otro **Developers** y otro **Test**. Los usuarios que estan dentro del grupo Admin, tienen los permisos que se hayan definido para los Administradores. Si un nuevo usuario administrador ingresa a la empresa, bastará con agregarlo al grupo para que tenga los mismos permisos que los otros administradores.

Otros ejemplos comunes: un usuario perteneciente al grupo de Test es promovido a developer. Por ejemplo; en este caso, agregaremos a Cathy al grupo Developers y la borraremos del grupo Test.

![IAM Groups](images/IAM_groups1.PNG)

Los permisos son atacheados a los grupos mediante políticas, ya sean predefinidas por AWS o creadas por nosotros mismos.

De esta froma, los usuarios dentro del grupo, heredan dichos permisos. Solo basta con modificar la política del grupo para cambiar los permisos sobre todos los usuarios.

- **Máximo de 100 grupos por cuenta de AWS**. En caso de necesitar más, es necesario generar un ticket en AWS.
- **Un usuario puede ser asociado a un máximo de 10 grupos**.

---
## [Ejercicio # 1 y 2](ejercicios/AWS_IAM_Users&Groups.md)
---

## Roles

Un rol es muy parecido a un usuario, en el sentido de que se le pueden asignar permisos y luego ser atacheado a un usuario federado, a una aplicación o un servicio que generalmente no tiene acceso a AWS. Sin embargo, **un rol no tiene ningun tipo de credenciales gestionadas por nosotros (user/password o access keys)**.


### Imaginen la siguiente situación:

Una aplicación que corre en una instancia de EC2 necesita acceso a un _bucket_ de S3, tenemos 2 formas de hacerlo.

- **Forma 1**: Creamos un usuario en IAM, le damos permisos de _Put_ y _Get_ sobre el _bucket_, por último,  embebemos el usuario y la password en el código dentro de la aplicación o en la instancia de EC2.
    
![IAM Groups](images/IAM_role2.PNG)

- **Forma 2**: Creamos un rol asociando una _policie_ con permisos de _Put_ y _Get_ sobre el _bucket_, por último, asociamos dicho rol a la instancia de EC2


![IAM Groups](images/IAM_role3.PNG)

- **Pregunta:** ¿Cuales son las desventajas de la forma 1 y cuales las ventajas de la forma 2?

---
### Tipos de roles

Existen 4 tipos de roles que podemos crear en AWS. A continuación un breve explicación de cada uno.

- **AWS Service Role**
    - El rol que asume un servicio para ejecutar determinada accion en nuestro nombre se denomina _service role_. (Ej: _replication_policy_for_iot-cloud-bucket-origen_to_iot-cloud-bucket-destino_).
    - En otras palabras, este tipo de rol, es asumido por un servicio para ejecutar determinada acción. 
    - Muchos servicios de AWS requieren tener asociado un rol para ejecutar las acciones.    
    - Dicho rol, debe incluir todos los permisos necesarios para acceder a los recursos de AWS que necesite.    
    - Se puede crear, modificar y borrar _Services Roles_ desde IAM.

![IAM Groups](images/IAM_role5.PNG)

- **AWS Service-Linked Role**
    - _Service-Linked Role_ es el único tipo de _service role_ que está directamente "linqueado" al servicio.
    - El rol está predefinido por el servicio e incluye todos los permisos que el servicio requiere.
    - Facilita el _setup_ porque no es necesario configurar los permisos manualmente.
    - No es posible modificar los permisos asociados a este este tipo de rol.


![IAM Groups](images/IAM_role4.PNG)

- **Rol for Cross-Account Access**

![IAM Groups](images/IAM_role6.PNG)

- **Rol for identify Provider Access**

![IAM Groups](images/IAM_role7.PNG)


---
## Caso de uso Roles

**EC2 app + S3 _Bucket_**

![IAM Groups](images/IAM_role12.png)

Como vemos en la figura anterior, un desarrollador ejecuta una aplicación sobre una instancia de EC2 la cual requiere acceso a un _bucket_ de S3 llamado _photos_.

Por otro lado, un administrador crea un rol llamado _get-pics_ al cual se le "adjunta" una _policy_ que tiene permisos de lectura sobre el _bucket_, y se le permite al desarrolladaor ejecutar la instancia con dicho rol asociado.

Como vemos a continuación, la aplicación que corre sobre la instancia, puede hacer uso de las credenciales temporales que genera el rol para acceder al _bucket_ ejecutando el siguiente comando.

 ```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/s3access
```

```bash
{
  "Code" : "Success",
  "LastUpdated" : "2012-04-26T16:39:16Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ASIAIOSFODNN7EXAMPLE",
  "SecretAccessKey" : "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
  "Token" : "token",
  "Expiration" : "2017-05-17T15:09:54Z"
}
```

* Ejemplo de uso en Python

```bash
assumeRoleResult = AssumeRole(a);
tempCredentials = new SessionAWSCredentials(
   assumeRoleResult.AccessKeyId, 
   assumeRoleResult.SecretAccessKey, 
   assumeRoleResult.SessionToken);
s3Request = CreateAmazonS3Client(tempCredentials);
```

De esta forma se obtiene la _Access Key Id_ y la _Secret Access Key_ necesarias para poder acceder al _bucket_ de S3 como lo vimos en la [clase de S3](https://github.com/conapps/conapps-iot/blob/master/AWS%20Cloud/S3/AWS_S3.md).

El administrador no necesita darle permisos al desarrollador para acceder al _bucket_ y tampoco el desarrollador debe compartir ni "hardcodear" sus _keys_ en la instancia o aplicación.

Refs:

- [Id_roles_use_switch-role-ec2](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html)


---
## Cuando usar Roles, Grupos y/o Usuarios

- Usuario
    - Cuando se crea una cuenta en AWS y solo existe un usuario que va a trabajar en AWS.
    - Cuando otras personas de la companía necesitan trabajar sobre AWS.
    - Cuando se desea utilizar CLI, API o SDK para trabajar en AWS.

- Grupos
    - Cuando tenemos un conjunto de usuario que realizarán las mismas tareas dentro de AWS.
    - Para facilitar la administración de los permisos.
    
- Rol
    - Cuando tenemos aplicaciones corriendo sobre algún servicio de AWS (Ej; EC2 y la aplicación necesita acceder a otros servicios de AWS (Ej S3, RDS, etc).
    - Cuando deseamos utilizar _features_ de algunos servicios los cuales si o si necesitan tener un rol asignados (Ej; Amazon S3 _region replication_).
    - Cuando los usuarios ya estan autenticados en la red empresarial o por _Identity Providers_ y necesitan utilizar AWS sin la necesidad de tener que loguearse nuevamente (SSO).

---

## IAM _Policies_

Las IAM _Policies_ son utilizadas para asignar permisos a usuarios, grupos y roles sobre los recursos y servicios de AWS.

El formato de definición de las políticas es JSON y la estructura base es la siguiente:

```bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt245245254535",
      "Action": "s3:*",
      "Effect": "Allow",
      "Resource": "*",
      "Condition": {
          "IpAddress": {
              "aws:SourceIp": "10.10.0.0/16"
          }
      }
    }
  ]
}
```

- **Version:**
    - Debe aparecer antes que el Statement.
    - Solo acepta 2 valores (2012-10-17 o 2008-10-17). 
    - Si no se especifica, toma 2008 por defecto. 
    - Con la versión 2008 hay _features_ que no funcionan (Ej; _policy variables_).

- **Statement:** 
    - Elemento principal de la _policy_.
    - Elemento obligatorio.
    - Incluye varios elementos dentro de un array json.
    - Formato: "Statement": [{...},{...},{...}].

- **Sid:**
    - Elemento opcional, se puede asignar un sid a cada statement.
    - Es un sub-id asignado al _statement_ que puede ser utilizados por algunos servicios como SQS y SNS.

- **Acction:**
    - Elemento obligatorio.
    Describe la/s acción/es específica/s que serán permitidas o denegadas dependiendo del campo _Effect_.
    - Cada servicio de AWS tiene su propio conjutno de tareas a realizar/denegar.
    - No es _key sensitive_ -> iam:ListAccessKeys = IAM:listaccesskeys.
    - Se pueden concatenar acciones: "Action": [ "sqs:SendMessage", "sqs:ReceiveMessage", "ec2:StartInstances", "iam:ChangePassword", "s3:GetObject"].
    - Se pueden utilizar _wildcards (*)_: "Action": "s3:*" o "Action": "sqs:\*Message".

- **Effect:**
    - Solo acepeta 2 opciones: Allow o Deny.

- **Resource:**
    - Indica el recurso o recursos que cubre el statement.
    - Se utiliza el nombre ARN.
    ```bash
    "Resource": "arn:aws:s3:us-east-2:user-account-ID:my_corporate_bucket/*"
    ```

- **Condition:**
    - Campo opcional.
    - Permite setear condiciones para ejecutar la política.
    - Pueden incluir fechas, horas, ip origen, usuario, etc.
    ```bash
    "Condition": {
          "IpAddress": {
              "aws:SourceIp": "10.10.0.0/16"
          }
      }
    ```

Refs:

[AWS Reference Policies Elements](http://docs.aws.amazon.com/es_es/IAM/latest/UserGuide/reference_policies_elements.html)

[AWS Amazon Resources Names](http://docs.aws.amazon.com/es_es/general/latest/gr/aws-arns-and-namespaces.html)

[AWS Global Conditions Keys](http://docs.aws.amazon.com/es_es/IAM/latest/UserGuide/reference_policies_condition-keys.html#AvailableKeys)

---

## Tipos de Políticas

- **AWS Managed Policies** 

Son políticas pre-creadas por AWS y pueden ser asociadas a Grupos, Roles y Usuarios (no recomendado)

- **Customer Managed Policies**

Son políticas creadas por el propio usuario y existen 3 formas de hacerlo:

- Copiar una _Managed Policy_ y editarla.
- Utilizar el generador de policies.
- Escribir el json desde 0.

---
## Resolución de conflictos de permisos.

- Por defecto el acceso a todos los recursos esta prohibido.
- Solo se otorgará acceso a un recurso si existe un "Allow".
- Si existe un "Deny", sobreescribirá cualquier "Allow" que exista anteriormente.

![IAM Policy](images/IAM_policy3.png)


---
## [Ejercicio # 3](ejercicios/AWS_IAM_Policies.md) & [Ejercicio # 4](ejercicios/AWS_IAM_Roles.md) & [Ejercicio Final](ejercicios/AWS_IAM_EjercicioFinal.md)
---


## _IAM Best Practices_

AWS recomienda seguir la siguiente guía para mejorar la seguridad sobre los recursos de AWS.

* Eliminar las _Access Keys_ para el usuario root.
* Crear usuarios individuales.
* Utilizar las Políticas definidas por AWS para asignar permisos donde sea posible.
* Utilizar grupos para asignar permisos a usuarios.
* Aplicar la regla de permisos mínimos.
* Configurar políticas de complejidad de claves.
* Habilitar MFA para usuarios con permisos elevados (root, administrator, etc).
* Utilizar roles para aplicaciones que corren sobre instancias de EC2.
* Delegar utilizando roles en lugar de compartir las credenciales.
* Rotar las credenciales regularmente.
* Eliminar usuarios y claves sin utilizar.
* Utilizar el campo "Conditions" cuando definimos _policies_ para mayor seguridad.
* Monitorear la actividad en nuestro _tenant_ de AWS (CloudTrial, CloudWatch).