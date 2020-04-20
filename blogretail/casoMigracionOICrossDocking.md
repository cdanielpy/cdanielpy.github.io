# Interface de Ordenes de Ingreso de Cross Docking

<div align="center">
<img src="https://drive.google.com/uc?id=1WPIkS3Q23GXnYji-HFTAVcC9SYGVBrnv" alt="Diagramas de Flujos actual y alternativo."/></div>

## Intro
---

Actualmente las _**Ordenes de Ingreso de Cross Doking (OI CD)**_ y las _**Ordenes de Ingreso de Compra Local (OI CL)**_ se transfieren al _**WMS Block (WMS)**_ desde el _**EmeRetail(EME)**_ previo proceso de _Consolidación_ de los ítems a través del _**AJ EDI PC WMS**_, donde los operadores seleccionan los documentos de un determinado _Proveedor_ con destino a una _Sucursal_ específica, o Depósito para el caso de las **OI CL**.
La consolicación de los documentos es un proceso opcional cuya finalidad es reducir la cantidad de órdenes generadas en el lado del **WMS** cuando se trata de recibir mercaderías de un proveddor y no tener que separarlas físicamente por cada documento del lado del **ERP** ya que además de complejizar el proceso de recepción, lo retrasa innecesariamente.

## Procedimiento Actual
---

El proceso que el el conjunto de aplicaciones realiza cuando un operador indica los documentos a consolidar (o uno sólo de ellos) y transferir al **WMS** se ejecuta del siguiente modo (ver en la imagen principal):
1. Cuando el operador inicia el proceso de migración mediante el comando correspondiente, el **AJ EDI PC WMS** crea una nueva cabecera con los datos comunes de los elementos seleccionados (origen, destino, tipo, fecha actual, etc). Sí es un sólo elemento, toma los datos de ese único elemento.
2. Recorre los ítems de los documentos seleccionados y los agrupa por código de artículos sumando la cantidad a recibir de cada uno, una única cantidad por cada artículo diferente.
3. Remite los datos a un _servicio web_ del lado del **EME** que se encarga de asignarle un _número de documento_ y persistirlo en las tablas correspondientes.
4. El _servicio web_ retorna como resultado del proceso el _número de documento_ recién creado. Si hubiera ocurrido un error manejado retorna el mensaje con la descripción correspondiente.
5. Si no hubo errores en el paso anterior, el **AJ EDI PC WMS** invoca a un _procedimiento almacenado_ del lado de la _base de datos_ del **WMS**, pasando como parámetro el _número de documento_ recibido desde el **EME**.
6. El _procedimiento almacenado_ procede a ejecutar una consulta directamente a la _base de datos_ del **EME** a través de una consulta del tipo _**[OPENQUERY][openquery]**_, evalúa el resultado obtenido y si no hay errores procede a insertar los registros correspondientes en las tablas `i_ordenes_ingreso` e `i_ordenes_ingreso_items`.
7. Al final el _procedimiento almacenado_ el **AJ EDI PC WMS** es informado y muestra el listado de documentos procesados y el _número de documento_ asignado al proceso, de tal forma a que eso sirva al operador como referencia para consulta del mismo en las aplicaciones dentro del **WMS**.

> **Obs.:** Un paso final que no es parte del proceso en sí es el que los operadores del **WMS** deben ejecutar la _Importación de Ordenes de Trabajo_, para que los datos de las órdenes pasen de las tablas de interfaces (`i_ordenes_ingreso` e `i_ordenes_ingreso_items`) a las diferentes tablas transaccionales del sistema y a estar disponibles para su ejecución por parte de los operadores de las terminales móbiles.

## El Problema
---

<div align="center">
<img src="https://blog.teknicks.com/hs-fs/hub/233529/file-830520563-png/broken_links.png" width="500" alt="Enlace roto representado por una cadena rota"/>
</div>

La desventaja de este esquema de funcionamiento actual es que el enlace entre las bases de datos de ambos sistemas es muy delicado y tiene una alta dependencia de la comunicación entre los mismos a la hora de ejecutar la consulta remota (_**[OPENQUERY][openquery]**_) desde el **WMS** hacia el **EME**, con lo que cualquier interrupción que ocurriera a nivel de comunicación hace que el procedimiento almacenado _se cuelgue_ y no termine de ejecutarse.

El diseño de este proceso fue desarrollado cuando los servidores, tanto de aplicación como de base de datos, de ambos sistemas se encontraban _"físicamente"_ en el mismo lugar, por lo que la comunicación a nivel de redes era práticamente perfecta e ininterrupida, por lo que no presentaba problemas.

