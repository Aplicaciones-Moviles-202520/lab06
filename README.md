# Laboratorio 6: Introducción a AWS

En este laboratorio introduciremos los conceptos básicos de computación en la nube (cloud computing) utilizando Amazon Web Services (AWS). Aprenderemos a crear y configurar una máquina virtual EC2, instalar un servidor web Apache, y hospedar un sitio web estático utilizando S3. Este laboratorio te permitirá entender las bases de la infraestructura cloud y cómo desplegar aplicaciones web en la nube.

## Marco Teórico

### ¿Qué es la computación en la nube?

La computación en la nube (cloud computing) es la entrega de servicios de computación a través de Internet ("la nube"). En lugar de comprar, poseer y mantener servidores físicos, puedes acceder a servicios como poder de procesamiento, almacenamiento, bases de datos y otras funcionalidades según las necesites.

### Amazon Web Services (AWS)

AWS es la plataforma de servicios en la nube más completa y ampliamente adoptada del mundo. Ofrece más de 200 servicios completos desde centros de datos a nivel global. Los servicios principales que utilizaremos en este laboratorio son:

#### EC2 (Elastic Compute Cloud)

Servicio que proporciona capacidad de procesamiento redimensionable en la nube. Es esencialmente un servidor virtual que puedes configurar y usar como si fuera una computadora física.

**Características principales:**

-   Instancias virtuales con diferentes configuraciones de CPU, memoria y almacenamiento
-   Control total sobre el sistema operativo
-   Escalabilidad: puedes aumentar o disminuir recursos según necesites
-   Pago por uso: solo pagas por el tiempo que uses la instancia

#### S3 (Simple Storage Service)

Servicio de almacenamiento de objetos escalable que permite almacenar y recuperar cualquier cantidad de datos desde cualquier lugar.

**Características principales:**

