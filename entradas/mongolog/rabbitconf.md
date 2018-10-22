# Logging Centralizado

## Parte 2: Preparar el RabbitMQ
[Parte 1: Presentación](../mongolog)

Continuando con el desarrollo de nuestro proyecto de centralización de eventos de logging, debemos comenzar a preparar el entorno, empezando por el que hará el transporte de los datos.

> Nos saltaremos la instalación y configuración del [RabbitMQ](https://www.rabbitmq.com/download.html) y del [MongoDB](https://www.mongodb.com/download-center?initial=true#community), cuyos sitios web contienen suficiente información acerca de dichos procesos.

### Configuración de requerimientos en el RabbitMQ

Necesitamos crear las configuraciones necesarias para que la pueda recibir los datos enviados por nuestra aplicación:

Un `exchange` exclusivo, para no interferir con los procesos del `exchange` "principal".

Un `binding` de los mensajes entrantes al `exchange` hacia una `queue` de mensajes, y

Una `queue` de destino de los mensajes.



#### El _"exchange"_ receptor

Estando en la seccion _**[Add a new exchange]**_, de la pestaña _**[Exchanges]**_ del **RabbitMQ** seguimos los pasos siguientes:

1. El host virtual lo dejamos en _**"/"**_, que es el host por defecto (salvo otras preferencias).
2. Ingresamos el nombre _**"logger.exchange"**_ (puede ser cualquiera).
3. Debe ser de tipo _**"direct"**_.
4. Los demás valores los dejamos en su configuración por defecto.
5. Click en el botón _**[Add exchange]**_.

<div align="center">
<img src="https://drive.google.com/uc?id=1swqnk0zD2vmYeCTJVDqAif9cP9nNUVAT" width="300" alt="new exchange"/>
<img src="https://drive.google.com/uc?id=1m3NOb3yjVOITTx0_fP5NFdHWej66SnaS" width="300" alt="exchange list"/>
</div><br/>


#### La _"queue"_ de destino

Estando en la seccion _**[Add a new queue]**_, de la pestaña _**[Queues]**_ del **RabbitMQ** seguimos los pasos siguientes:

1. El host virtual lo dejamos en _**"/"**_, que es el host por defecto (salvo otras preferencias).

2. Ingresamos el nombre _**"logger_input_queue"**_ (también puede ser cualquiera).

3. Los demás valores los dejamos en su configuración por defecto.

4. Click en el botón _**[Add queue]**_.

<div align="center">
<img src="https://drive.google.com/uc?id=14ORc8cTX1oirrzBg4TYmfTu_mD7A5hDL" width="300" alt="new queue"/>
</div>


#### El _"binding"_ de los mensajes

Ahora, debemos indicar al `exchange` cómo direccionar a la `queue` de salida los mensajes que recibe. Devuelta en la pestaña _**[Exchanges]**_ del **RabbitMQ** seguimos los pasos siguientes:

Click en el `exchange` _**"logger.exchange"**_, que nos abriá la página de administración del mismo.

En la sección _**[Bindings]**_,  específicamente en la sub sección _**[Add binding from this exchange]**_:

Indicamos que los mensajes deben direccionarse a la `queue` ( _**[To queue]**_ ) _**"logger_input_queue"**_, y

La _**[Routing key]**_ será _**"mensaje_de_log"**_

Click en el botón _**[Bind]**_.

<div align="center">
<img src="https://drive.google.com/uc?id=1O6OYZrF3im-uQs-5CaY7hom9GvIeuAWE" width="300" alt="new bingind"/>
<img src="https://drive.google.com/uc?id=1m3NOb3yjVOITTx0_fP5NFdHWej66SnaS" width="300" alt="bindings list"/>
</div>



> Con este paso, lo que hacemos es indicar al RabbitMQ, que los mensajes que lleguen al `exchange` _**"logger.exchange"**_ y tengan la `routing key` _**"mensaje_de_log"**_ serán derivados a la `queue` _**"logger_input_queue"**_.

> Los mensajes que no tengan esa clave de ruteo, serán directamente eliminados, as;i que mucho cuidado con esto.
<br/><br/>


#### Prueba del circuito en el RabbitMQ

Para probar que lo realizado en los pasos anteriores funciona, podemos publicar un mensaje de pruebas en nuestro nuevo `exchange` y verificar que esté llegando a la `queue` correctamente.

Estando en la sección ***[Publish message]***, dentro de la página de propiedades del `exchange` _**"logger.exchange"**_ seguimos estos pasos:

En el campo _**[Routing key]**_, ingresamos nuestra clave de ruteo del mensaje _**"mensaje_de_log"**_.

En el campo _**[Payload]**_, escribimos nuestro mensaje de pruebas _**"Hola logger!"**_, y

Click en el botón _**[Publish message]**_.

Si todo está correctmanete configurado, tendriamos que ver que hay un mensaje listo en la `queue` _**"logger_input_queue"**_. 

Si no es así, debemos de revisar qué es lo que se nos pasó por alto de los pasos anteriores.

<div align="center">
<img src="https://drive.google.com/uc?id=14Ab8Z45_AHOyeLpwJUXTxheiTx_h-ero" width="300" alt="new bingind message"/>
<img src="https://drive.google.com/uc?id=1BiogoxC8pTSNUAX-in69d9k6yKug8oB8" width="300" alt="message ready"/>
</div>

### [Paso 3: El Publicador de Eventos](./publicador)
