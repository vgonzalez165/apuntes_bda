```
------------- ESPECIALIZACIÓN EN INTELIGENCIA ARTIFICIAL Y BIG DATA -------------
---------------------------------------------------------------------------------

Módulo:                     BIG DATA APLICADO
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT03. ALMACENAMIENTO DISTRIBUIDO EN CLOUD CON S3. DATA LAKES
Apartado:                   1.- Object Storage con Amazon S3
Resultados de aprendizaje:  RA3
```

## 1.OBJECT STORAGE CON AMAZON S3

### 1.1. Conceptos fundamentales

**Amazon S3 (Simple Storage Service)** es un servicio de almacenamiento de objetos que ofrece cuatro pilares principales:

- **Escalabilidad:** proporciona almacenamiento ilimitado que crece según las necesidades.
- **Disponibilidad de datos:** alta disponibilidad y durabilidad (99.999999999%).
- **Seguridad:** incorpora cifrado de extremo a extremo y control de acceso mediante IAM.
- **Rendimiento:** ofrece alto rendimiento y acceso de baja latencia.


**Arquitectura plana frente a jerarquía física**

A diferencia de un sistema de archivos tradicional que utiliza una jerarquía física basada en estructuras de directorios y carpetas (acceso por ruta de carpetas, más lento), Amazon S3 es un sistema plano donde cada objeto se identifica mediante una **clave única** para un acceso directo, rápido y escalable.

Sus elementos básicos son:

- **Bucket:** es el contenedor fundamental de S3. Los nombres de los buckets deben ser globalmente únicos.
- **Objeto:** es el archivo propiamente dicho (por ejemplo, un archivo CSV, un modelo de IA entrenado `.pkl` o un vídeo).
- **Prefijo (carpeta virtual):** en S3 no existen las carpetas reales. Si se sube un archivo llamado `datos/2024/ventas.csv`, el nombre completo del objeto es esa ruta completa. La consola de AWS muestra carpetas para facilitar la navegación, pero técnicamente son solo prefijos.


**Conceptos previos a las clases de almacenamiento**
S3 dispone de diferentes tipos de almacenamiento en función de la rapidez requerida para acceder a los datos y el presupuesto disponible. Para comprenderlos hay que distinguir dos conceptos:

* **Durabilidad:** es la posibilidad de que un objeto se pierda. Amazon S3 sigue la regla de los 11 nueves (99.999999999%); por ejemplo, si se almacenan 10 millones de documentos, se podría perder un documento cada 10.000 años.


* **Disponibilidad:** se refiere a la frecuencia con la que un servicio podría enfrentar interrupciones. Por ejemplo, una plataforma de streaming con una disponibilidad del 99.99% podría experimentar interrupciones durante aproximadamente 53 minutos al año.



**Clases de almacenamiento en Amazon S3**

| Clase de almacenamiento        | Caso de uso y acceso                                                                                                                     | Zonas (AZ) | Duración / Tamaño mínimo          | Precio orientativo   |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------------------------- | -------------------- |
| **S3 Standard**                | Datos activos accedidos frecuentemente (sitios web, apps móviles, distribución de contenido, Big Data). Acceso en milisegundos.          | > 3 AZ     | Sin mínimo especificado           | 0,023 $ por GB/mes   |
| **S3 Express One Zone**        | Acceso ultrarrápido y selección precisa de AZ. Ideal para cargas de trabajo intensivas.                                                  | 1 AZ       | Sin mínimo especificado           | 0,11 $ por GB/mes    |
| **S3 Standard-IA**             | Datos accedidos infrecuentemente. Acceso en milisegundos. Aplica tarifa de recuperación por GB.                                          | > 3 AZ     | 30 días / Tamaño mínimo de objeto | 0,0125 $ por GB/mes  |
| **S3 One Zone-IA**             | Datos menos accedidos. Acceso en milisegundos. Aplica tarifa de recuperación por GB.                                                     | 1 AZ       | 30 días / Tamaño mínimo de objeto | 0,01 $ por GB/mes    |
| **Glacier Instant Retrieval**  | Almacenamiento de archivos de bajo costo. Recuperación en milisegundos para datos raramente accesibles.                                  | > 3 AZ     | 90 días                           | 0,004 $ por GB/mes   |
| **Glacier Flexible Retrieval** | Recuperación de datos asíncrona (de minutos a horas para recuperar los datos).                                                           | > 3 AZ     | 90 días                           | 0,0036 $ por GB/mes  |
| **Glacier Deep Archive**       | Almacenamiento a largo plazo. Recuperación asíncrona de 12 a 48 horas.                                                                   | > 3 AZ     | 180 días                          | 0,00099 $ por GB/mes |
| **S3 Intelligent-Tiering**     | Mueve objetos entre dos niveles (frecuente/infrecuente) en base a su uso. Cuota mensual por monitorización de 0,0025 $ por 1000 objetos. | -          | -                                 | 0,023 $ / 0,0125 $   |