-   Almacenamiento prácticamente ilimitado
-   Durabilidad del 99.999999999% (11 9's)
-   Posibilidad de hospedar sitios web estáticos
-   Integración con otros servicios de AWS

### Tipos de instancias EC2

AWS ofrece diferentes tipos de instancias optimizadas para diferentes casos de uso:

-   **t2.micro**: Instancia de uso general pequeña, incluida en la capa gratuita
-   **t2.small/medium**: Instancias de uso general con más recursos
-   **m5.large**: Instancias balanceadas para cargas de trabajo generales
-   **c5.xlarge**: Instancias optimizadas para cómputo intensivo

Para este laboratorio utilizaremos **t2.medium** que ofrece un buen balance entre recursos y costo para aplicaciones web básicas.

## Pasos iniciales

### Requisitos previos

Antes de comenzar, asegúrate de tener:

1. Acceso a AWS Academy Learner Lab
2. Navegador web
3. Conocimientos básicos de línea de comandos de Linux
4. Editor de texto para crear archivos HTML

## 1. Acceso a AWS Academy

AWS Academy proporciona acceso gratuito a AWS para fines educativos. AWS Academy funciona sobre la plataforma Canvas. Canvas es un proveedor de LMS (Learning Management System) que sirve a múltiples instituciones educativas.

### Paso 1.1: Ingreso a AWS Academy

1. Revisa el correo electrónico que recibiste como invitación a AWS Academy
2. Al ingresar te preguntará si tienes una cuenta en Canvas. La respuesta es NO, ya que las credenciales para Canvas de la Universidad son para acceso a esta institución, y no para AWS Academy
3. Completa el proceso de creación de cuenta
4. Busca el curso "AWS Academy Learner Lab" y haz clic en el curso para acceder

### Paso 1.2: Inicio del Learner Lab

1. Una vez dentro del curso, dirígete a la sección "Modules" y luego "Launch AWS Academy Learner Lab"
2. Haz clic en "Start Lab" (puede tomar unos minutos en inicializar, especialmente la primera vez)
3. Espera a que el indicador cambie a verde
4. Haz clic en "AWS" para acceder a la consola de AWS

**⚠️ Importante:** La sesión de Learner Lab dura 4 horas, después de las cuales los servicios se apagan automáticamente. El tiempo de funcionamiento se puede extender haciendo clic en "Start Lab" nuevamente, lo que prolongará la sesión por 4 horas adicionales.

### Paso 1.3: Familiarización con la consola de AWS

Una vez en la consola de AWS:

1. Observa la barra de navegación superior
2. Identifica la región actual (debería ser us-east-1 por omisión)
3. Familiarízate con la búsqueda de servicios en la parte superior
4. Explora el menú "All services" (colapsado en el menú del lado izquierdo) para ver la variedad de servicios disponibles
5. Identifica los servicios EC2 y S3 y agrégalos como favoritos

## 2. Creación y configuración de una instancia EC2

Antes de comenzar con la creación y configuración de una instancia EC2 (EC2 &rarr; ECC &rarr; Elastic Compute Cloud), es importante entender qué es una instancia EC2 y por qué es fundamental en el ecosistema de AWS: Una instancia EC2 es un servidor virtual en la nube que puedes lanzar, detener, reiniciar y eliminar según tus necesidades. Es el equivalente a tener una computadora física, pero con la flexibilidad y escalabilidad que ofrece la nube.

En esta sección aprenderás paso a paso cómo lanzar tu primer servidor virtual en AWS, conectarte a él de forma segura y prepararlo para hospedar aplicaciones web. Comenzaremos desde la consola de AWS, por lo que asegúrate de haber completado los pasos previos de acceso a AWS Academy y familiarización con la consola.

### Paso 2.1: Crear un par de claves SSH

Las claves SSH son necesarias para conectarse de forma segura a las instancias EC2. Crearemos una antes de lanzar la instancia.

1. En la consola de AWS, busca "EC2" en la barra de búsqueda o utiliza el ícono que se creó al agregarlo como favorito
2. Haz clic en "EC2" para acceder al dashboard del servicio
3. Asegúrate de estar en la región correcta (us-east-1)
4. En el panel izquierdo, busca la sección "Network & Security" y haz clic en "Key Pairs"
5. Haz clic en "Create key pair" (Crear par de claves)
6. Configura los siguientes parámetros:
    - **Name**: "lab06-keypair"
    - **Key pair type**: RSA
    - **Private key file format**: .pem
7. Haz clic en "Create key pair"
8. **Importante**: Identifica dónde se descargó el archivo .pem

**⚠️ Nota de seguridad**: Mantén este archivo seguro. No podrás descargarlo nuevamente y lo necesitarás para conectarte a tu instancia.

### Paso 2.2: Lanzar una nueva instancia con Ubuntu 24.04

1. Ve a "Instances" en el panel izquierdo del dashboard EC2
2. Haz clic en "Launch Instance" (Lanzar instancia)
3. Configura los siguientes parámetros:

#### Name and tags

-   **Name**: "lab06-ubuntu-server"

#### Application and OS Image (AMI)

-   Selecciona Ubuntu como sistema operativo
-   En la pestaña "Quick Start", selecciona "Ubuntu" y luego "Ubuntu Server 24.04 LTS" (la versión más reciente disponible, estará seleccionada por omisión)
-   Esta es la distribución Ubuntu 24.04 LTS oficial

#### Instance type

-   Selecciona "t2.medium"
-   Esto te dará 2 vCPUs y 4 GB de RAM, suficiente para un servidor web básico

#### Key pair

-   **Key pair name**: Selecciona "lab06-keypair" (la clave que creaste en el paso anterior)

#### Network settings

-   **VPC**: Deja la VPC por omisión
-   **Subnet**: Deja la subnet por omisión
-   **Auto-assign public IP**: Enable (Habilitar)
-   **Firewall (security groups)**: Se debe dejar la configuración por omisión

#### Configure storage

-   **Size**: 8 GB (por omisión)
-   **Volume type**: gp3 (por omisión)

### Paso 2.3: Lanzar la instancia

1. Revisa todas las configuraciones en el resumen
2. Haz clic en "Launch Instance" (Lanzar instancia)
3. Deberías ver un mensaje de éxito con el ID de la instancia
4. Haz clic en "View all instances" para ir al dashboard de instancias
5. Espera a que el "Instance state" cambie a "Running"
6. Verifica que "Status check" muestre "2/2 checks passed"

### Paso 2.4: Configurar una dirección IP elástica

Una IP elástica garantiza que tu servidor mantenga la misma dirección IP pública incluso si se reinicia.

1. En el panel izquierdo del dashboard EC2, busca "Network & Security" → "Elastic IPs"
2. Haz clic en "Allocate Elastic IP address"
3. Deja todas las configuraciones por omisión
4. Haz clic en "Allocate"
5. Selecciona la IP elástica recién creada
6. Haz clic en "Actions" → "Associate Elastic IP address"
7. Configura:
    - **Resource type**: Instance
    - **Instance**: Selecciona tu instancia "lab06-ubuntu-server"
    - **Private IP address**: No editar (se completa automáticamente)
8. Haz clic en "Associate"
9. Identifica la dirección IP elástica asignada
10. Dirígete a "Instances" y selecciona tu instancia para verificar que la IP elástica está asociada correctamente

**Importante**: Anota la dirección IP elástica asignada, la necesitarás para conectarte.

### Paso 2.5: Conectar por consola web (EC2 Instance Connect)

AWS proporciona una forma fácil de conectarse a instancias Ubuntu sin configurar claves localmente:

1. Selecciona tu instancia en el dashboard
2. Haz clic en "Connect" (Conectar)
3. En la pestaña "EC2 Instance Connect", verifica que:
    - **Connection type**: "Connect using EC2 Instance Connect"
    - **Username**: ubuntu (por omisión para Ubuntu)
4. Haz clic en "Connect"
5. Se abrirá una nueva ventana con una terminal en el navegador
6. Ejecuta algunos comandos básicos de Linux:

```bash
ls -la
pwd
whoami
```

### Paso 2.6: Conectar usando tu clave SSH local

Ahora aprenderás a conectarte desde tu computadora local usando la clave SSH. Existen dos escenarios principales: Windows y Linux (incluyendo macOS).

#### En Windows

```bash
# Conectarse a la instancia (reemplaza TU-IP-ELASTICA por la IP real)
ssh -i ruta/a/lab06-keypair.pem ubuntu@TU-IP-ELASTICA
```

#### En Linux o macOS

```bash
# Cambiar permisos de la clave (solo la primera vez)
chmod 400 ~/Downloads/lab06-keypair.pem

# Conectarse a la instancia
ssh -i ~/Downloads/lab06-keypair.pem ubuntu@TU-IP-ELASTICA
```

**Ejemplo**:

```bash
ssh -i ~/Downloads/lab06-keypair.pem ubuntu@12.34.56.78
```

### Paso 2.7: Instalar Apache Web Server

Una vez conectado a tu instancia por SSH, ejecuta los siguientes comandos:

```bash
# Actualizar el sistema
sudo apt update && sudo apt upgrade -y

# Instalar Apache
sudo apt install apache2 -y

# Verificar que Apache se instaló correctamente
apache2 -version

# Iniciar Apache y habilitarlo para inicio automático
sudo systemctl start apache2
sudo systemctl enable apache2

# Verificar que Apache está ejecutándose
sudo systemctl status apache2
```

Deberías ver una salida que indique que Apache está activo (running).

### Paso 2.8: Verificar el acceso al sitio web

Ahora probaremos que no podemos acceder al servidor web desde Internet debido a la configuración del grupo de seguridad:

1. Abre tu navegador web
2. Intenta acceder a: `http://TU-IP-ELASTICA`
3. **Esperado**: La página no carga o muestra un error de timeout/connection refused

Esto es normal y esperado porque no hemos abierto el puerto 80 en el grupo de seguridad.

### Paso 2.9: Configurar reglas de entrada (Inbound Rules) en el Security Group

Ahora abriremos el puerto 80 para permitir tráfico HTTP:

1. Ve al dashboard de EC2 y selecciona tu instancia
2. En el panel inferior que se despliega, selecciona la pestaña "Security"
3. Haz clic en el enlace del "Security Group"
4. Ve a la pestaña "Inbound rules" (Reglas de entrada)
5. Haz clic en "Edit inbound rules" (Editar reglas de entrada)
6. Haz clic en "Add rule" (Agregar regla)
7. Configura la nueva regla:
    - **Type**: HTTP
    - **Port range**: 80
    - **Source**: Anywhere-IPv4 (0.0.0.0/0)
    - **Description**: "Allow HTTP traffic"
8. Haz clic en "Save rules" (Guardar reglas)

### Paso 2.10: Verificar acceso a la página demo de Apache

Ahora que el puerto 80 está abierto, verifica que el servidor web es accesible:

1. Ve a tu navegador web
2. Accede a: `http://TU-IP-ELASTICA`
3. **Esperado**: Deberías ver la página por omisión de Apache que dice "Apache2 Ubuntu Default Page"

¡Excelente! Tu servidor web está funcionando correctamente y es accesible desde Internet.

### Paso 2.11: Detener y eliminar la instancia EC2

En este paso aprenderás la diferencia entre detener y eliminar (terminar) una instancia EC2:

#### Detener la instancia (Stop)

Detener una instancia apaga el sistema operativo, pero conserva tanto la configuración como el disco (volumen EBS). Puedes reiniciarla más tarde y todo seguirá igual.

1. Ve al dashboard de EC2 &rarr; "Instances"
2. Selecciona tu instancia "lab06-ubuntu-server"
3. Haz clic en "Instance State" &rarr; "Stop instance"
4. Espera a que el estado cambie a "stopped"

**Nota:** Mientras la instancia está detenida, no genera cargos por cómputo, pero sí por almacenamiento EBS.

#### Eliminar (terminar) la instancia (Terminate)

Eliminar (terminar) una instancia borra la máquina virtual de forma permanente. Por omisión, también elimina el disco (volumen EBS) asociado, a menos que se haya configurado lo contrario.

1. Con la instancia detenida o en ejecución, selecciónala en el dashboard EC2
2. Haz clic en "Instance State" &rarr; "Terminate instance"
3. Confirma la terminación cuando se te solicite
4. La instancia cambiará a estado "Terminating" y luego "Terminated"

**Diferencia clave:**

-   **Detener**: Puedes volver a iniciar la instancia y conservar los datos.
-   **Eliminar**: La instancia y su almacenamiento se eliminan permanentemente (a menos que el volumen EBS esté configurado para conservarse).

### Paso 2.12: Liberar la dirección IP elástica

Finalmente, libera la IP elástica para evitar cargos innecesarios:

1. Ve a "EC2" → "Network & Security" → "Elastic IPs"
2. Selecciona tu dirección IP elástica
3. Haz clic en "Actions" → "Release Elastic IP addresses"
4. Confirma la liberación

**⚠️ Importante**: Las IP elásticas no utilizadas (no asociadas a una instancia en ejecución) generan cargos. Asegúrate de liberarlas cuando no las necesites.

## 4. Creación de sitio web estático con S3

### Paso 4.1: Acceso al servicio S3

1. En la consola de AWS, busca "S3" en la barra de búsqueda
2. Haz clic en "S3" para acceder al servicio
3. Verás la lista de buckets (inicialmente vacía)

### Paso 4.2: Crear un bucket S3

1. Haz clic en "Create bucket" (Crear bucket)
2. Configura los siguientes parámetros:

#### General configuration

-   **Bucket type**: Dejar en "General purpose"
-   **Bucket name**: "[TU-RUT]-icc4203" (reemplaza [TU-RUT] con tu RUT sin puntos ni guión, usando solo letras minúsculas y números)

#### Object ownership

-   Selecciona "ACLs enabled" (ACLs habilitadas)
-   Marca "Bucket owner preferred"

#### Block public access for this bucket

-   **Desmarcar** "Block all public access" (Bloquear todo el acceso público)
-   Aparecerá una advertencia - marca la casilla de confirmación que dice "I acknowledge that the current settings might result in this bucket and the objects within becoming public"

#### Bucket versioning

-   Deja "Disable" (Deshabilitado) para simplicidad

#### Etiquetas

-   No editar

#### Default encryption

-   Deja la configuración por omisión (Server-side encryption with Amazon S3 managed keys)

3. Haz clic en "Create bucket"

### Paso 4.3: Crear página y subirla al bucket

1. En tu computador, crea un archivo llamado `index.html` con el siguiente contenido:

```html
<!DOCTYPE html>
<html lang="es">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Mi Sitio Web en S3</title>
    </head>
    <body>
        <div class="container">
            <div class="header">
                <h1>Bienvenido a Mi Sitio Web en S3</h1>
            </div>
            <div class="content">
                <p>Este es un sitio web estático alojado en Amazon S3.</p>
            </div>
        </div>
    </body>
</html>
```

2. En la consola de S3, haz clic en el nombre de tu bucket recién creado
3. Ve a la pestaña "Objects" (Objetos)
4. Haz clic en "Upload" (Subir)
5. Haz clic en "Add files" (Agregar archivos) y selecciona el archivo `index.html` que creaste
6. Haz clic en "Upload" (Subir) para subir el archivo
7. Una vez subido, deberías ver `index.html` en la lista de objetos del bucket. Revisa como funciona "object url", "Open" y Object actions &rarr; Shared with presigned URL

### Paso 4.4: Habilitar hosting de sitio web estático

1. Haz clic en tu bucket recién creado
2. Ve a la pestaña "Properties" (Propiedades)
3. Desplázate hacia abajo hasta encontrar "Static website hosting"
4. Haz clic en "Edit" (Editar)
5. Configura:
    - **Static website hosting**: Enable (Habilitar)
    - **Hosting type**: Host a static website
    - **Index document**: index.html
6. Haz clic en "Save changes"
7. **Importante**: Anota la "Bucket website endpoint" que aparece (será algo como: `http://lab06-sitio-web-tunombre.s3-website-us-east-1.amazonaws.com`)

### Paso 4.5: Configurar política del bucket para acceso público

Para que el sitio web funcione correctamente, necesitas configurar una política de bucket:

1. Ve a la pestaña "Permissions" (Permisos) de tu bucket
2. Busca la sección "Bucket policy" (Política del bucket)
3. Haz clic en "Edit" (Editar)
4. Pega la siguiente política JSON (reemplaza `TU-NOMBRE-DE-BUCKET` con el nombre real de tu bucket):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::TU-NOMBRE-DE-BUCKET/*"
        }
    ]
}
```

5. Haz clic en "Save changes"
6. Ahora te puedes dirigir a la URL del endpoint del sitio web que anotaste anteriormente para ver tu página web estática en acción.

### Paso 4.6: Limpieza de recursos

Para evitar cargos innecesarios, elimina el bucket S3 y su contenido cuando ya no lo necesites:

1. Ve a la consola de S3
2. Selecciona tu bucket
3. Haz clic en "Empty" (Vaciar bucket) primero
4. Confirma escribiendo "permanently delete" y haz clic en "Empty"
5. Una vez que el bucket esté vacío, selecciónalo nuevamente
6. Haz clic en "Delete" (Eliminar bucket)
7. Confirma escribiendo el nombre del bucket y haz clic en "Delete bucket"

**Nota**: Los buckets deben estar vacíos antes de poder eliminarlos. AWS no permite eliminar buckets que contengan objetos.

## Conclusión

En este laboratorio, has aprendido los conceptos básicos de AWS, incluyendo cómo crear y configurar una instancia EC2, instalar un servidor web Apache, y hospedar un sitio web estático utilizando S3. Estos son los fundamentos para trabajar con infraestructura en la nube y desplegar aplicaciones web. A medida que avances en tu aprendizaje, podrás explorar más servicios y funcionalidades que AWS ofrece para construir soluciones más complejas y escalables.
