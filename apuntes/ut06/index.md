# UT06: Monitorización, Fiabilidad y Rendimiento del Sistema

* **Resultado de Aprendizaje asignado:** **RA 4** (*Realiza el seguimiento de la monitorización de un sistema, asegurando la fiabilidad y estabilidad de los servicios que se proveen.*)
* **Criterios de Evaluación cubiertos:**
* **CE 4.a:** *Se han aplicado herramientas de monitorización eficiente de los recursos.*
* **CE 4.b:** *Se han recogido métricas, procesamiento y visualización de los datos.*
* **CE 4.c:** *Se han generado alertas para detectar un riesgo o mal funcionamiento.*
* **CE 4.d:** *Se ha comprobado que las herramientas usadas ofrecen un rendimiento elevado con rapidez.*
* **CE 4.e:** *Se ha comprobado la fiabilidad de los datos según respuestas.*
* **CE 4.f:** *Se ha analizado la estabilidad de servicios.*



---

## 1. Fundamentos de Observabilidad en Sistemas Distribuidos y Big Data

* **Los tres pilares de la observabilidad:**
* **Métricas:** Valores numéricos agregados en series temporales (uso de CPU, saturación de RAM, I/O de disco, bytes transmitidos).
* **Logs:** Registros estructurados y no estructurados de eventos discretos en el tiempo.
* **Trazas distribuidas (*Traces*):** Seguimiento del ciclo de vida de una petición a través de múltiples microservicios o nodos del clúster.


* **Retos específicos en arquitecturas de Big Data:**
* Asimetría de nodos (*Data Skew* / *Stragglers*): Un único nodo lento ralentiza todo el trabajo distribuido.
* Saturación del recolector de basura de Java (*Garbage Collection pauses*): Congelación de nodos NameNode o Executors de Spark por mala gestión de memoria Heap.
* Cuellos de botella en red vs. almacenamiento vs. computación.


* **Definición y control de compromisos de servicio:**
* Indicadores de nivel de servicio (**SLI**), Objetivos (**SLO**) y Acuerdos de Nivel de Servicio (**SLA**).
* Medición de disponibilidad (*Uptime*), tasa de fallos en peticiones y percentiles de latencia (p50, p95, p99).



---

## 2. Monitorización On-Premise: Ecosistema Apache Hadoop

* **Interfaces de administración Web nativas:**
* **NameNode Web UI (`http://<nn-host>:9870`):**
* Capacidad DFS total, usada y disponible.
* Conteo de bloques sanos, corruptos y bloques sub-replicados (*Under-Replicated Blocks*).
* Estado de salud de DataNodes: Nodos vivos (*Live*), muertos (*Dead*) y en proceso de desmantelamiento (*Decommissioning*).


* **YARN ResourceManager Web UI (`http://<rm-host>:8088`):**
* Supervisión global de vCores y memoria RAM asignada vs. reservada.
* Inspección de colas de planificación (*Capacity Scheduler* / *Fair Scheduler*).
* Auditoría de aplicaciones en curso: Identificación de tareas atascadas, fallidas (*FAILED*) y consumo de contenedores.




* **Herramientas de diagnóstico y auditoría por línea de comandos (CLI):**
* `hdfs dfsadmin -report`: Informe de salud del sistema de ficheros distribuido.
* `yarn node -list` y `yarn application -status <app_id>`: Control del planificador.
* Comandos del sistema operativo anfitrión: `iostat`, `top`/`htop` y `vmstat` para detectar cuellos de botella en disco y memoria swap.


* **Ecosistema Open Source moderno de métricas:**
* Exposición de métricas internas mediante **JMX (Java Management Extensions)**.
* Recogida y almacenamiento de series temporales con **Prometheus**.
* Visualización y tableros operativos centralizados con **Grafana**.



---

## 3. Monitorización Gestionada en Cloud: Amazon CloudWatch

En arquitecturas *Serverless* y clústeres elásticos en la nube, la monitorización se centraliza en servicios gestionados capaces de unificar métricas de almacenamiento, cómputo y flujos de datos en tiempo real.

---

* **Arquitectura y componentes centrales de CloudWatch:**
* **CloudWatch Metrics:**
* Espacios de nombres (*Namespaces*), dimensiones y agregaciones estadísticas (Average, Sum, Maximum, Sample Count).
* Resolución estándar (intervalos de 1 minuto) vs. alta resolución (*High-Resolution*, intervalos de 1 segundo).


