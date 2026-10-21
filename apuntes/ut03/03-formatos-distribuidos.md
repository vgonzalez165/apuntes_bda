
3. Formatos de Ficheros Distribuidos y Compresión

La elección del formato de almacenamiento en el Data Lake determina directamente el rendimiento de las consultas y la factura en la nube, marcando la diferencia entre realizar lecturas completas de disco o escanear solo las columnas estrictamente necesarias.



Formatos de texto plano (CSV y JSON):

    Ventajas de portabilidad e interoperabilidad humana.

    Ineficiencias en Big Data: Falta de esquema estricto, sobrecarga de análisis sintáctico (parsing overhead), ausencia de estadísticas embebidas y alto consumo de I/O.

Formatos binarios orientados a filas: Apache Avro:

    Definición y serialización mediante esquemas JSON embebidos.

    Idoneidad para procesos de escritura secuencial rápida (ingesta, buffers de streaming, eventos Kafka).

    Soporte de evolución de esquema (Schema Evolution): compatibilidad hacia adelante y hacia atrás.

Formatos binarios orientados a columnas: Apache Parquet y Apache ORC:

    Estructura física de Parquet: File Header, Row Groups, Column Chunks, Pages y File Footer (metadatos al final del archivo).

    Poda de columnas (Column Pruning): Lectura exclusiva de los atributos requeridos por la consulta analítica, evitando transferencias de I/O innecesarias.

    Pulsado de predicados (Predicate Pushdown): Uso de valores mínimos y máximos en el pie de página para omitir bloques enteros sin leerlos de disco.

    Codificación columnar eficiente: Dictionary Encoding, Run-Length Encoding (RLE) y Bit Packing.

Algoritmos de compresión en entornos distribuidos:

    Criterios de selección: Ratio de compresión vs. velocidad de descompresión en CPU.

    Comparativa práctica: GZIP (alta compresión, intensivo en CPU) vs. Snappy (compresión balanceada, descompresión ultrarrápida, estándar con Parquet) vs. Zstandard (ZSTD).

    Propiedad de división (Splittability): Capacidad de procesar fragmentos comprimidos en paralelo sin descomprimir el fichero completo.