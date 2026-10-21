6. Ingesta en Streaming a Gran Escala: Amazon Kinesis

    El ecosistema de mensajería masiva en AWS:

        Diferencias de arquitectura frente a colas tradicionales (SQS) y publicación/suscripción (SNS).

    Amazon Kinesis Data Streams:

        Concepto de Shards (capacidad de ingesta de 1 MB/s o 1000 registros/s por partición).

        Productores (KPL, AWS SDK) y Consumidores.

        Retención de datos y reproducción de flujos (replayability).

    Amazon Kinesis Data Firehose (Carga gestionada Zero-Code):

        Ingesta directa y sin servidor hacia Amazon S3, Amazon OpenSearch y Amazon Redshift.

        Capacidades nativas en tránsito: Conversión de formatos (JSON a Parquet en vuelo), compresión y particionado dinámico por timestamp.