```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02. Almacenamiento distribuido e integridad de datos
Apartado:                   5.- Mecanismos de integridad de datos y calidad del dato
Resultados de aprendizaje:  RA3
```

# UT01: ALMACENAMIENTO DISTRIBUIDO E INTEGRIDAD DE DATOS

## 5. Mecanismos de integridad y calidad del dato

A medida que se incrementa el volumen de datos almacenados y el número de servidores que componen el clúster, aumenta exponencialmente la probabilidad estadística de experimentar **corrupción silenciosa de datos (*Silent Data Corruption* o *Bit Rot*)**.

### 5.1 Concepto de Corrupción Silenciosa de Datos

Se produce cuando el contenido físico de un bloque o archivo en almacenamiento sufre alteraciones respecto a su estado original sin que la controladora de disco, el bus de E/S o el sistema operativo subyacente detecten anomalía alguna ni generen códigos de excepción.

A diferencia de un fallo catastrófico de hardware (donde una unidad deja de responder o genera un error de I/O bloqueante), la corrupción silenciosa devuelve al sistema un bloque corrompido marcado formalmente como «válido».

```
                     DATOS EN ORIGEN (01011001)
                                 │
                                 ▼
┌───────────────────────────────────────────────────────────────────┐
│                    CAUSAS FÍSICAS DE CORRUPCIÓN                   │
├───────────────────────────────┬───────────────────────────────────┤
│ • Radiación y Rayos Cósmicos  │ • Ruido Eléctrico en Buses        │
│   Impacto de partículas alfa  │   Interferencias en PCIe/SATA/SAS │
│   mutando bits en RAM         │   durante transferencias          │
├───────────────────────────────┼───────────────────────────────────┤
│ • Degradación del Medio Físico│ • Caché / Firmware de Controladora│
│   Pérdida magnética en HDDs / │   Cálculo de ECC erróneo sobre    │
│   fuga de electrones en SSDs  │   datos ya alterados en caché     │
└───────────────────────────────┴───────────────────────────────────┘
                                 │
                                 ▼
                     DATOS LEÍDOS (01010001)
            [El disco reporta lectura correcta sin errores]

```

### 5.2 Causas físicas y lógicas de corrupción

* **Rayos cósmicos y radiación ambiental:** Las partículas alfa y neutrones de alta energía procedentes del entorno físico impactan en los circuitos integrados de las memorias RAM (tanto de los buses como de las controladoras de disco), provocando una inversión del estado eléctrico de los transistores (*bit flip*, mutando un 0 en un 1 o viceversa).


* **Degradación del medio físico:**
* *Discos mecánicos (HDD):* Pérdida progresiva de la remanencia magnética en los dominios de los platos o microdesalineaciones en las cabezas de lectura/escritura.


* *Unidades de estado sólido (SSD):* Fuga de electrones en las celdas de memoria NAND Flash inducida por el paso del tiempo o por el desgaste acumulado de ciclos de programado/borrado (P/E cycles).




* **Ruido eléctrico e interferencias en buses:** Variaciones de tensión o perturbaciones electromagnéticas en cables y pistas de conexión (PCIe, SATA, SAS) durante el tránsito de datos entre la placa base y el almacenamiento físico, alterando la trama binaria justo antes de su escritura.


* **Caché o firmware defectuoso en la controladora de disco:** Las controladoras intercalan memorias caché intermedias antes de persistir los datos en plato o memoria Flash. Si un bit se corrompe en la memoria de la controladora antes de la escritura física, el algoritmo de detección de errores de sector (ECC) del propio disco se calculará tomando como base el dato **ya corrupto**. En consecuencia, al leer el disco posteriormente, el ECC de sector coincidirá a la perfección y el hardware considerará el bloque como completamente íntegro, enmascarando el error ante el sistema operativo.



Para mitigar este problema inherente al hardware masivo, HDFS incorpora mecanismos de integridad a nivel de aplicación mediante el cálculo de **sumas de verificación (*Checksums*)** automáticas por cada fragmento de bloque y auditorías periódicas de detección y re-replicación de datos corruptos.

---

[Volver al índice](ut01_index.md)