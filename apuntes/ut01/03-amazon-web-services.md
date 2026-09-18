```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT01. Introducción al Big Data y paradigmas distribuidos
Apartado:                   3.- Amazon Web Services
Resultados de aprendizaje:  RA2
```

# UT01: INTRODUCCIÓN A BIG DATA Y PARADIGMAS DISTRIBUIDOS


## 3. Amazon Web Services (AWS): ¿Qué es?

### 3.1.- Definición y concepto

**Amazon Web Services (AWS)** es la plataforma de computación en la nube más adoptada del mundo. Consiste en una infraestructura global de centros de datos repartidos por todo el planeta que Amazon ofrece bajo un modelo de alquiler a empresas y particulares. AWS funciona como un ecosistema integral de servicios gestionados que permite almacenar y procesar petabytes de información sin necesidad de invertir en hardware local.


### 3.2.- Pilares del modelo Cloud

La computación en la nube en AWS se fundamenta en tres principios esenciales:

1. **Pago por uso (*Pay-as-you-go*):** Los costes se facturan estrictamente en función de los recursos utilizados (por ejemplo, segundos de computación o Gigabytes de almacenamiento). Se debe tener en cuenta que los recursos encendidos consumen saldo de manera continuada aun cuando permanezcan inactivos.
2. **Elasticidad:** Capacidad de escalar (sumar potencia o instancias) o desescalar (reducir recursos) de manera automática e instantánea en respuesta a la demanda.
3. **Agilidad:** Permite el despliegue de infraestructuras complejas (como clústeres de bases de datos) en cuestión de minutos a través de la consola web o mediante Infraestructura como Código (IaC), sustituyendo las semanas o meses del método de adquisición tradicional.


### 3.3.- Infraestructura global de AWS

La infraestructura física de AWS se organiza en tres niveles conceptuales:

- **Regiones:** Zonas geográficas aisladas e independientes en diferentes partes del mundo, delimitadas para garantizar proximidad a los usuarios y cumplir con normativas de protección de datos.
- **Zonas de Disponibilidad (AZ - *Availability Zones*):** Conjunto de uno o varios centros de datos físicos ubicados dentro de una región, distanciados geográficamente pero enlazados mediante fibra de muy baja latencia. La alta disponibilidad exige replicar los servicios en al menos dos AZs distintas.
- **Puntos de Presencia (*Edge Locations*):** Centros de datos de menor envergadura distribuidos mundialmente para almacenar contenido en caché y mitigar la latencia de acceso.


### 3.4.- Modelo de responsabilidad compartida

Marco que define los límites de seguridad y cumplimiento legal entre el proveedor y el cliente:

- **Seguridad DE la nube (Responsabilidad de AWS):** Protección de la infraestructura física (servidores, cableado, centros de datos) y del software fundamental (hipervisores, capa de virtualización).
- **Seguridad EN la nube (Responsabilidad del usuario):** Todo aquello que el cliente despliega y configura dentro de la plataforma. Incluye:
  * Gestión de identidades y accesos mediante AWS IAM.
  * Cifrado de datos en reposo y en tránsito (por ejemplo, en S3).
  * Configuración, mantenimiento y actualización del sistema operativo y aplicaciones en máquinas virtuales (EC2).



### 3.5.- Historia de AWS

- **1994:** Jeff Bezos funda Amazon (inicialmente llamada Cadabra) en Seattle, operando exclusivamente como una tienda online de libros.
- **Principios de 2000:** El rápido crecimiento comercial genera problemas operativos en su infraestructura, requiriendo diseñar arquitecturas tecnológicas desde cero en cada proyecto.
- **Reorganización interna:** La compañía rediseñó sus sistemas desacoplando los equipos y las aplicaciones, pasando a comunicarse exclusivamente mediante interfaces de programación (APIs) documentadas.
- **2006:** Tras dominar la operativa de infraestructuras distribuidas masivas, Amazon lanza comercialmente **AWS** para ofrecer almacenamiento y computación bajo un modelo de servicio público. En la actualidad, cuenta con más de 200 servicios que abarcan computación, bases de datos, analítica, redes, inteligencia artificial y herramientas de desarrollo.



### 3.6.- Servicios de AWS para Big Data

Amazon AWS ha ido creciendo a lo largo de los años hasta tener decenas de servicios de todo tipo. En la siguiente tabla se muestran algunos de los más relevantes.


|     | Servicio | Tipo / Propósito | Descripción funcional |
| --- | --- | --- | --- |
| ![s3](./imgs/s3.png) | **Amazon S3** *(Simple Storage Service)* | Almacenamiento de Objetos / *Data Lake* | Almacenamiento virtualmente infinito y de bajo coste con durabilidad del 99,999999999% (11 nueves). Admite datos estructurados y no estructurados, sirviendo de base para los *Data Lakes* al desvincular el almacenamiento del cómputo. |
| ![ec2](./imgs/ec2.png) | **Amazon EC2** *(Elastic Cloud Compute)* | Computación / Máquinas Virtuales | Alquiler de instancias virtuales bajo demanda en minutos. Dispone de gran variedad de configuraciones (CPU, RAM, GPU) y escalado automático (*Auto Scaling*) para absorber cargas de trabajo masivas. |
| ![EMR](./imgs/emr.png) | **Amazon EMR** *(Elastic MapReduce)* | Procesamiento Distribuido Gestionado | Automatiza el despliegue de clústeres de Hadoop y Spark sobre instancias EC2 utilizando Amazon S3 como capa de almacenamiento. Permite crear clústeres efímeros y destruirlos tras la ejecución a muy bajo coste. |
| ![Dynamo](./imgs/dynamo.png) | **Amazon DynamoDB** | Base de Datos NoSQL (Clave-Valor) | Base de datos NoSQL gestionada con latencia de milisegundos a cualquier escala. Distribuye los datos en particiones físicas mediante una clave de partición y escala horizontalmente sin administrar servidores. |
| ![Redshift](./imgs/redshift.png) | **Amazon Redshift** | *Data Warehouse* / Base de Datos Columnar | Base de datos relacional columnar optimizada para operaciones analíticas complejas (OLAP) y consultas SQL sobre miles de millones de filas mediante procesamiento paralelo en clúster. |
| ![Lambda](./imgs/lambda.png) | **AWS Lambda** | Computación *Serverless* / FaaS *(Function as a Service)* | Ejecuta código ante eventos sin necesidad de aprovisionar ni administrar servidores, facturando por los milisegundos de cómputo utilizados. En Big Data se aplica a flujos ligeros en tiempo real, validación y limpieza en pipelines. |
| ![Athena](./imgs/athena.png) | **Amazon Athena** | Motor de Consultas SQL *Serverless* | Motor interactivo para realizar consultas SQL estándar directamente sobre datos almacenados en S3 (en formatos como CSV, JSON, Parquet u ORC) sin aprovisionar clústeres. |
| ![Glue](./imgs/glue.png) | **AWS Glue** | Integración de Datos / ETL *Serverless* | Servicio administrado para la preparación, transformación y movimiento de datos (ETL). Integra el *Glue Data Caalog* (metadatos), *Glue Crawlers* (inferencia automática de esquemas) y *ETL Jobs* (procesamiento masivo basado en Apache Spark).|




---

[Volver al índice](ut01_index.md)