* **Métricas clave por servicio de Big Data en AWS:**
* **Amazon S3:** `BucketSizeBytes`, `NumberOfObjects`, peticiones `4xxErrors` y `5xxErrors`.
* **AWS Lambda (Ingesta):** `Invocations`, `Duration`, `Errors`, `Throttles` y concurrencia.
* **AWS EMR / AWS Glue:** `CPUUtilization`, `MemoryAllocatedMB`, `RunningNodes`, tiempos de ejecución de jobs.




* **Centralización y análisis de trazas con CloudWatch Logs:**
* Conceptos de Grupos de Registro (*Log Groups*) y Flujos de Registro (*Log Streams*).
* Políticas de retención para optimización de costes.
* Consultas interactivas en tiempo real mediante **CloudWatch Logs Insights** (sintaxis de filtrado, agregaciones y conteos de errores).


* **Construcción de cuadros de mando operativos (*CloudWatch Dashboards*):**
* Diseño de paneles visuales (gráficos de líneas, números de estado, medidores de porcentaje).
* Consolidación de métricas de almacenamiento, ingesta y procesamiento en una única vista.



---

## 4. Gestión de Alarmas, Notificaciones y Auto-reparación

* **Ciclo de vida y evaluación de una alarma en CloudWatch:**
* Estados de una alarma: `OK`, `ALARM` y `INSUFFICIENT_DATA`.
* Umbrales estáticos (ej. *Uso de CPU > 85% durante 2 periodos consecutivos*).
* **Detección de anomalías basada en Machine Learning:** Modelado automático de patrones estacionales y bandas de desviación estándar dinámicas.


* **Orquestación de notificaciones multicanal con Amazon SNS:**
* Creación de temas (*SNS Topics*) y configuración de suscripciones por correo electrónico y SMS.
* Enrutamiento de avisos críticos de caída o fallo de jobs a los administradores de la plataforma.


* **Respuestas reactivas y automatización (*Remediation*):**
* Vinculación de alarmas con políticas de **Auto Scaling**: Escalado horizontal automático añadiendo instancias de cómputo ante picos de demanda.
* Disparo automático de funciones AWS Lambda correctivas para reiniciar contenedores o purgar particiones atascadas.



---

## 5. Análisis de Fiabilidad, Rendimiento y Detección de Cuellos de Botella

* **Evaluación del rendimiento y fiabilidad:**
* Cálculo de caudal (*Throughput*): Registros por segundo y megabytes por segundo en tuberías de datos.
* Validación de la consistencia y fiabilidad de las respuestas del clúster bajo estrés.


* **Identificación de fallos comunes en entornos masivos:**
* Estrangulamiento (*Throttling*) de peticiones API por saturación de cuotas en Cloud.
* Contención de I/O en disco durante procesos intensivos de Shuffle en Spark/MapReduce.
* Fallos por falta de memoria física: Detección de eventos *Out Of Memory Killer* (OOM) en los nodos trabajadores.


* **Pruebas de resiliencia y tolerancia a fallos (*Chaos Engineering básico*):**
* Simulación de caída abrupta de un nodo trabajador y comprobación del comportamiento de la replicación y reasignación de tareas sin parada de servicio.



---

## 6. Propuesta de Laboratorios y Actividades Prácticas

* **Práctica 6.1: Inspección de Salud y Diagnóstico de Fallos en Hadoop (On-Premise)**
* Saturar artificialmente el espacio en HDFS y desconectar un contenedor DataNode.
* Auditar el clúster usando la NameNode Web UI (`:9870`) y el comando `hdfs dfsadmin -report`.
* Analizar la respuesta del sistema: cómo detecta los bloques sub-replicados y cómo reacciona el NameNode.


* **Práctica 6.2: Monitorización, Alarmas y Notificaciones en AWS Cloud**
* Configurar un *CloudWatch Dashboard* que monitorice el tamaño de un bucket S3 y las métricas de ejecución de una función Lambda.
* Crear una alarma de CloudWatch vinculada a un topic de Amazon SNS con alerta por correo electrónico que se active cuando la tasa de fallos de la función supere el 5%.
* Provocar ejecuciones fallidas enviando datos con formato erróneo y verificar la recepción de la alerta en tiempo real.