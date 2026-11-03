```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT03. ALMACENAMIENTO DISTRIBUIDO EN CLOUD CON S3. DATA LAKES
Apartado:                   3.- Acceso a S3 desde Python
Resultados de aprendizaje:  RA1
```

# UT03. ALMACENAMIENTO DISTRIBUIDO EN CLOUD CON S3. DATA LAKES

## 3. Manipulando AWS S3 desde Python

### 3.1. Acceso a S3 utilizando Python (`boto3`)

**Instalación de la librería**
Para acceder a Amazon AWS desde Python se utiliza **Boto3**, el SDK oficial de AWS para Python. Se instala mediante `pip`:

```bash
pip install boto3
# O desde una celda de Jupyter Notebook:
%pip install boto3

```

**Configuración de credenciales temporales**
Antes de ejecutar código, es necesario descargar las credenciales de AWS al equipo local. En el entorno **AWS Learner Lab**, las credenciales son temporales (tienen una duración de entre 3 y 4 horas), por lo que este procedimiento debe repetirse cada día de trabajo:

1. En la interfaz del Learner Lab, pulsar en **AWS Details** y hacer clic en el botón **Show** junto a `AWS CLI`.


2. Copiar el bloque de texto completo que incluye `[default]`, `aws_access_key_id`, `aws_secret_access_key` y `aws_session_token`.


3. Guardar dicho contenido en el archivo de credenciales correspondiente según el sistema operativo:


* **Windows:** `C:\Usuarios\{username}\.aws\credentials`


* **Linux:** `/home/{username}/.aws/credentials`


* **Contenedor Docker (ej. Jupyter):** si se ejecuta Jupyter dentro de un contenedor, es necesario acceder por terminal al contenedor (`docker exec -ti <id_contenedor> bash`) y editar el fichero `/home/jovyan/.aws/credentials` (o `~/work/../.aws/credentials`) con un editor como `nano`.





**Comprobación de la conexión**
Para verificar que las credenciales funcionan correctamente:

```python
import boto3

try:
    s3 = boto3.client('s3')
    buckets = s3.list_buckets()
    print("¡Conexión exitosa!")
    print(f"Tienes {len(buckets['Buckets'])} buckets en tu cuenta.")
except Exception as e:
    print("Error de conexión. Revisa tus credenciales.")
    print(e)

```

**Funciones de conexión e identificación**

* `boto3.client('s3')`: crea un objeto cliente de bajo nivel. Es la forma más rápida y estándar de acceder a todas las funciones de la API.


* `boto3.resource('s3')`: crea una interfaz orientada a objetos; resulta más intuitiva, pero dispone de menos opciones.



**Funciones para la gestión de Buckets**

* `list_buckets()`: devuelve un diccionario que contiene la lista con todos los buckets existentes en la cuenta.


* `create_bucket(Bucket='name')`: crea un nuevo bucket con el nombre especificado.


* `delete_bucket(Bucket='name')`: borra un bucket (el bucket debe estar previamente vacío).



```python
import boto3

s3 = boto3.client('s3')
respuesta = s3.list_buckets()

# Recorremos la lista de diccionarios que nos da AWS
for bucket in respuesta['Buckets']:
    nombre = bucket['Name']
    print(f"Nombre del bucket: {nombre}")

s3.create_bucket(Bucket='mi-nombre-de-bucket')
s3.delete_bucket(Bucket='mi-nombre-de-bucket')

```

**Funciones para la gestión de Objetos (archivos locales y remotos)**

* `list_objects_v2(Bucket=bucket_name, Prefix='carpeta/')`: lista los archivos de un bucket que coinciden con un determinado prefijo.


* `upload_file(ruta_local, bucket, ruta_s3)`: sube un archivo desde el disco duro local hacia S3.


* `download_file(bucket, ruta_s3, ruta_local)`: descarga un archivo desde el bucket de S3 al sistema de archivos local.


* `delete_object(Bucket=bucket_name, Key=ruta_s3)`: borra un objeto específico dentro del bucket.



```python
# Listar objetos con un prefijo
res = s3.list_objects_v2(Bucket='mi-bucket', Prefix='fotos/')
for obj in res.get('Contents', []):
    print(obj['Key'])

# Subir un archivo: upload_file(origen_local, bucket, destino_s3)
s3.upload_file('documento.pdf', 'mi-bucket', 'carpetita/documento.pdf')
print("Archivo subido con éxito")

# Descargar un archivo: download_file(bucket, origen_s3, destino_local)
s3.download_file('mi-bucket', 'carpetita/documento.pdf', 'descargado.pdf')
print("Archivo descargado con éxito")

# Borrar un objeto: delete_object(Bucket, Key)
s3.delete_object(Bucket='mi-bucket', Key='carpetita/documento.pdf')
print("Archivo borrado")

```

**Funciones para la manipulación de datos en memoria**
Permiten leer o escribir datos directamente desde variables en memoria sin necesidad de que exista un archivo físico previo en el disco local:

* `get_object(Bucket=bucket_name, Key=file_name)`: obtiene el contenido del archivo y devuelve un diccionario cuyo campo más importante es `Body`, el cual se puede leer mediante `.read()`.


* `put_object(Bucket=bucket_name, Key=file_name, Body=datos)`: sube datos directamente desde una variable o memoria hacia S3.



```python
# Lectura en memoria con get_object
respuesta = s3.get_object(Bucket='mi-bucket', Key='datos.txt')
# Extraemos el Body, lo leemos y convertimos los bytes en texto legible con decode('utf-8')
contenido = respuesta['Body'].read().decode('utf-8')
print(f"El archivo dice: {contenido}")

# Escritura desde memoria con put_object
datos_a_subir = "Este es un texto generado en el código."
s3.put_object(Bucket='mi-bucket', Key='nota_rapida.txt', Body=datos_a_subir)
print("¡Datos subidos directamente a S3!")

```