---

### 1.2. Trabajando con Amazon S3

**Acceso al entorno de prácticas (AWS Academy)**

1. Acceder a `[https://www.awsacademy.com/vforcesite/LMS_Login](https://www.awsacademy.com/vforcesite/LMS_Login)` y seleccionar **Student Login**.


2. En el panel de control, seleccionar el curso **AWS Academy Learner Lab** e ir al apartado **Módulos**.


3. Buscar y pulsar en **Lanzamiento del Laboratorio para el alumnado de AWS Academy**.


4. Iniciar el laboratorio haciendo clic en **Start Lab**. Cuando el icono de AWS cambie a color verde, el acceso al laboratorio estará disponible.


5. Al pulsar en el enlace de AWS se abre la consola de administración, desde donde se puede acceder a todos los servicios (S3, EMR, EC2, IAM, Athena, Lambda, AWS Glue) y consultar el panel de costes (el laboratorio cuenta con un saldo de 50 $).



**Creación y configuración de un Bucket**
Dentro del servicio S3, al pulsar en **Crear bucket** se configuran los siguientes parámetros:

* **Tipo de bucket:**
* *Uso general:* recomendado para la mayoría de los casos de uso y patrones de acceso. Almacena objetos de forma redundante en múltiples zonas de disponibilidad y permite combinar clases de almacenamiento.


* *Directorio:* recomendado para casos de uso de baja latencia; utiliza únicamente la clase S3 Express One Zone dentro de una única zona de disponibilidad.




* **Nombre del bucket:** sigue el formato `s3://bucket/prefijo`. Debe ser único en todo el mundo, tener entre 3 y 63 caracteres, empezar y terminar con una letra o número, y contener únicamente letras minúsculas (`a-z`), números (`0-9`), puntos (`.`) y guiones (`-`).


* **Propiedad de objetos:** permite gestionar el acceso mediante ACLs (Listas de control de acceso) o mediante políticas. Se selecciona **ACL deshabilitadas (recomendado)** para que todos los objetos sean propiedad de la cuenta y el acceso se especifique exclusivamente mediante políticas.


* **Bloqueo de acceso público:** por defecto se marca **Bloquear todo el acceso público**, lo cual activa cuatro reglas independientes que bloquean e ignoran tanto las ACLs nuevas o existentes como las políticas de bucket y puntos de acceso públicos.


* **Control de versiones de buckets:** permite mantener múltiples variantes de un objeto en el mismo bucket para conservar, recuperar y restaurar versiones ante acciones involuntarias o errores en aplicaciones (se deja en *Desactivar* inicialmente).


* **Etiquetas (opcional):** permite agregar hasta 50 etiquetas para asignación de costos, organización de recursos y control de acceso (administrables mediante las API `s3:ListTagsForResource`, `s3:TagResource` y `s3:UntagResource`).


* **Cifrado predeterminado:** se aplica automáticamente del lado del servidor a los nuevos objetos. Ofrece tres tipos: claves administradas de Amazon S3 (**SSE-S3**), claves de AWS Key Management Service (**SSE-KMS**) o cifrado de doble capa (**DSSE-KMS**), además de la opción de habilitar una *Clave de bucket* para reducir costos en SSE-KMS.



