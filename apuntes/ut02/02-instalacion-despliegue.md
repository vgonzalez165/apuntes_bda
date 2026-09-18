```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02. Almacenamiento distribuido e integridad de datos
Apartado:                   2.- Instalación de Hadoop y HDFS
Resultados de aprendizaje:  RA3
```

# UT01: ALMACENAMIENTO DISTRIBUIDO E INTEGRIDAD DE DATOS

## 2. Instalación de Hadoop y HDFS

### 2.1 Modos de ejecución de Hadoop

Hadoop puede desplegarse en tres modos operativos:

| Característica     | Modo Independiente (*Standalone*) | Modo Pseudo-distribuido | Modo Totalmente Distribuido |
| ------------------ | --------------------------------- | ----------------------- | --------------------------- |
| **Nº de máquinas** | 1 | 1 | Múltiples (2 o más) |
| **HDFS activo**    | No (emplea el sistema de ficheros local) | Sí | Sí |
| **YARN activo** | No | Sí | Sí |
| **Demonios / Procesos** | Único proceso Java en la misma JVM | Procesos Java independientes en una misma máquina | Procesos separados distribuidos entre nodos maestros y *workers*<br> |
| **Uso de red** | No | Sí (`localhost`) | Sí (red entre nodos reales) |
| **Propósito** | Pruebas simples y depuración local | Desarrollo y aprendizaje con simulación de clúster | Producción y alto rendimiento |
| **Escalabilidad** | Nula | Limitada | Alta |
| **Tolerancia a fallos** | No | No (un único punto de fallo físico) | Sí (tolerante mediante replicación) |
| **Configuración** | Mínima | Moderada | Compleja |


### 2.2 Preparación del entorno e instalación en Modo Pseudo-distribuido

Para este laboratorio se parte de una máquina virtual (p. ej., Ubuntu Server 22.04 LTS) con dos adaptadores de red: uno con acceso a Internet (*Default Switch*) y otro asignado a la red interna que interconecta las máquinas.

#### Paso 1: Creación de usuario y directorio base

Se crea un usuario de sistema dedicado `hadoop` y se le otorgan privilegios administrativos:

```bash
sudo adduser hadoop
sudo usermod -a -G sudo hadoop
```

Se crea el directorio `/opt/hadoop` para la instalación del framework y se asigna su propiedad:

```bash
cd /opt
sudo mkdir hadoop
sudo chown hadoop /opt/hadoop
su hadoop

```

#### Paso 2: Descarga e integridad del paquete binario

Desde el portal oficial de Apache Hadoop se descargan tanto el tarball binario (p. ej., Hadoop 3.4.1) como su firma criptográfica SHA-512. La verificación de sumas de verificación es indispensable para asegurar que el archivo no ha sufrido alteraciones ni corrupción en tránsito:

```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz.sha512
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz
shasum -a 512 -c hadoop-3.4.1.tar.gz.sha512

```

Si la salida devuelve `hadoop-3.4.1.tar.gz: OK`, la integridad queda validada.


#### Paso 3: Descompresión de ficheros

Se desempaquetan los binarios directamente dentro de `/opt/hadoop` utilizando `--strip-components=1` para evitar la creación de subcarpetas redundantes:

```bash
tar xvf hadoop-3.4.1.tar.gz --strip-components=1 -C /opt/hadoop

```

La estructura de directorios resultante incluye:

- `bin/`: Contiene los ejecutables principales (`hadoop`, `hdfs`, `yarn`, `mapred`).
- `sbin/`: Contiene los scripts de inicio y parada de servicios (`start-dfs.sh`, `stop-dfs.sh`, `start-yarn.sh`, etc.).
- `etc/hadoop/`: Archivos de configuración XML y scripts de entorno.
- `share/`: Bibliotecas Java (.jar) y ejemplos de MapReduce.
- `lib/`, `libexec/`, `include/`: Librerías y componentes nativos del sistema.


#### Paso 4: Instalación de Java JDK

Hadoop soporta oficialmente las versiones de Java 8 y 11. Debido al cambio en los términos de licenciamiento de Oracle Java a partir de la versión 8 (restringida para uso comercial no de pago), se opta por **OpenJDK** (versión 8).

El paquete se descomprime en `/opt/hadoop/jdk` para uso exclusivo del usuario `hadoop`:

```bash
wget https://download.java.net/openjdk/jdk8u44/ri/openjdk-8u44-linux-x64.tar.gz
tar xvf openjdk-8u44-linux-x64.tar.gz -C /opt/hadoop/
mv /opt/hadoop/java-se-8u44-ri /opt/hadoop/jdk

```

#### Paso 5: Configuración de variables de entorno

Se añaden las rutas globales en el archivo `~/.bashrc` del usuario `hadoop`:

```bash
## HADOOP & JAVA CONFIGURATION
export HADOOP_HOME=/opt/hadoop
export JAVA_HOME=/opt/hadoop/jdk
export HADOOP_MAPRED_HOME=${HADOOP_HOME}
export HADOOP_COMMON_HOME=${HADOOP_HOME}
export HADOOP_HDFS_HOME=${HADOOP_HOME}
export YARN_HOME=${HADOOP_HOME}
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$JAVA_HOME/bin
```

Se recarga el entorno con `source ~/.bashrc` y se comprueba la disponibilidad ejecutando `hadoop version` y `java -version`. Adicionalmente, se declara explícitamente `JAVA_HOME`:

```bash
export JAVA_HOME=/opt/hadoop/jdk

```

#### Paso 6: Prueba de funcionamiento inicial (MapReduce local)

Para validar la instalación sin levantar aún demonios distribuidos, se ejecuta el ejemplo `grep` sobre un archivo de texto descargado en local (p. ej., `quijote.txt`). Este ejemplo lanza un proceso MapReduce que recoge un conjunto de ficheros, se le pasa una expresión regular con el texto a buscar y devuelve el número de ocurrencias de la expresión regular entre todos los datos procesados

```bash
hadoop jar /opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.4.1.jar grep /tmp/quijote.txt /tmp/salida '[Ss]ancho'
```

La carpeta `/tmp/salida` generará el fichero testigo `_SUCCESS` y el fichero de resultados `part-r-00000` con el recuento de coincidencias.


#### Paso 7: Configuración de SSH sin contraseña e IPv6

Los demonios maestros y trabajadores de Hadoop se comunican a través de túneles SSH. Para permitir el acceso automatizado sin solicitud interactiva de clave, se genera un par de claves RSA y se añade la clave pública al fichero `authorized_keys` propio:

```bash
ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
cp ~/.ssh/id_rsa.pub ~/.ssh/authorized_keys
chmod go-w $HOME $HOME/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Hadoop no admite el uso de IPv6 para la gestión interna de clústeres. Se desactiva añadiendo las siguientes directivas en `/etc/sysctl.conf` y reiniciando el equipo:

```ini
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```



---

[Volver al índice](ut01_index.md)