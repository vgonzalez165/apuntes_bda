```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02. Almacenamiento distribuido e integridad de datos
Apartado:                   1.- Arquitectura y conceptos fundamentales de Hadoop HDFS
Resultados de aprendizaje:  RA3
```

# UT01: ALMACENAMIENTO DISTRIBUIDO E INTEGRIDAD DE DATOS

## 1. Arquitectura y conceptos fundamentales de Hadoop HDFS

Hadoop Distributed File System (HDFS) es el sistema de archivos distribuido del ecosistema Apache Hadoop, diseñado para ejecutarse sobre clústeres de hardware convencional (*commodity hardware*) y gestionar grandes volúmenes de datos mediante una arquitectura tolerante a fallos.

### 1.1 Características principales

- **Tolerancia a fallos:** El sistema incorpora mecanismos tanto correctivos como preventivos. Los archivos se dividen en fragmentos de tamaño fijo llamados **bloques** (habitualmente de 64 MB o 128 MB). Cada bloque se replica de manera distribuida a lo largo del clúster con un **factor de replicación** (cuyo valor por defecto suele ser 3), garantizando que la caída o fallo de una máquina física no comprometa la disponibilidad del dato, ya que siempre existirán copias adicionales.
- **Acceso a datos en flujo (*Streaming Data Access*):** HDFS se fundamenta en el principio de «*escribe una vez, lee muchas*» (*Write Once, Read Many* o WORM). Los datos se sirven al cliente en forma de flujo continuo (*streaming*), lo que evita la necesidad de esperar a la lectura completa del archivo en disco para comenzar su procesamiento.
- **Escalabilidad horizontal:** Permite incorporar nuevos nodos de almacenamiento al vuelo sin detener el servicio. Los clústeres reales abarcan desde decenas hasta miles de nodos. Como referencia de escala, la infraestructura de Yahoo (origen del proyecto Hadoop) alcanzó clústeres de 42.000 nodos y más de 600 PB de almacenamiento agregado.
- **Simplicidad operativa:** Desarrollado sobre la plataforma Java, proporciona una interfaz de línea de comandos (CLI) con una sintaxis prácticamente homóloga a la de los entornos Linux POSIX.
- **Alta disponibilidad:** Aunque las peticiones se centralizan a través de un nodo maestro que históricamente constituía un punto único de fallo (*Single Point of Failure* o SPOF), HDFS provee esquemas de respaldo (nodos secundarios o instancias *Standby* en clústeres HA) para asumir el control o consolidar el estado del sistema en caso de contingencia.



### 1.2 Componentes de la arquitectura HDFS

HDFS implementa un modelo **Maestro-Esclavo** compuesto por cuatro elementos clave:

#### NameNode (Maestro)

Es el servidor principal encargado de gestionar el espacio de nombres (*namespace*) del sistema de archivos. Mantiene en memoria RAM la jerarquía de directorios, permisos, propietarios, identificadores de bloque (Block ID) y la ubicación física de cada bloque en los distintos DataNodes.

El NameNode **no almacena los datos de los archivos**, únicamente sus metadatos. Para garantizar la persistencia de estos metadatos frente a reinicios, recurre a dos estructuras de almacenamiento local:

- **`fsimage`:** Instantánea binaria y completa del estado del sistema de archivos en un instante de tiempo determinado. Se carga en la memoria RAM del NameNode durante la inicialización.
- **`editlogs`:** Registro transaccional (*journal*) de todas las modificaciones (creaciones, borrados, cambios de permisos) ocurridas en el sistema desde la generación de la última `fsimage`. Si el NameNode reinicia, reproduce secuencialmente los `editlogs` sobre la `fsimage` para reconstruir el estado íntegro.


#### DataNodes (Esclavos)

Son los nodos encargados del almacenamiento físico de los bloques de datos en sus propios sistemas de ficheros locales del sistema operativo. Responden a las instrucciones del NameNode para crear, suprimir o replicar bloques. De manera periódica, remiten al NameNode dos tipos de señales:

- **Señales de vida (*Heartbeats*):** Notifican que el nodo sigue operativo. Si el NameNode deja de recibir estas señales, da al nodo por caído y redistribuye sus bloques.
- **Informes de bloques (*Block Reports*):** Relación exhaustiva de los bloques físicos custodiados en el disco local.


#### Secondary NameNode

No actúa como un nodo maestro redundante ni sustituto directo ante caídas (*no es un NameNode en caliente*). Su función es realizar de forma periódica el proceso de **punto de control (*Checkpointing*)**:

1. Descarga del NameNode la `fsimage` actual y los `editlogs` acumulados.
2. Fusiona ambos ficheros en su propia memoria para generar una nueva `fsimage` limpia y consolidada.
3. Transfiere la nueva `fsimage` de regreso al NameNode y vacía los `editlogs` procesados.

Este mecanismo previene que el fichero `editlog` crezca indefinidamente, reduciendo de manera sustancial los tiempos de recuperación y arranque del NameNode. En topologías con Alta Disponibilidad (HA), esta labor es asumida por un **Standby NameNode**.

#### Cliente HDFS

Capa de interfaz utilizada por los usuarios y aplicaciones (mediante APIs de Java, Python o utilidades CLI) para interactuar con el clúster:

- **Lectura:** El cliente solicita al NameNode la localización de los bloques del archivo. Con las direcciones IP obtenidas, el cliente se conecta directamente a los DataNodes correspondientes para transferir los datos en flujo continuo.
- **Escritura:** El NameNode asigna los DataNodes de destino para los nuevos bloques. El cliente envía el flujo de datos al primer DataNode de la cadena, el cual se encarga de replicarlo sucesivamente en canalización (*pipeline*) hacia los siguientes DataNodes.




### 1.3 Bloques y políticas de replicación

#### Tamaño de bloque (*Block Size*)

Representa la unidad atómica mínima de lectura y escritura en HDFS (generalmente configurada a 64 MB o 128 MB). La elección del tamaño de bloque tiene un impacto directo sobre la arquitectura:

| Tamaño de bloque | Ventajas | Inconvenientes |
| ---------------- | -------- | -------------- |
| **Pequeño (< 64 MB)**<br> | Minimiza el espacio desaprovechado en archivos pequeños y ofrece mayor paralelismo en conjuntos de datos reducidos. | Sobrecarga de metadatos en la memoria RAM del NameNode y fragmentación en ficheros grandes. |
| **Mediano (64 – 128 MB)**<br> | Equilibrio óptimo para la mayoría de cargas de trabajo estándar en Big Data.| — |
| **Grande (> 512 MB)**<br> | Reduce significativamente el volumen de metadatos en el NameNode y maximiza el rendimiento secuencial en grandes archivos. | Pérdida de paralelismo en tareas de cómputo y riesgo de desaprovechamiento de espacio interno.|

#### Conciencia de Topología (*Rack Awareness*)

HDFS implementa políticas de distribución conscientes de la ubicación física de los servidores (*Rack Awareness*). La comunicación dentro de un mismo rack es más rápida y de menor latencia que entre racks diferentes. Por ello, para un factor de replicación de 3, HDFS distribuye los bloques equilibrando rendimiento y disponibilidad: almacena una réplica en el nodo local, la segunda en otro nodo del mismo rack, y la tercera en un rack físicamente independiente, protegiendo los datos frente al fallo de un conmutador (*switch*) de rack completo.



---

[Volver al índice](ut01_index.md)

