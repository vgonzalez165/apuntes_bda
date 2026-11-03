```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT03. ALMACENAMIENTO DISTRIBUIDO EN CLOUD CON S3. DATA LAKES
Apartado:                   2.- Diseño de Data Lakes
Resultados de aprendizaje:  RA1
```

# UT03. ALMACENAMIENTO DISTRIBUIDO EN CLOUD CON S3. DATA LAKES

## 2. Diseño de Data Lakes

### 2.1. Arquitectura de capas en el Data Lake (Modelo Medallón)

En un proyecto de Big Data los datos no se guardan una única vez, sino que se transforman y persisten en diferentes capas de calidad bajo el estándar conocido como **arquitectura en medallón**. La gobernanza, calidad y seguridad de los datos actúan de forma transversal a lo largo de todo el ciclo (desde las fuentes de datos como bases de datos, APIs, archivos JSON/CSV o sensores IoT hasta el consumo en BI, Ciencia de Datos/IA y aplicaciones).

- **Capa Bronce (Datos en Bruto / Raw):**
  - Es el punto de aterrizaje inicial donde llegan los datos tras el proceso de ingesta.
  - Contiene datos sin procesar y en su formato original (JSON, CSV, etc.).
  - Su propósito es mantener un histórico completo e inmutable de toda la información recibida, permitiendo regresar siempre a este punto sin necesidad de volver a consultar las fuentes originales.
- **Capa Plata (Datos Limpios / Silver):**
  - Se alimenta de la capa bronce aplicando procesos de limpieza y transformación.
  - Contiene datos validados sobre los que se han aplicado reglas de filtrado, eliminación de duplicados (deduplicación), estandarización de formatos, tratamiento de nulos y enriquecimiento/cruces.
  - Su propósito es actuar como fuente única de verdad a nivel técnico y corporativo, ofreciendo tablas estructuradas y confiables.
- **Capa Oro (Datos de Negocio / Gold):**
  - Es la etapa final de refinamiento, generada mediante procesos de agregación y lógica de negocio a partir de la capa plata.
  - Contiene datos agregados, resumidos y estructurados específicamente para responder a métricas (KPIs) y casos de uso de negocio.
  - Su propósito es ofrecer un rendimiento optimizado para consultas rápidas y directas por parte de usuarios finales y herramientas analíticas.

**Resumen comparativo de las capas de la arquitectura en medallón**

| Característica          | Capa Bronce (Raw)                                    | Capa Plata (Silver)                                  | Capa Oro (Gold)                                             |
| ----------------------- | ---------------------------------------------------- | ---------------------------------------------------- | ----------------------------------------------------------- |
| **Foco principal**      | Captura e histórico                                  | Calidad y consistencia                               | Valor de negocio y rendimiento                              |
| **Transformaciones**    | Ninguna (solo metadatos de carga)                    | Filtrado, limpieza, deduplicación y cruces           | Agregaciones, cálculos de KPIs y reglas de negocio          |
| **Estructura**          | Semi-estructurada, no estructurada o copia de origen | Estructurada (tablas normalizadas o desnormalizadas) | Muy estructurada (modelos dimensionales y tablas agregadas) |
| **Usuarios habituales** | Ingenieros de datos                                  | Ingenieros de datos y Científicos de datos           | Analistas de negocio (BI), ejecutivos y sistemas finales    |



### 2.2. Estrategias de particionado

Al desplegar una arquitectura de medallón en AWS, la primera decisión arquitectónica consiste en elegir cómo separar las capas en S3:

- **Enfoque Multi-Bucket:** cada capa tiene su propio bucket independiente; es la opción recomendada para entornos de producción y empresas.
- **Enfoque Single-Bucket:** un único bucket con tres prefijos principales; es ideal para laboratorios o proyectos pequeños.

 Organización de prefijos según la capa

- **En la Capa Bronce:** el foco se mantiene en la ingesta y el sistema de origen, por lo que se particiona por sistema de origen y fecha de ingesta:
  - Estructura: `s3://bucket-bronze/<sistema_origen>/<entidad>/ingestion_year=YYYY/ingestion_month=MM/ingestion_day=DD/`
  - Ejemplo: `s3://mi-datalake-bronze/salesforce/clientes/ingestion_year=2026/ingestion_month=10/ingestion_day=23/dump_01.json`
