```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02. Almacenamiento distribuido e integridad de datos
Apartado:                   5.- Dockerizando Hadoop
Resultados de aprendizaje:  RA3
```

# UT02: ALMACENAMIENTO DISTRIBUIDO E INTEGRIDAD DE DATOS

## 5. Dockerizando Hadoop

### 5.1. Arquitectura

Para facilitar las prácticas del ecosistema Hadoop, se emplea un entorno contenerizado desplegado mediante **Docker Compose**. El fichero de configuración (`compose.yml`) levanta un clúster de **6 nodos** interconectados a través de una red de Docker denominada **`shared-network`**, lo que permite utilizar la resolución de nombres local de Docker.

El entorno `compose`, que puedes descargar del [repositorio de recursos Docker](https://vgonzalez165.github.io/docker_resources) del módulo, consta de los siguientes servicios:

1. **`namenode`**: nodo maestro del sistema de ficheros distribuido HDFS. 
2. **`yarnmanager`**: administrador de recursos de YARN (*Resource Manager*).
3. **`datanode1`, `datanode2`, `datanode3`, `datanode4`**: cuatro nodos esclavos encargados del almacenamiento físico distribuido de los bloques de datos. 


### 5.2. Puesta en marcha y monitorización

El primer paso es descargar el fichero `compose.yml` desde la ruta oficial de recursos: `https://vgonzalez165.github.io/docker_resources/compose/hadoop_cluster/`

Tras ello, sitúate en el directorio de descarga y ejecuta el despliegue en segundo plano con `docker compose up -d`.  Tras una breve espera mientras se descargan las imágenes, los 6 contenedores iniciarán su ejecución (`namenode`, `yarnmanager` y los 4 `datanodes`).

El entorno desplegado expondrá los siguientes puertos:

- **`9870`**: Interfaz web de HDFS (*NameNode* / Estado general, nodos y bloques, accesible en `http://localhost:9870/dfshealth.html`).
- **`8088`**: Interfaz web de YARN (*Resource Manager*).
- **`19888`**: Interfaz web de YARN (*JobHistory Server*).
- **`7777`**: Interfaz web de Jupyter Notebook (mapeado desde el puerto `8888` del contenedor).
- **`10000`**: Puerto de conexión de cliente para Apache Hive.
- **`10002`**: Puerto de conexión de cliente para HiveServer2 (orientado a conexiones JDBC/ODBC).



### 5.3. Línea de Comandos (CLI) y Gestión de Ficheros

#### Conexión a la terminal del NameNode

Para interactuar mediante la línea de comandos con HDFS, se conecta la terminal al contenedor del NameNode: `docker exec -ti namenode bash`

Una vez dentro, se pueden emplear comandos de la shell de Hadoop, por ejemplo, para listar la raíz:

```bash
root@namenode:~# hdfs dfs -ls /
Found 1 items
drwxrwx---   - root supergroup          0 2026-10-21 07:30 /tmp
```

#### Métodos para subir archivos al entorno

Dado que en esta instalación no existe un usuario `hadoop` dedicado y todas las operaciones se realizan como `root`, existen dos vías principales:

1. **Copiar desde la máquina física al contenedor** usando `docker cp`:
   ```powershell
   PS C:\> docker cp C:\Users\victor\Downloads\salarios.csv namenode:/root
   Successfully copied 2.05kB to namenode:/root
   ```
2. **Descargar directamente desde internet** usando `wget` dentro de la terminal del contenedor:
   ```bash
   root@namenode:~# wget https://vgonzalez165.github.io/apuntes_bda/apuntes/ut02/01-arquitectura-conceptos-hdfs.html
   ```
