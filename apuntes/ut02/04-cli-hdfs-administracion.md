```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02. Almacenamiento distribuido e integridad de datos
Apartado:                   4.- Comandos básicos y CLI de HDFS
Resultados de aprendizaje:  RA3
```

# UT01: ALMACENAMIENTO DISTRIBUIDO E INTEGRIDAD DE DATOS

## 4. Comandos básicos y CLI de HDFS

La CLI de HDFS se invoca mediante el binario `hdfs` acompañado de un subcomando:

```bash
hdfs [OPTIONS] SUBCOMMAND [SUBCOMMAND OPTIONS]
```

Los subcomandos se dividen en tres áreas principales:

1. **Comandos de Cliente:** Para operar con el sistema de archivos (`dfs`, `lsSnapshot`, `snapshotDiff`, `getconf`).
2. **Comandos de Administración:** Para mantenimiento avanzado del clúster (`dfsadmin`, `fsck`, `haadmin`, `oiv`, `oev`, `crypto`, `ec`).
3. **Comandos de Demonio:** Para el control manual de los procesos individuales (`namenode`, `datanode`, `secondarynamenode`, `balancer`, `journalnode`, `dfsrouter`).

### 4.1 Operaciones sobre ficheros (`hdfs dfs`)

La sintaxis del subcomando `hdfs dfs` emula las órdenes POSIX de Linux:

| Comando                                           | Función  |
| ------------------------------------------------- | --------------------------------------------------------------- |
| `hdfs dfs -ls <ruta>`<br>                         | Lista los ficheros y directorios indicados. |
| `hdfs dfs -mkdir <ruta>`<br>                      | Crea un nuevo directorio en HDFS. |
| `hdfs dfs -put <origen_local> <destino_hdfs>`<br> | Sube un archivo desde el sistema de archivos local a HDFS. |
| `hdfs dfs -get <origen_hdfs> <destino_local>`<br> | Descarga un archivo de HDFS hacia el disco local. |
| `hdfs dfs -cat <archivo_hdfs>`<br>                | Imprime en pantalla el contenido de un archivo de texto. |
| `hdfs dfs -rm <archivo_hdfs>`<br>                 | Elimina un fichero del sistema HDFS. |
| `hdfs dfs -mv <origen> <destino>`<br>             | Mueve o renombra elementos dentro de HDFS. |
| `hdfs dfs -cp <origen> <destino>`<br>             | Copia ficheros entre rutas internas de HDFS. |
| `hdfs dfs -du <ruta>`<br>                         | Muestra el espacio en disco consumido por los directorios. |
| `hdfs dfs -chmod [-R] <modo> <ruta>`<br>          | Modifica los permisos de acceso. |
| `hdfs dfs -chown [-R] <usuario:grupo> <ruta>`<br> | Modifica el propietario o grupo asignado. |
| `hdfs dfs -test <path>`                           | Comprueba si un archivo o directorio existe |
| `hdfs dfs -setrep -w <nivel> <archivo>`<br>       | Ajusta el factor de replicación de un archivo en caliente. |

#### Correspondencia interna de bloques en el sistema físico

Al subir un fichero (p. ej., `hola.txt`), HDFS le asigna un **Block ID** (p. ej., `blk_1073741825`) y un **Block Pool ID**. En el disco local del DataNode (`/opt/hadoop/workspace/dfs/data/current/.../finalized/`), se observan dos archivos vinculados:

- `blk_<ID>`: Contiene los datos brutos del bloque.
- `blk_<ID>_<versión>.meta`: Contiene los metadatos del bloque y sus sumas de verificación (*checksums*).

Si se genera un archivo de gran tamaño (p. ej., 1 GB con `dd if=/dev/zero of=/tmp/big_file.dat bs=1024 count=1000000`) y se transfiere a HDFS con un tamaño de bloque predeterminado de 128 MB, el sistema lo fragmenta automáticamente en **8 bloques físicos** de 128 MB cada uno.



