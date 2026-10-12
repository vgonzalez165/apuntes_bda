1. Fundamentos y Patrones de Ingesta en Big Data

    El rol de la ingesta en el ciclo de vida del dato: De la fuente de origen (Data Sources) a la zona de aterrizaje cruda (Raw Zone / Landing Zone).

    Tipología de fuentes:

        Estructuradas: Bases de datos relacionales (RDBMS, OLTP).

        Semiestructuradas: Archivos JSON, XML, logs de aplicaciones, APIs REST, feeds IoT.

        No estructuradas: Audio, vídeo, imágenes, texto plano.

    Modelos temporales de ingesta:

        Ingesta por lotes (Batch): Ventanas de tiempo, periodicidad y grandes volúmenes.

        Ingesta en tiempo real (Streaming y Micro-batch): Latencia sub-segundo y flujo continuo.

    Patrones arquitectónicos de transporte:

        Push (la fuente empuja el dato al clúster) vs. Pull (el sistema de ingesta sondea y extrae).

    Garantías de entrega y robustez:

        Semánticas de entrega: At-most-once, At-least-once y Exactly-once.

        Conceptos de contrapresión (Backpressure) y desacoplamiento de productores y consumidores.