**Carga de objetos y propiedades de identificación**
Una vez creado el bucket, se utiliza el botón **Cargar** para añadir archivos o carpetas arrastrándolos o seleccionándolos desde el equipo (para archivos de más de 160 GB se debe utilizar AWS CLI, SDK de AWS o la API REST).

Al seleccionar un objeto subido al bucket, este presenta cuatro identificadores clave en sus propiedades:

* **URI de S3** (ej. `s3://vjgr-bd-iessanandres/ventas_este.json`): es la dirección del objeto que entenderá el ecosistema interno de AWS (CLI, Glue, etc.).


* **ARN (Nombre de recurso de Amazon)** (ej. `arn:aws:s3:::vjgr-bd-iessanandres/ventas_este.json`): es el identificador único y oficial de cualquier recurso dentro de toda la infraestructura de AWS (no solo S3).


* **URL del objeto** (ej. `[https://vjgr-bd-iessanandres.s3.us-east-1.amazonaws.com/ventas_este.json](https://vjgr-bd-iessanandres.s3.us-east-1.amazonaws.com/ventas_este.json)`): es una dirección web estándar (HTTPS) para acceder al archivo desde un navegador.


* **ETag (Entity Tag):** es un identificador de versión del contenido del archivo.



**Permisos, acceso público y políticas de Bucket**

* Si se abre un archivo desde el botón **Abrir** de la consola de AWS, el navegador muestra el contenido porque genera una URL con una *query string* firmada (`?X-Amz-Algorithm=AWS4-HMAC-SHA256...`) que incluye información sobre los permisos del propio usuario autenticado.


* En cambio, si se copia y pega directamente la **URL del objeto** en un navegador para compartir el recurso, devuelve un error XML de `AccessDenied` porque dicha URL no lleva asociados esos permisos y, por defecto, los recursos en S3 no son públicos.



Para habilitar el acceso público a través de la URL del objeto (práctica que por seguridad general no debe hacerse salvo que sea estrictamente necesario), se requieren dos pasos:

1. Ir a la pestaña **Permisos** del bucket, editar la **Configuración de bloqueo de acceso público** y desmarcar el bloqueo.


2. Crear una **Política de bucket** en formato JSON (ya que solo desbloquear el acceso público sigue devolviendo `AccessDenied` si no existe una política que lo autorice):



```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Statement1",
      "Principal": "*",
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::vjgr-bd-iessanandres/*"
    }
  ]
}

```

**Elementos de la política JSON**:

* `Version`: versión del lenguaje de políticas de AWS; es un estándar y siempre debe ser la fecha `"2012-10-17"`.


* `Sid`: nombre descriptivo opcional para identificar el bloque de reglas.


* `Principal`: indica quién puede acceder; el asterisco (`"*"`) significa que cualquiera puede hacerlo sin requerir usuario ni contraseña.


* `Effect`: indica si la regla sirve para permitir (`"Allow"`) o denegar la acción.


* `Action`: especifica qué permite hacer la regla (en este caso `"s3:GetObject"` para lectura; existen otras opciones como `ListBucket` para ver el contenido del bucket).


* `Resource`: identificador ARN del bucket; el `/*` al final indica que la regla se aplica a todo el contenido dentro del bucket.



**Criterios de uso de políticas de Bucket**

* **¿Cuándo NO hace falta crear una política?**
* *Consola de AWS:* cuando se accede desde la interfaz web y se suben o bajan archivos manualmente.


* *AWS CLI / Python local:* cuando se utilizan las credenciales propias de *AWS Details* en el ordenador local.


* *Servicios con roles:* cuando se utiliza una función Lambda o un Job de Glue asignándoles el rol `LabRole` (el rol preconfigurado de AWS Academy).




* **¿Cuándo SÍ hay que crear una política?**
* *Acceso público:* cuando se requiere que un archivo sea descargable por cualquier persona mediante una URL pública.


* *Permisos entre cuentas:* cuando en un entorno real se necesita que un bucket de una cuenta A sea leído por una aplicación de una cuenta B.


* *Restricciones de red:* cuando se desea restringir el acceso al bucket únicamente desde una dirección IP específica.




