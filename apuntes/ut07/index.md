# UT07: Analítica de Datos y Simulación de Modelos de BI

* **Resultado de Aprendizaje asignado:** **RA 5** (*Valida las técnicas de Big Data para transformar una gran cantidad de datos en información significativa, facilitando la toma de decisiones de negocios.*)
* **Criterios de Evaluación cubiertos:**
* **CE 5.a:** *Se han seleccionado gran cantidad de datos estructurados y no estructurados para reforzar la función de BI.*
* **CE 5.b:** *Se ha realizado la limpieza y transformación de datos en base a los objetivos predeterminados.*
* **CE 5.c:** *Se ha comprobado que el Big Data multiplica la relevancia y la utilidad del BI para el negocio.*
* **CE 5.d:** *Se han conjugado dentro de un modelo de empresa datos de clientes, financieros de ventas, de productos, de marketing, de redes sociales, de la competencia, entre otros, para extraer un análisis valioso y efectivo para el negocio.*
* **CE 5.e:** *Se ha evaluado e interpretado la información extraída de los datos y su influencia en el triunfo de diferentes negocios.*
* **CE 5.f:** *Se ha simulado la implantación de un modelo de Inteligencia de negocios BI.*



---

## 1. El Salto Estratégico: Del BI Tradicional al Big Data Analytics

* **Límites del Business Intelligence clásico:**
* Dependencia de bases de datos relacionales fijas, informes descriptivos estáticos y retrasos de actualización diarios o semanales.


* **El valor multiplicador del Big Data en el BI moderno (CE 5.c):**
* Integración de las dimensiones de **Velocidad** (análisis *near real-time*) y **Variedad** (datos no estructurados).
* Evolución del análisis: Analítica Descriptiva (*¿Qué ocurrió?*) $\rightarrow$ Diagnóstica (*¿Por qué ocurrió?*) $\rightarrow$ Predictiva (*¿Qué pasará?*) $\rightarrow$ Prescriptiva (*¿Qué debemos hacer?*).
* Casos de éxito y retorno de inversión (ROI): Cómo la toma de decisiones basada en datos (*Data-Driven*) impacta en la competitividad y rentabilidad empresarial.



---

## 2. Ingesta, Limpieza y Transformación de Fuentes Heterogéneas

* **Selección de fuentes de datos (CE 5.a):**
* **Datos estructurados:** Transacciones ERP, bases de datos transaccionales de ventas, inventario y registros contables.
* **Datos no estructurados y semiestructurados:** Comentarios de clientes en redes sociales, logs de navegación web (*clickstream*), tickets de soporte en texto libre y feeds RSS de la competencia.


* **Técnicas de preparación y saneamiento de datos masivos (CE 5.b):**
* Detección y tratamiento de valores nulos o atípicos (*outliers*) a escala distribuida con **Spark SQL**.
* Estandarización de formatos temporales, monedas y codificaciones de caracteres (UTF-8).
* Procesamiento básico de lenguaje natural (NLP) sobre datos no estructurados: Limpieza de texto, extracción de palabras clave y análisis de polaridad/sentimiento para alimentar atributos de BI.
* Enriquecimiento del dato (*Data Wrangling*): Cruce de datos transaccionales con metadatos contextuales y geográficos.



---

## 3. Modelado Multifuente de Negocio (*Data Modeling* para Big Data)

* **Diseño del modelo de empresa integrado (CE 5.d):**
* Fusión de dominios funcionales: Clientes, finanzas, campañas de marketing digital, catálogo de productos y menciones sociales.


* **Arquitectura de modelado dimensional:**
* Tablas de Hechos (*Fact Tables*): Hechos transaccionales, de instantánea periódica y acumulativa.
* Tablas de Dimensiones (*Dimension Tables*): Dimensiones conformadas (Clientes, Tiempo, Geografía, Canales).
* Esquemas en Estrella (*Star Schema*) y Copo de Nieve (*Snowflake Schema*) adaptados al Data Lake y Data Warehouse.
* Gestión de dimensiones que cambian lentamente (**SCD** - *Slowly Changing Dimensions*, Tipos 1 y 2).


* **Construcción de Data Marts especializados:** Creación de vistas consolidadas orientadas a casos de uso concretos (ej. *Data Mart de Fidelización y Churn*, *Data Mart de Conversión de Marketing*).

---

## 4. Consultas Analíticas Distribuidas e Interactivas

* **Capa de consumo analítico sobre el Data Lake:**
* Desacoplamiento total entre computación analítica y almacenamiento físico.


