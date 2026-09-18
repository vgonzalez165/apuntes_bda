```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02
Título:                     Almacenamiento distribuido e integridad de datos
Resultados de aprendizaje:  RA3
```

# UT02: Almacenamiento distribuido e integridad de datos

## Resultados de aprendizaje asociados

**RA3**: Genera mecanismos de integridad de los datos, comprobando su mantenimiento en los sistemas de ficheros distribuidos y valorando la sobrecarga que conlleva en el tratamiento de los datos.

## Mapa de contenidos

| Apartado | Título | Descripción | Enlace |
| --- | --- | --- | --- |
| **01** | **Arquitectura y conceptos de HDFS**             | Fundamentos del almacenamiento distribuido, modelo Maestro-Esclavo (NameNode, DataNodes, Secondary NameNode), gestión de metadatos (`fsimage` y `editlogs`), particionamiento en bloques y políticas de replicación (*Rack Awareness*). | [Ver contenidos →](./01-arquitectura-conceptos-hdfs.md) |
| **02** | **Instalación y despliegue**                     | Modos de ejecución (Standalone, Pseudo-distribuido y Totalmente Distribuido), preparación de máquinas virtuales, instalación de OpenJDK 8, configuración de accesos SSH sin contraseña y variables de entorno. | [Ver contenidos →](./02-instalacion-despliegue.md) |
| **03** | **Configuración, puesta en marcha y Web UI**     | Parametrización de archivos XML (`core-site.xml`, `hdfs-site.xml`), formateo del NameNode, arranque de demonios del sistema y supervisión del clúster a través de la interfaz web (puerto 9870). | [Ver contenidos →](./03-configuracion-puesta-en-marcha.md) |
| **04** | **Operaciones con la CLI y administración HDFS** | Manipulación de ficheros (`hdfs dfs`), comandos de mantenimiento (`hdfs dfsadmin`), gestión del Modo Seguro (*Safe Mode*), instantáneas (*Snapshots*), asignación de cuotas y modelo de permisos. | [Ver contenidos →](./04-cli-hdfs-administracion.md) |
| **05** | **Integridad y calidad del dato**                | Análisis de la corrupción silenciosa de datos (*Bit Rot*), causas a nivel físico y de hardware (radiación, degradación de discos/SSD, buses de datos, memorias caché) y mecanismos de verificación por *Checksums*. | [Ver contenidos →](./05-integridad-calidad-dato.md) |



---

[Volver al índice principal](../../bda_index.md)