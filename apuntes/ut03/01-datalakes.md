1. Evolución del Almacenamiento Analítico: De los Data Warehouses al Data Lake

    Paradigmas analíticos:

        Almacenamiento relacional transaccional (OLTP) frente a almacenamiento analítico masivo (OLAP).

        Filosofías de modelado: Esquema en escritura (Schema-on-Write) en Data Warehouses vs. Esquema en lectura (Schema-on-Read) en Data Lakes.

    Arquitectura lógica del Data Lake moderno (Patrón Medallion):

        Capa Bronze (Landing / Raw): Ingesta cruda inmutable, preservación de datos fuente sin procesar.

        Capa Silver (Cleansed / Conformed): Limpieza, validación de esquemas, deduplicación y tipado.

        Capa Gold (Curated / Aggregated): Tablas agregadas listas para BI, analítica y consumo de modelos de Machine Learning.

    Concepto de Lakehouse: Unificación de transacciones ACID y gobierno sobre el almacenamiento de objetos del Data Lake.