```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02. Almacenamiento distribuido e integridad de datos
Apartado:                   6.- Acceso a Hadoop desde Python
Resultados de aprendizaje:  RA3
```

# UT02: ALMACENAMIENTO DISTRIBUIDO E INTEGRIDAD DE DATOS

## 6. Acceso a Hadoop desde Python

## 6.1. Arquitectura de accesos a HDFS (Hadoop Distributed File System)

El acceso a HDFS puede realizarse a través de múltiples enfoques según el caso de uso:

- **Interfaz de Línea de Comandos (CLI):** Hadoop FS Shell (`hdfs dfs -ls /user`, `hdfs dfs -put file.txt /input`).
- **Interfaz Web / REST API:** WebHDFS (REST API) y exploradores visuales de archivos como Hue.
- **Lenguajes de Programación (APIs):** 
  - Java / Scala (HDFS API nativa).
  - Python (`pyarrow`, `snakebite`, `hdfs`).
  - R (`hdfs`).
  - REST API (HTTP/JSON).
- **Servicios del Ecosistema Hadoop:**
  - Apache Spark (Lectura/Escritura RDD/DataFrame).
  - Apache Hive (Almacenamiento de Tablas).
  - MapReduce (Procesamiento masivo).
  - HBase (Base de datos NoSQL sobre HDFS).
- **Herramientas de Ingesta y ETL:**
  - Apache Sqoop (Bases de datos relacionales $\leftrightarrow$ HDFS).
  - Apache Flume (Logs y datos en tiempo real).
  - Apache NiFi (Flujos de datos automatizados).
  - Kafka Connect.
- **Montaje como Sistema de Archivos (FUSE):** NFS Gateway para montar HDFS directamente en el sistema operativo local y tratarlo como un directorio estándar con comandos de SO (`cp`, `mv`, `cat`).



## 6.2. Uso de HDFS desde Python

Para interactuar con HDFS desde scripts de Python (ejecutándose en el contenedor complementario de Jupyter conectado a la misma red `shared-network`), se cuenta principalmente con 3 librerías:

1. **`hdfs`**: Ideal para tareas administrativas ligeras, subida/bajada de archivos y exploración de ficheros. Es la seleccionada para este curso.
2. **`pyarrow`**: Opción actual de alto rendimiento, excelente para leer y escribir grandes volúmenes de datos directamente hacia Pandas o formatos columnares (`Parquet`, `Arrow`).
3. **`pyspark`**: Estándar de la industria para procesamiento masivo y distribuido leyendo directamente de HDFS.

En este curso utilizaremos la librería `hdfs`, por lo que primero deberemos instalarla.

```python
!pip install hdfs
```



## 6.3. Operaciones con la Librería `hdfs` 

### A. Conexión al Cliente (`InsecureClient`)

Se utiliza la resolución de nombres local de Docker (`http://namenode:9870`) especificando el usuario:

```python
from hdfs import InsecureClient

client = InsecureClient('http://namenode:9870', user='root')
print(client)
# Salida: <InsecureClient(url='http://namenode:9870')>
```
*(Nota: Si Hadoop estuviera ubicado en otro entorno externo como AWS, se debería indicar su dirección IP).*


### B. Listar contenido de un directorio (`list`)

**Listado básico de nombres:**

```python
ruta_hdfs = '/'
elems = client.list(ruta_hdfs)
print(f"Elementos en {ruta_hdfs}: {elems}")
# Salida: Elementos en /: ['salarios.csv', 'tmp']
```
**Listado detallado con metadatos (`status=True`):**  

Añadiendo este parámetro se obtiene un diccionario detallado con el tamaño, tipo, permisos, última modificación, ID de bloque, propietario, grupo, etc.

```python
elems = client.list(ruta_hdfs, status=True)
print(elems)
# Devuelve tuplas con el nombre del fichero/directorio y su diccionario de metadatos asociado.
```

### C. Crear directorios (`makedirs`)

Permite crear directorios de forma recursiva (análogo a `mkdir -p` en Linux):

```python
dir = '/user'
client.makedirs(dir)
print(f"Se ha creado el directorio {dir}")
print(f"Elementos en /: {client.list('/')}")
```
*Opcionalmente, acepta el parámetro `permission` para fijar permisos POSIX en formato octal (ej. `'755'`)*.

### D. Subir Archivos (`upload`)

Mueve archivos desde el sistema local hacia HDFS:

```python
archivo_local = 'datos.csv'
destino_hdfs = '/user'

client.upload(
    hdfs_path=destino_hdfs,
    local_path=archivo_local,
    overwrite=True,
    n_threads=4  # Número de hilos concurrentes para acelerar directorios con múltiples ficheros
)
```

### E. Descargar archivos (`download`)

Recupera recursos desde HDFS hacia el almacenamiento local:

```python
origen_hdfs = '/user'
destino_local = './descargado_datos.csv'

client.download(
    hdfs_path=origen_hdfs,
    local_path=destino_local,
    overwrite=True
)
```

### F. Lectura de archivos en Streaming (`read`)

Diseñado para leer ficheros grandes por flujos y evitar saturar la memoria RAM:

```python
with client.read('/user/datos.csv', encoding='utf-8') as reader:
    for linea in reader:
        print(linea.strip())
```
*Parámetros opcionales:* `encoding` para el sistema de codificación y `delimiter` para estructurar la lectura por líneas o bloques personalizados.

### G. Escritura de archivos en streaming (`write`)

Permite escribir o añadir contenido a ficheros de forma controlada:

```python
# Creación y escritura inicial (sobrescribiendo)
contenido = "id, nombre, valor\n1, ProductoA, 100\n2, ProductoB, 250"
with client.write('/user/salida.csv', encoding='utf-8', overwrite=True) as writer:
    writer.write(contenido)

# Adición de un nuevo registro a un fichero existente mediante append=True
nuevo_registro = "3, ProductoC, 400\n"
with client.write('/user/salida.csv', encoding='utf-8', append=True) as writer:
    writer.write(nuevo_registro)
```

### H. Eliminación de archivos y directorios (`delete`)

Permite borrar elementos de HDFS de manera individual o recursiva:

```python
# Eliminar un archivo específico
client.delete('/user/salida.csv')

# Eliminar un directorio completo de forma recursiva
client.delete('/user', recursive=True)

# Comprobación final
print(client.list('/'))
# Salida: ['salarios.csv', 'tmp']