Ahora que ambos servidores se encuentran ubicados físicamente en diferentes ciudades, inclusive, y como nuestra infraestructura de redes de comunicación aún tiene mucho por mejorar, sumando a esto el hecho de que la carga de trabajo en el _**Centro de Distribución (CD)**_ se ha incrementado considerablemente, las probabilidades de que ocurran problemas también se ve incrementada y cuando ocurren afectan a la operación y las sucursales destino de las mercaderías.

## La Solución Alternativa
---

<div align="center"><img src="https://vishalgupta7982.files.wordpress.com/2017/11/amqp.jpg" width="500" alt="Esquema simple de intercambio de mensajes entre sistemas"/></div>
                                                                                                                                                  
La solución propuesta se basa en dos fundamentos que actualmente vengo implementando en mis desarrollos:
1. La especialización/simplificación de procesos, y
2. La ejecución asincrónica de tareas que no tienen dependencias directas. 

La primera consiste básicamente en evitar escribir procesos con demasiados pasos _anidados_ y que afecten al resultado general del mismo.

La segunda consiste en separar la ejecución de procesos que dependen entre sí pero que cuya ejecución fallida no requiera necesariamente deshacer los resultados de los procesos previos.

> _**Les pondría un ejemplo pero sería muy extenso y no viene al caso.**_

De lo anterior, lo que haré será elimnar el proceso de transferencia de los datos de la _**OI CD**_ a través del enlace entre las bases de datos de ambos sistemas (_**[OPENQUERY][openquery]**_ que corresponde a la línea roja) y pasar a ejecutarlo en un proceso de tarea programada a través del _[RabbitMQ](https://www.rabbitmq.com)_, con lo que ahora pasarían a ser dos procesos, dependientes siempre pero separados para el operador del _**AJ EDI PC WMS**_.

Los componentes a agregar al proceso, además del ya mencionado _**[RabbitMQ](https://www.rabbitmq.com)**_, serían el _**HEAVY WORKER**_ y el _**BLOCK - MQ**_, donde el primero hará el trabajo de monitorear los documentos pendientes de exportar al **WMS** y el segundo será el encargado de recibirlos e insertarlos en las tablas `i_ordenes_ingreso` e `i_ordenes_ingreso_items`.

> Este flujo ya está actualmente implementado con otros tipos de documentos, como las _**Ordenes de Egreso de Importaciones**_ o los datos del _**Maestro de Artículos**_, con lo que no se trataría de un desarrollo desde cero.

## La Implementación
---

<div align="center">
<img src="https://161cli18elctkuzva3yluzd6-wpengine.netdna-ssl.com/wp-content/uploads/2018/12/Relevant-Software-product-development-life-cycle.png" width="500" alt="Esquema simple de intercambio de mensajes entre sistemas"/>
</div>

Como el desarrollo ya lo tengo terminado y probado en el esquema de producción actual (=D) considero que está listo para implementarlo en reemplazo del proceso actual, pero para no interrumpir bruscamente el flujo de operaciones del _**CD**_ con la probabilidad de tener errores en los múltiples componentes del circuito actual, lo dividí en los siquientes pasos:
1. Modificar el proceso **[PERSISTIR OI CROSS DOCKING]** para incluir un número de versión del mismo para poder separar los documentos en _VISTAS_ diferentes (en la base de datos).
2. Reemplazar el **[BLOCK - MQ]** en el servidor **AJVBLOCK** con la nueva versión que incluye la recepción de datos de _**OI CD**_ recibidos a través del _**[RabbitMQ](https://www.rabbitmq.com)**_.
3. Reemplazar el **[HEAVY WORKER]** en el servidor **OLVRABBIT02** con la versión que incluye la tarea de exportación de _**OI CD**_ desde la base de datos del **EME** hacia la cola correspondiente en el _**[RabbitMQ](https://www.rabbitmq.com)**_ y con destino al **[BLOCK MQ]**. Los pasos 2 y 3 permitirán asegurarnos de que ambas versiones modificadas siguen funcionando normalmente con los procesos que venían implmentados antes del cambio.
4. Modificar el procedimiento **[PR_I_JOB_OICROSS_DOCKING]** para que en caso de ser invocado con un número de documento como parámetro, finalice exitosamente pero sin ejecutar la parte de la consulta de datos a la base de datos del _**EME**_. Con este paso eliminamos el proceso representado con la línea roja en la imagen principal.
5. Activar en el **[HEAVY WORKER]** la tarea programada de monitorear y exportar las _**OI CD**_ pendientes de notificar al **WMS**. Estos dos últimos pasos deberan hacerse en este mismo orden.
6. Hacer el seguimiento de la implementación y si hubieran problemas volver a la versión anterior del proceso.

>  Hacerlo de este modo me permitirá mantener los procesos actuales en caso de que deba de volver a activar la versión original del flujo.

[openquery]: https://www.sqlshack.com/es/consultando-fuentes-de-datos-remotas-en-sql-server/
