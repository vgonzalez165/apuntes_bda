2. Ingesta Estructurada On-Premise: Apache Sqoop

    Arquitectura de Sqoop: Cómo utiliza el motor distribuido (tareas Map-only) para paralelizar transferencias JDBC.

    Estrategias de importación a HDFS y Hive:

        Importación completa de tablas relacionales.

        Paralelismo y división de carga: Configuración del número de mappers (-m) y selección de la clave de particionado (Split-by).

    Estrategias de ingesta incremental:

        Modos --append (basado en IDs autoincrementales) y --lastmodified (basado en marcas de tiempo/timestamps).

    Exportación inversa: Devolución de datos analizados desde HDFS hacia almacenes transaccionales relacionales.