- **En la Capa Plata:** el foco está en las entidades y el tiempo real del evento. Los datos están validados y convertidos a formatos columnares como Apache Parquet; ya no interesa el sistema de origen, sino que se organiza por dominio o unidad de negocio particionando por la fecha del evento:
  - Estructura: `s3://bucket-silver/<dominio>/<entidad>/event_year=YYYY/event_month=MM/`
  - Ejemplo: `s3://mi-datalake-silver/ventas/transacciones/event_year=2026/event_month=10/part-0001.parquet`
- **En la Capa Oro:** el foco reside en el consumo y los patrones de consulta. Contiene modelos dimensionales y tablas agregadas optimizadas para consultas BI, por lo que el particionado debe responder exactamente a cómo filtran los usuarios finales en el cuadro de mando (filtros `WHERE` habituales en SQL):
  - Estructura: `s3://bucket-gold/<unidad_negocio>/<caso_de_uso_o_kpi>/<dimension_filtro>=<valor>/`
  - Ejemplo: `s3://mi-datalake-gold/finanzas/kpi_ingresos_mensuales/pais=ES/year=2026/part-0001.parquet`

**Particionado al estilo Hive**

El particionado al estilo Hive es una convención heredada de Apache Hive que consiste en nombrar los prefijos (carpetas) del almacenamiento utilizando pares `columna=valor` en lugar de poner únicamente el valor. Presenta cuatro ventajas fundamentales:

1. **Autodescubrimiento de esquemas:**
  - *Sin estilo Hive (ruta tradicional como `.../ventas/ES/2026/10/datos.parquet`):* el Crawler de AWS Glue no sabe qué significan los nombres de las carpetas y crea columnas genéricas (`partition_0`, `partition_1`, `partition_2`), obligando al ingeniero de datos a editar el catálogo manualmente para renombrarlas.
  - *Con estilo Hive (`.../ventas/pais=ES/year=2026/month=10/datos.parquet`):* el Crawler extrae automáticamente el par clave-valor, documentando el esquema sin intervención humana e integrando las carpetas como columnas virtuales tipadas (`pais (string)`, `year (int)`, `month (int)`) que se unen a los campos leídos del archivo Parquet en una tabla unificada en Amazon Athena / Glue.
2. **Columnas virtuales y ahorro de espacio:**
  - *Método ineficiente (sin particionar):* en un archivo como `datos.csv`, los mismos valores (por ejemplo, `'ES'`, `'2026'`, `'10'`) se repiten físicamente en millones de filas, generando redundancia, consumiendo espacio y aumentando el coste.
  - *Método eficiente (con estilo Hive):* S3 ya conoce el país, año y mes gracias a los metadatos de la ruta. Esas columnas no se guardan física ni repetidamente dentro del archivo Parquet, sino que se cargan como columnas virtuales al ejecutar consultas (ej. `SELECT * FROM ventas;`), reduciendo el almacenamiento y abaratando y acelerando las consultas.
3. **Partition Pruning (Poda de particiones):**
  - Ante una consulta filtrada como `SELECT * FROM ventas WHERE pais = 'ES' AND year = 2026 AND month = 10;`:
  - *Sin Partition Pruning:* el motor realiza una búsqueda exhaustiva recorriendo y leyendo absolutamente todas las carpetas y archivos del bucket (por ejemplo, leyendo 10 TB completos, tardando minutos y con un coste elevado de ~50,00 $).
  - *Con Partition Pruning (estilo Hive):* gracias al mapa de particiones de AWS Glue, Athena ignora las carpetas no solicitadas y apunta directamente al prefijo que cumple el filtro `WHERE` (leyendo solo 10 GB en segundos y con un coste muy bajo de ~0,05 $).
4. **Escritura automática desde Apache Spark / AWS Glue:**
   - No es necesario crear carpetas manualmente en S3. Al utilizar la instrucción `.partitionBy()` sobre un DataFrame en memoria, Spark analiza los valores únicos de las columnas indicadas, las extrae de los archivos físicos y genera dinámicamente toda la estructura de prefijos en S3:

  ```python
  df.write \
    .mode("append") \
    .format("parquet") \
    .partitionBy("pais", "year", "month") \
    .save("s3://bucket-silver/ventas/")

  ```
