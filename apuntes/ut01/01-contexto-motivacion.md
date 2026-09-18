```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT01. Introducción al Big Data y paradigmas distribuidos
Apartado:                   1.- Contexto y motivación
Resultados de aprendizaje:  RA2
```

# UT01: INTRODUCCIÓN A BIG DATA Y PARADIGMAS DISTRIBUIDOS

## 1. Contexto y motivación

### 1.1.- El crecimiento del Big Data

En las últimas décadas, el volumen de datos a nivel global ha crecido de manera exponencial. La información se origina a partir de múltiples fuentes heterogéneas, entre las que destacan:

- Redes sociales.
- Sensores de Internet de las Cosas (IoT).
- Registros de transacciones y datos financieros.
- Sistemas empresariales (ERP como SAP u Oracle).
- Datos multimedia (vídeos, imágenes y audios).
- Aplicaciones móviles y registros (*logs*) de servidores.

Este fenómeno se denomina **Big Data** y se define técnicamente mediante las **3V**:

- **Volumen:** Generación masiva y constante de ingentes cantidades de información, transitando de rangos de Gigabytes (GB) a Terabytes (TB), Petabytes (PB) y Zetabytes (ZB).
- **Velocidad:** Los datos se producen con alta celeridad y demandan procesamiento continuo, abarcando desde procesamiento por lotes (*batch*) o periódico, hasta tiempo casi real (*near real-time*) y tiempo real.
- **Variedad:** Coexistencia de múltiples formatos que comprenden datos estructurados (tablas relacionales), semiestructurados (archivos JSON, XML) y no estructurados (texto plano, vídeo, imágenes).


### 1.2.- Limitaciones de los sistemas tradicionales

Las soluciones tradicionales, como las bases de datos relacionales (RDBMS) y las arquitecturas monolíticas centralizadas, fueron concebidas para operar en entornos controlados, con volúmenes moderados, esquemas muy estructurados y baja concurrencia masiva. En el contexto del Big Data, presentan cinco limitaciones críticas:

1. **Escalabilidad vertical limitada (*Scale Up*):** Para incrementar el rendimiento, los sistemas tradicionales dependen de ampliar la capacidad del único servidor existente (añadiendo CPU, RAM o almacenamiento en disco). Este enfoque es sumamente costoso y no es viable a largo plazo frente a la escalabilidad horizontal (*Scale Out*).
2. **Procesamiento centralizado:** Los datos se almacenan y procesan en un único nodo central, convirtiéndose en el cuello de botella del sistema.
3. **Falta de tolerancia a fallos:** La dependencia de un nodo centralizado implica que la caída o fallo de este servidor provoca la interrupción total del servicio.
4. **Dificultad ante datos semiestructurados o no estructurados:** Los RDBMS exigen esquemas relacionales rígidos. Puesto que cerca del 80% de los datos corporativos actuales son no estructurados, su gestión mediante soluciones heredadas resulta ineficiente.
5. **Procesamiento por lotes lento:** El análisis de grandes volúmenes de datos mediante arquitecturas tradicionales requiere tiempos de ejecución prolongados (horas o días).


### 1.3.- Nuevos enfoques: Sistemas Distribuidos

Frente a las limitaciones de los sistemas convencionales, el paradigma distribuido adoptado por soluciones como Apache Hadoop introduce un nuevo marco de trabajo:

- **Almacenamiento distribuido (HDFS):** Los archivos de gran tamaño se dividen en bloques y se reparten entre múltiples servidores o nodos del clúster, aprovechando la capacidad acumulada de muchas máquinas.
- **Procesamiento distribuido (MapReduce, Spark):** Las tareas computacionales se dividen y se ejecutan en paralelo directamente sobre los distintos nodos del clúster, reduciendo sensiblemente los tiempos de ejecución.
- **Tolerancia a fallos:** Los datos se replican en diferentes nodos físicos y las tareas fallidas se reasignan automáticamente, garantizando alta disponibilidad sin requerir respaldos manuales constantes.
- **Escalabilidad horizontal:** Es posible incrementar la capacidad de cómputo y almacenamiento añadiendo nuevos nodos estándar (*commodity hardware*) al clúster sin necesidad de interrumpir el servicio.



---

[Volver al índice](ut01_index.md)