```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02. Almacenamiento distribuido e integridad de datos
Apartado:                   3.- Administración y puesta en marcha de HDFS
Resultados de aprendizaje:  RA3
```

# UT01: ALMACENAMIENTO DISTRIBUIDO E INTEGRIDAD DE DATOS

## 3. Administración y puesta en marcha de HDFS

### 3.1 Configuración de ficheros XML

Para poner en marcha el modo pseudo-distribuido se configuran dos ficheros esenciales dentro de `/opt/hadoop/etc/hadoop/`:

- **`core-site.xml`:** Define el punto de enlace central del sistema de ficheros HDFS mediante el protocolo RPC.
 
    ```xml
    <configuration>
        <property>
            <name>fs.default.name</name>
            <value>hdfs://localhost:8020</value>
            <description>Nombre del filesystem por defecto</description>
        </property>
    </configuration>
    ```

- **`hdfs-site.xml`:** Establece las rutas locales en el host para persistir metadatos y bloques, así como el factor de replicación (fijado en 1 al existir una única máquina virtual).

    ```xml
    <configuration>
        <property>
            <name>dfs.namenode.name.dir</name>
            <value>file:/opt/hadoop/workspace/dfs/name</value>
            <description>Ruta almacenamiento metadatos</description>
        </property>
        <property>
            <name>dfs.datanode.data.dir</name>
            <value>file:/opt/hadoop/workspace/dfs/data</value>
            <description>Ruta almacenamiento de los bloques</description>
        </property>
        <property>
            <name>dfs.replication</name>
            <value>1</value>
            <description>Factor de replicación.</description>
        </property>
    </configuration>
    ```

Se crean las carpetas locales definidas:

```bash
sudo mkdir -p /opt/hadoop/workspace/dfs/name
sudo mkdir -p /opt/hadoop/workspace/dfs/data
sudo chown -R hadoop:hadoop /opt/hadoop/workspace
```


### 3.2 Formateo e inicio del servicio

Antes de arrancar HDFS por primera vez, es obligatorio formatear el espacio de nombres del NameNode:

```bash
hdfs namenode -format
```

Este comando crea la estructura de metadatos inicial (`fsimage_000...`, `seen_txid`, `VERSION`) dentro del directorio configurado `dfs/name/current`.

A continuación, se inicia el sistema distribuido ejecutando el script oficial:

```bash
start-dfs.sh
```

Para verificar que los tres procesos Java se han lanzado satisfactoriamente, se utiliza el comando `jps`:

- `NameNode`
- `DataNode`
- `SecondaryNameNode`




### 3.3 Interfaz Web de HDFS (Puerto 9870)

HDFS expone una consola web de monitorización en `http://<IP_SERVIDOR>:9870` con las siguientes secciones de diagnóstico:

- **Overview:** Estado global del clúster (versión activa, identificador de clúster *Cluster ID*, *Block Pool ID*, capacidad configurada, espacio HDFS en uso y disponible, nodos vivos/muertos y políticas activas de *Erasure Coding*).
- **Datanodes:** Detalle de uso de disco de cada DataNode, dirección HTTP, capacidad total, bloques asignados y fecha del último *heartbeat* y *block report*.
- **Datanode Volume Failures:** Registro de anomalías o fallos en los discos locales de los DataNodes.
- **Snapshots:** Resumen de directorios habilitados y recuento de instantáneas creadas.
- **Startup Progress:** Monitoreo del proceso de arranque por fases (carga de `fsimage`, procesado de `edits`, guardado de *checkpoint* y evaluación de *Safe Mode*).
- **Browse the file system:** Explorador gráfico para navegar, visualizar y descargar archivos almacenados en HDFS.
- **Logs / Metrics / Configuration:** Inspección directa de ficheros de registro, métricas en formato JSON de la JVM y configuración XML activa resultante.
- **Network topology:** Mapa de la distribución de nodos según sus racks lógicos.




---

[Volver al índice](ut01_index.md)