4. Ingesta Serverless y Arquitecturas Event-Driven en AWS

    El paradigma Event-Driven Ingestion:

        Ingesta reactiva: Invocación ante eventos frente a la ejecución programada por cron/batches.

    Validación y enrutamiento con AWS Lambda:

        Triggers disparados por subida de ficheros a buckets S3 de entrada (S3 Event Notifications).

        Validación ligera previa a la ingesta (control de formatos, cabeceras, compresión en caliente).

    Ingesta de fuentes externas:

        Consumo de webhooks y APIs externas mediante integración con Amazon API Gateway y Lambda para aterrizar datos en S3.