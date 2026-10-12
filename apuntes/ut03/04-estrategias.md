4. Estrategias de Organización, Particionado y Buenas Prácticas

    Estructura y diseño de rutas en S3:

        Convención de particionado estilo Hive:

        s3://data-lake-empresa/silver/ventas/year=2026/month=10/day=12/

        Selección adecuada de las claves de particionado: Cardinalidad óptima para evitar dispersión excesiva.

    El problema de los ficheros pequeños (Small Files Problem):

        Impacto del exceso de ficheros de pocos KB: Saturación de metadatos, latencias de peticiones GET en S3 y sobrecarga en los motores de procesamiento distribuido.

        Técnicas de compactación y consolidación de particiones a tamaños recomendados (128 MB – 512 MB por fichero).

    Control de versiones (S3 Versioning) y prevención de pérdidas accidentales.