### 4.2 Tareas de administración con `hdfs dfsadmin`

El subcomando `hdfs dfsadmin` permite gestionar el clúster a bajo nivel:

| Comando | Utilidad |
| --- | --- |
| `hdfs dfsadmin -report`                               | Genera un informe exhaustivo de capacidad, nodos vivos, bloques corruptos o sub-replicados. |
| `hdfs dfsadmin -metasave <fichero>`                   | Vuelca el estado actual de la memoria del NameNode a un archivo de texto. |
| `hdfs dfsadmin -listCorruptFileBlocks <ruta>`         | Enumera bloques corruptos en una ruta determinada. |
| `hdfs dfsadmin -refreshNodes`                         | Recarga `dfs.hosts` y `dfs.hosts.exclude` para admitir o retirar nodos sin detener el clúster. |
| `hdfs dfsadmin -safemode get\|enter\|leave\|wait`     | Consulta, fuerza o levanta el **Modo Seguro** (*Safe Mode*). |
| `hdfs dfsadmin -balancer`                             | Ejecuta la utilidad de rebalanceo de carga entre discos del clúster. |
| `hdfs dfsadmin -printTopology`                        | Muestra el mapa de topología de red y adscripción a racks de cada máquina. |


#### Modo Seguro (*Safe Mode*)

Estado de solo lectura en el que el NameNode no admite operaciones de escritura o modificación sobre el sistema de archivos. El NameNode entra en modo seguro de manera automática durante el arranque (mientras espera los *block reports* iniciales de los DataNodes para validar la disponibilidad de réplicas).

Debe activarse manualmente ante las siguientes situaciones:

- Tareas de mantenimiento preventivo o migraciones de datos críticas.
- Diagnóstico y subsanación de bloques corruptos o pérdidas masivas de nodos.
- Procesos de balanceo o reconfiguraciones de topología.
- Recuperación de metadatos tras caídas inesperadas del clúster.




### 4.3 Snapshots (Instantáneas)

Un snapshot es una copia de solo lectura en un instante determinado de un directorio y sus contenidos.

- **Propiedades:** Su creación es casi instantánea (no implica duplicación física de bloques de datos) y no introduce sobrecoste de almacenamiento inicial, ya que únicamente registra los deltas (*modificaciones posteriores*).
- **Gestión de Snapshots:**
    1. Habilitar el directorio como apto para snapshots: `hdfs dfsadmin -allowSnapshot <directorio>`.
    2. Crear el snapshot: `hdfs dfs -createSnapshot <directorio> <nombre_snapshot>`.
    3. Consultar: Las instantáneas residen en la ruta virtual `<directorio>/.snapshot/`.
    4. Restauración: Al ser de solo lectura, la recuperación se realiza copiando los datos con `hdfs dfs -cp <directorio>/.snapshot/<nombre_snapshot>/... <destino>`.
    5. Desactivar / Eliminar: `hdfs dfs -deleteSnapshot <dir> <nombre>` y `hdfs dfsadmin -disallowSnapshot <dir>`.




### 4.4 Cuotas de almacenamiento

HDFS permite definir límites de consumo sobre directorios para prevenir el agotamiento de recursos:

- **Cuota de Nombres (*Name Quota*):** Limita el número máximo de archivos y subdirectorios permitidos dentro de una carpeta. Se fija mediante `hdfs dfsadmin -setQuota <N> <directorio>`.
- **Cuota de Espacio (*Space Quota*):** Limita la cantidad máxima de bytes físicos almacenables en el árbol del directorio. Se asigna con `hdfs dfsadmin -setSpaceQuota <tamaño_bytes> <directorio>`.

El estado de ocupación frente a los límites de cuota se verifica con la orden `hdfs dfs -count -q -h <directorio>`.



---

[Volver al índice](ut01_index.md)