* **Consultas ad-hoc serverless con Amazon Athena:**
* Ejecución de consultas estándar ANSI SQL directamente sobre ficheros Parquet/ORC alojados en Amazon S3.
* Particionado y poda de datos en Athena: Reducción drástica de gigabytes escaneados y optimización directa de costes de facturación.
* Creación de vistas analíticas intermedias y tablas CTAS (*Create Table As Select*).


* **Optimización con Spark SQL:** Funciones de ventana (*Window Functions*), agregaciones complejas y persistencia en memoria para acelerar la entrega de métricas.

---

## 5. Simulación e Implantación de un Modelo BI con Amazon QuickSight

Amazon QuickSight permite desplegar tableros empresariales interactivos conectados directamente al Data Lake o a motores federados, habilitando capacidades analíticas avanzadas sin necesidad de gestionar servidores locales.

---

* **Arquitectura de Amazon QuickSight (CE 5.f):**
* Conexión a orígenes de datos: Amazon Athena, Amazon S3, Amazon RDS y bases de datos externas.
* El motor **SPICE** (*Super-fast, Parallel, In-memory Calculation Engine*): Almacenamiento columnar en memoria para consultas instantáneas y concurrencia multiusuario.


* **Diseño del conjunto de datos (*Datasets*):**
* Definición de tipos de campos, cálculos derivados y funciones de fecha.
* Aplicación de filtros de seguridad a nivel de fila (**RLS** - *Row-Level Security*) para restringir el acceso según el perfil de usuario.


* **Construcción de cuadros de mando empresariales:**
* Selección de visualizaciones según la naturaleza del dato: Mapas de calor, diagramas de dispersión, gráficos de árbol (*treemaps*) y series temporales.
* Diseño de **KPIs clave**: Indicadores numéricos de rendimiento con objetivos y barras de progreso.
* Controles interactivos: Filtros cruzados, selectores dinámicos y acciones de detalle (*Drill-down*).


* **Capacidades avanzadas:**
* Generación de previsiones automáticas (*ML-Powered Forecasting*) y detección de anomalías sin programar modelos.
* Creación de **Historias de Datos** (*Data Stories*) para comunicar conclusiones a directivos y clientes finales.



---

## 6. Conectividad con el Ecosistema BI Empresarial Híbrido

* **Integración con herramientas de mercado mediante JDBC/ODBC:**
* Configuración de conexiones desde clientes externos hacia el clúster Big Data (Hadoop Hive / Spark Thrift Server / Athena).
* Explotación con herramientas de visualización líderes: **Power BI**, **Tableau**, **Apache Superset** o **Metabase**.


* **Estrategias de actualización y refresco:** Modos de consulta directa (*DirectQuery / Live Connection*) vs. importación programada a la capa de caché.

---

## 7. Interpretación de la Información y Toma de Decisiones Estratégicas

* **Extracción de conclusiones y valor de negocio (CE 5.e):**
* Análisis de abandono de clientes (*Customer Churn Rate*): Identificación de patrones de desconexión cruzando actividad en la app y quejas en redes sociales.
* Segmentación de clientes (Modelo RFM: Recencia, Frecuencia y Monetización) para optimización de presupuestos de marketing.
* Análisis de elasticidad de precios frente a movimientos de la competencia.


* **Presentación efectiva al cliente final:**
* Principios de diseño visual y narrativa del dato (*Data Storytelling*).
* Cómo traducir métricas técnicas (terabytes, latencias) en métricas de negocio (conversión, ingresos, costes).



---

## 8. Propuesta de Laboratorios y Proyecto Integrador de la Unidad

* **Práctica 7.1: Pipeline de Transformación Multifuente (Spark SQL / Athena)**
* Unificar un archivo CSV de clientes, una base de datos relacional de pedidos y un JSON semiestructurado con interacciones en redes sociales.
* Limpiar nulos, extraer el sentimiento de las publicaciones y generar una tabla curada consolidada en formato Parquet en S3.


* **Práctica 7.2: Proyecto Integrador — Cuadro de Mandos Ejecutivo en Amazon QuickSight**
* Conectar QuickSight a la tabla generada en Athena mediante SPICE.
* Diseñar un cuadro de mando integral con métricas de ventas por región, satisfacción del cliente (extraída del texto) y previsión automática de ingresos.
* Redactar un informe ejecutivo (o Data Story) con 3 decisiones estratégicas recomendadas basadas en los datos analizados.