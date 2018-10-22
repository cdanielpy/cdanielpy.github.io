
# Logging Centralizado

## Parte 2.1: Configurar el Logback
[Parte 2: El Publicador de Eventos](./publicador)

<div align="center">
<img src="https://drive.google.com/uc?id=1AocSmYP20Lu3o0jIai-O3N2dCkwzRMl2" alt="logback logo"/></div><br/>

### Canalizacíon de eventos hacia el RabbitMQ

En esta parte de la historia, lo que haremos es que el _**Logback**_ derivé los mensajes de eventos de logging hacia el `exchange` _**"logger.exchange"**_, de donde luego será redireccionado a la cola correspondiente se acuerdo a la clave de ruteo que le pasemos como parámetro.

#### Adición de dependencias al proyecto

Debemos agregar una dependencia clave al proyecto que será la encargada de gestionar la comunicación con el _**RabbitMQ**_  y envíar las cadenas de eventos al mismo, para ello, localizamos el archivo `pom.xml` y agregamos en el bloque de `<dependencies>` la siguiente:

```xml
<!-- Integracion AMQP -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

1. Actualizamos el proyecto _**Maven**_:

2. Seleccionamos la carpeta del mismo y hacemos click derecho encima de la misma.

3. En el ítem de menú desplegado seleccionamos _**[Maven] > [Update project...]**_

4. Marcamos nuestro proyecto y click en el botón _**[OK]**_ y esperamos a que el proceso de actualización de dependencias finalice.


<div align="center">
<img src="https://drive.google.com/uc?id=1bFeUY_U30P6E9cjMy--FzAJ5N-4wchbI" width="300" alt="logback logo"/>
<img src="https://drive.google.com/uc?id=1FAC7zFUJwmoU78F_xdUbVatFaxpKVdxZ" width="300" alt="logback logo"/></div><br/>



#### Adición del "appender" al `logback.xml`

Cuando se hayan actualizado las dependencias del proyecto, procedemos a agregar el _**"appender"**_ al archivo configuración del _**Logback**_ para que envíe los mensajes de eventos al _**RabbitMQ**_.

##### Procedemos de la siguiente manera:

Abrimos el archivo `logback.xml` y a continuación del elemento `<configuration scan="true">` agregamos la línea siguiente:

```xml
<property resource="application.properties" />
```

Con este paso estamos indicando que se ben buscar los parámetros de configuración en el archivo `application.properties` para completar los valores de la configuración.

Entre la última etiqueta `</appender>` y antes de `<root level="INFO">`, agregamos:

```xml
<appender name="AMQP"class="org.springframework.amqp.rabbit.logback.AmqpAppender">
	<layout>
		<encoder>
			<pattern>${logger.application.version} > ${logger.patron.archivo}</pattern>
		</encoder>
	</layout>
	<!-- RabbitMQ connection -->
	<host>${logger.rabbitmq.host}</host>
	<port>${logger.rabbitmq.port}</port>
	<username>${logger.rabbitmq.username}</username>
	<password>${logger.rabbitmq.password}</password>
	
	<applicationId>{logger.application.id}</applicationId>
	<virtualHost>${logger.rabbitmq.virtual-host}</virtualHost>

	<routingKeyPattern>${logger.rabbitmq.routingKeyPattern}
	</routingKeyPattern>
	<declareExchange>${logger.rabbitmq.declareExchange}</declareExchange>
	<exchangeType>${logger.rabbitmq.exchangeType}</exchangeType>
	<exchangeName>${logger.rabbitmq.exchangeName}</exchangeName>
	<generateId>true</generateId>
	<charset>UTF-8</charset>
	<durable>true</durable>
	<deliveryMode>PERSISTENT</deliveryMode>
</appender>
```

> Está demás decir que después de cada cambio realizado a un archivo debemos guardarlos.

luego, dentro del elemento `<root level="INFO">` agregamos la referencia al nuevo _"appender"_:

```xml
<appender-ref ref="AMQP" />
```

con lo que debería quedar así:

```xml
<root level="INFO">
	<appender-ref ref="FILE" />
	<appender-ref ref="CONSOLE" />
	<appender-ref ref="AMQP" />
</root>
```

En esta última parte, le decimos al _**Logback**_ que lo tenga en cuenta para informar los eventos que ocurren durante la ejecución del programa.

##### Probamos los cambios hechos

Ahora toca probar lo hecho, ejecutamos nuestro programa (Click derecho sobre la carpeta raíz del proyecto, _**[Run As] > [Spring Boot App]**_).

Deberíamos estar viendo la salida en consola normalmente, y si nos fijamos en la cola de destino de los mensajes (_**"logger_input_queue"**_) en el _**RabbitMQ**_, deberíamos ver que la cantidad de mensajes listos se ha disparado hacia arriba.

<div align="center">
<img src="https://drive.google.com/uc?id=1WDwb3dXq9_Pwhghyq7wL5S5oknU6dENl" alt="logs messages"/></div><br/>

Si listamos alguno de los mensajes, tendríamos que ver que tienen exactamente el mismo formato y contenido que lo que se escribe en la consola y en el archivo `current.out`, puesto que hemos especificado que utilizarán el mismo patrón (`${logger.application.version} > ${logger.patron.archivo}`).

<div align="center">
<img src="https://drive.google.com/uc?id=1YNEWlwJBZ0dg3ivLV6z5F_gwFcjwzjLJ" width="400" alt="logs messages view"/>
<img src="https://drive.google.com/open?id=1gZ42TnOGU4lcoSLaZ4uyFC0XILZAIu2J" width="400" alt="publisher console"/>
</div><br/>

> Obs.: Sí surgen inconvenientes con la conexión entre el  _**"appender AMQP"**_ y el _**RabbitMQ**_, se mostrá la notificación correspondiente tanto en la consola como en el archivo `current.out`, pero no impedirá que el programa se ejecute con normalidad.

Si nos fijamos, las cadenas de mensajes de eventos llegan como texto plano y para poder procesarlas y persistirlas en la base de datos, necesitamos que cumplan algunas condiciones, como por ejemplo, una notación específica para poder parsearlas. Aqquí podríamos utilizar la vieja confiable `XML` o el siempre ligero `JSON`. 

Para este ejemplo utilizaremos `JSON` debido a que se puede persistir como tal directamente en la base de datos _**MongoDB**_.

----

En el siguiente paso, haremos las configuraciones necesarias al _**appender AMQP**_ para que genere los mensajes de e

### [Parte 2.2: Logback a JSON](./logbackjson)
