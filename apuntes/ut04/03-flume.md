3. Ingesta de Logs y Flujos Continuos On-Premise: Apache Flume

    Arquitectura de un agente Flume:

        Source: Receptores de eventos (Netcat, Exec, Spooling Directory, Syslog).

        Channel: Colas amortiguadoras intermedias (Memory Channel vs. File Channel para persistencia y durabilidad).

        Sink: Destinos de volcado (HDFS Sink, Logger Sink).

    Topologías avanzadas de agentes:

        Consolidación (Fan-in): Múltiples fuentes web convergiendo en un nodo central.

        Replicación y multiplexación (Fan-out): Envío simultáneo a HDFS y a un sistema de indexación/alertas.

    Casos prácticos: Monitorización e ingesta en caliente de ficheros de log de servidores web (/var/log/nginx/access.log) directamente hacia HDFS.