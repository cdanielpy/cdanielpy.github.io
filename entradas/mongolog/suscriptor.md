# Logging Centralizado

## Parte 4: El Suscriptor

[Parte 3.2: Logback a JSON](./logbackjson)

<div align="center">
<img src="https://drive.google.com/uc?id=1VMny-HWh1Ez-ezZ1Q1UqBBhS9d-1AzIx" width="600" alt="dfd logger"/>
</div><br/>

### Aplicación de Recepción de Eventos

Aquí crearemos una aplicación ya con todas las dependencias requeridas tanto para conectarnos al _**RabbitMQ**_ y a la base de datos _**MongoDB**_.

### Configuración del proyecto

Iniciamos un nuevo proyecto haciendo los pasos siguientes:

1. Desde el menú seleccionamos la opción_**[File] > [New] > [Spring Starter Project]**_ y completamos los datos requeridos (_nombre, grupo de proyectos maven, el identificador del artefacto, la descripción y el paquete base_) y le damos click en el botón _**[Next]**_.

2. En el siguiente grupo de opciones tenemos que indicar las dependencias de nuestro proyecto, agregamos las siguientes (_podemos valernos del filtro de búsqueda que incorpora_):
   - _**Lombok:**_ para agilizar la escritura de clases.
   - _**Web Services:**_ para realizar pruebas de la aplicación mediante nuestro navegador web.
   - _**RabbitMQ:**_ para conectarnos al servicio de colas de mensajes, y
   - _**MongoDB:**_ para conectarnos a la base de datos y persistir los mensajes recibidos.

3. Agregamos en el archivo `pom.xml` la dependencia a _**Joda**_ para simplificar el manejo de fechas.

   ```xml
   <dependency>
   	<groupId>joda-time</groupId>
   	<artifactId>joda-time</artifactId>
   </dependency>
   ```

4. Click en el botón _**[Next]**_ que nos lleva al resumen, y click en el botón _**[Finish]**_ para que empiece a generar remotamente el proyecto y luego importarlo a nuestro entorno de trabajo local.

<div align="center">
<img src="https://drive.google.com/uc?id=11PM1eBE_9doG_38J4_PkqRklnDn4drt4" width="200" alt="new project 0"/>
<img src="https://drive.google.com/uc?id=1pGwZHrvkipmrw67IT2MWqOkxpbh9GchH" width="200" alt="new project 1"/>	
<img src="https://drive.google.com/uc?id=1AU845mlBg26HCOTwSLGc73s0bpIdXaJx" width="200" alt="new project 2"/>
</div><br/>

#### Realizamos las modificaciones siguientes para este ejercicio:

Una vez que el proyecto se haya descargado completamente, lo primero siempre es probar su ejecución para asegurarnos de que sea el resultado correcto (_click derecho sobre la carpeta principal del proyecto y del menú seleccionamos **[Run As] > [Spring Boot App]**_).

El programa se ejecutará y quedará activo en el puerto `8080` pero mostrará una excepción relacionada con la conexión a la base de datos _**MongoDB**_, parecida a las líneas siguientes:

```sh
com.mongodb.MongoSocketOpenException: Exception opening socket
	at com.mongodb.connection.SocketStream.open(SocketStream.java:62) ~[mongodb-driver-core-3.6.4.jar:na]
	at com.mongodb.connection.InternalStreamConnection.open(InternalStreamConnection.java:126) ~[mongodb-driver-core-3.6.4.jar:na]
	at com.mongodb.connection.DefaultServerMonitor$ServerMonitorRunnable.run(DefaultServerMonitor.java:114) ~[mongodb-driver-core-3.6.4.jar:na]
	at java.lang.Thread.run(Thread.java:748) [na:1.8.0_152]
Caused by: java.net.ConnectException: Connection refused: connect
	at java.net.DualStackPlainSocketImpl.waitForConnect(Native Method) ~[na:1.8.0_152]
	at java.net.DualStackPlainSocketImpl.socketConnect(DualStackPlainSocketImpl.java:85) ~[na:1.8.0_152]
	at java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:350) ~[na:1.8.0_152]
	at java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:206) ~[na:1.8.0_152]
	at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:188) ~[na:1.8.0_152]
	at java.net.PlainSocketImpl.connect(PlainSocketImpl.java:172) ~[na:1.8.0_152]
	at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392) ~[na:1.8.0_152]
	at java.net.Socket.connect(Socket.java:589) ~[na:1.8.0_152]
	at com.mongodb.connection.SocketStreamHelper.initialize(SocketStreamHelper.java:59) ~[mongodb-driver-core-3.6.4.jar:na]
	at com.mongodb.connection.SocketStream.open(SocketStream.java:57) ~[mongodb-driver-core-3.6.4.jar:na]
	... 3 common frames omitted	
```

Detenemos la ejecución de la aplicación y realizaremos las configuraciones requeridas, parte por parte.

#### Configurar la conexión a MongoDB

En el archivo `application.properties` agregamos las líneas:

```properties
spring.data.mongodb.uri=mongodb://usuario:contrasena@servidor:27017/basedatos
```

##### Donde:

| Parámetro    | Valor/Descripción                                            |
| ------------ | ------------------------------------------------------------ |
| `usuario`    | ID de usuario de la base de datos.                           |
| `contrasena` | Clave de acceso.                                             |
| `servidor`   | Nombre o dirección IP del Servidor donde se aloja la base de datos. |
| `puerto`     | Puerto de "escucha" de conexiones. Por defecto suele ser 27017. |
| `basedatos`  | Nombre de la base de datos donde se alojan las colecciones de documentos a consultar/persisitir. |

y listo... si volvemos a ejecutar nuestro programa, y todo está correctamente configurado, ya no deberían de haber excepciones.

#### Configurar la conexión al RabbitMQ

En el archivo `application.properties` agregamos las líneas:

```
spring.rabbitmq.host= servidor
spring.rabbitmq.port=5672
spring.rabbitmq.virtual-host= /
spring.rabbitmq.username= usuario
spring.rabbitmq.password= contrasena
```

##### Donde:

| Parámetro                      | Valor/Descripción                                            |
| ------------------------------ | ------------------------------------------------------------ |
| `spring.rabbitmq.host`         | Nombre o dirección IP donde se encuentra instalado el RabbitMQ. |
| `spring.rabbitmq.virtual-host` | Nombre del host virtual donde se encuentra la `queue` de mensajes a consumir. El valor por defecto es "/" así que si no es obligatorio establecerlo. |
| `spring.rabbitmq.username`     | ID de usuario de conexión al servicio.                       |
| `spring.rabbitmq.password`     | Clave de acceso.                                             |
| `spring.rabbitmq.port`         | Puerto de "escucha" de conexiones. Por defecto suele ser 5672. |

Establecidos los parámetros, que pueden ser los mismos utilizados en la configuración del _**logback**_ en el [Paso 3.1](./logback), y probamos ejecutar de vuelta el programa para ver si es que todo esta correcto.

Si no hubieran inconvenientes, entonces podemos pasar al modelo de persistencia de nuestra aplicación.



### Diseño del modelo de datos

En esta sección, escribiremos las clases correspondientes a la capa de persistencia de nuestra aplicación, que no será demasiado complicada ya que sólo tendremos que guardar los mensajes de eventos recibidos, sin demasiadas restricciones.

#### Modelo de Entidad

Diseñamos una clase que tenga la misma estructura que los mensajes publicados en formato `json` y la llamaremos `LogLinea` y tendrá la estructura siguiente:

```java
import java.io.Serializable;
import java.util.Date;

import org.joda.time.DateTime;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.format.annotation.DateTimeFormat;
import org.springframework.format.annotation.DateTimeFormat.ISO;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.annotation.JsonProperty;

import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.experimental.Accessors;

/**
 * Clase que representa a un documento de la colección [LOG_LINEAS]
 * 
 * @author Cristian Garay :
 *         <a href='cdaniel.py@gmail.com'>cdaniel.py@gmail.com</a>
 *
 */
@Data
@NoArgsConstructor
@Accessors(chain = true)
@Document(collection = "log_lineas")
@JsonIgnoreProperties(ignoreUnknown = true)
public class LogLinea implements Serializable {

	private static final long serialVersionUID = 5840309249107044152L;

	@Id
	String id;

	@DateTimeFormat(iso = ISO.DATE_TIME)
	DateTime fechaHoraInsercion = DateTime.now();

	@JsonProperty("context")
	String contexto;
	
	@JsonProperty("timestamp")
	Date fechaHora;

	@JsonProperty("thread")
	String hilo;

	@JsonProperty("level")
	String nivel;

	@JsonProperty("logger")
	String clase;

	@JsonProperty("message")
	String mensaje;

}

```

Sin entrar en demasiados detalles, lo que tenemos es una clase _`POJO`_ con los mismos atributos que los mensajes publicados por el _logger_ en el otro extremo, con las anotaciones del proyecto _**Lombok**_ que nos ahorra tener que escribir, entre otras cosas los _"constructores" y "accesores" de la clase_. Las anotaciones de mapeo `json -> objeto` y viceversa.

#### Atención especial a las líneas:

| Línea                                                        | Descripción                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `@Document(collection = "log_lineas")`                       | Esta es como la homóloga de la anotación `@Table` de `JPA`, donde vinculamos la clase con la colección de datos en la base de datos _**MongoDB**_. |
| `@DateTimeFormat(iso = ISO.DATE_TIME) DateTime fechaHoraInsercion = DateTime.now();` | Éste es el único atributo que no tiene su par correspondiente en la estructura de los mensajes `json` y tiene como finalidad dejar registro de la creación del objeto java antes de su persistencia, que no necesariamente será el mismo que el valor del atributo `timestamp` del mensaje ya que normalmente siempre habrá un lapso tiempo desde que se produce el evento y se recibe y procesa en el otro extremo. |

#### Repositorio de Datos

A continuación, procedemos con el repositorio de la colección, que será la que utilizaremos para interactuar con la misma.

Creamos la interface `LogsLineasRepositorio` que extienda de `MongoRepository` y le indicamos que los parámetros de la misma son la clase `LogLinea` y su clave primaria es de tipo `String`.

```java
import org.springframework.data.mongodb.repository.MongoRepository;

/**
 * Administrador de operaciones sobre datos de la colección [LOG_LINEAS]
 * 
 * @author Cristian Garay : <a href='cdaniel.py@gmail.com'>cdaniel.py@gmail.com</a>
 *
 */
public interface LogsLineasRepositorio extends MongoRepository<LogLinea, String> {

}
```

Sin más, ya está lista... probamos ejecutar nuestro programa para verificar que todo continúe en orden, y si no hay inconvenientes, pasamos a la sección del cliente del _**RabbitMQ**_.

### Diseño del Consumidor de Mensajes del RabbitMQ

Necesitaremos dos clases, una para la que se encargará de procesar los mensajes recibidos y otra para conectarse al _**RabbitMQ**_.



#### El Procesador de Mensajes

Escribimos la clase `ProcesadorMensajes` que hará el trabajo pesado de procesar los mensajes entrantes, convertirlos a objetos java y persistirlos a la base de datos.

```java
import java.util.Objects;

import org.springframework.stereotype.Component;

import com.fasterxml.jackson.databind.ObjectMapper;

import lombok.val;
import lombok.extern.slf4j.Slf4j;

/**
 * Clase de implementación de métodos de procesamiento de Mensajes de Eventos de
 * Logging
 * 
 * @author Cristian Garay :
 *         <a href='cdaniel.py@gmail.com'>cdaniel.py@gmail.com</a>
 *
 */
@Slf4j
@Component
public class ProcesadorMensajes {

	final ObjectMapper serializadorJson = new ObjectMapper();

	/**
	 * Método de procesamiento y persistencia de mensajes
	 * 
	 * @param mensaje
	 *            Instancia de la cadena de texto con el mensaje a procesar
	 */
	public void procesar(String mensaje) {
		// si no hay mensaje, salimos
		if (Objects.isNull(mensaje) || mensaje.trim().length() == 0) return;
	}
}

```

Notemos que el único método de la clase, recibe como parámetro una cadena de texto, que será la que contenga el mensaje _"en bruto"_ y deberemos deserializar y mostrar en la consola de la aplicación (o donde se defina la salida de eventos de logging de ésta aplicación).



#### El Consumidor de Mensajes

Esta vez no tendremos que escribir clase alguna sino simplemente modificaremos la clase principal de nuestra aplicación (`MongologSuscriptorApplication`) para incluir los _"beans"_ necesarios que conecten con el _**RabbitMQ**_ a través de la `queue` que habíamos definido, se suscriban a la misma, recibian una copia de los mensajes que ésten _encolados_ y los derive al método `procesar` de nuestra clase `ProcesadorMensajes`. 

```java
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.connection.ConnectionFactory;
import org.springframework.amqp.rabbit.listener.SimpleMessageListenerContainer;
import org.springframework.amqp.rabbit.listener.adapter.MessageListenerAdapter;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class MongologSuscriptorApplication {

	public static void main(String[] args) {
		SpringApplication.run(MongologSuscriptorApplication.class, args);
	}

	static final String queueName = "logger_input_queue";

	@Bean
	Queue queue() {
		return new Queue(queueName, true);
	}

	@Bean
	SimpleMessageListenerContainer container(ConnectionFactory connectionFactory,
			MessageListenerAdapter listenerAdapter) {
		SimpleMessageListenerContainer container = new SimpleMessageListenerContainer();
		container.setConnectionFactory(connectionFactory);
		container.setQueueNames(queueName);
		container.setMessageListener(listenerAdapter);
		return container;
	}

	@Bean
	MessageListenerAdapter listenerAdapter(ProcesadorMensajes procesador) {
		return new MessageListenerAdapter(procesador, "procesar");
	}
}
```

El conector en 3 pasos:

| @Bean             | Clase                          | Función                                                      |
| ----------------- | ------------------------------ | ------------------------------------------------------------ |
| `queue`           | Queue                          | Contener los datos de la cola de mensajes a _"escuchar"_.    |
| `container`       | SimpleMessageListenerContainer | Vincular la conexión que el framework crea con su proceso _"escucha"_. |
| `listenerAdapter` | MessageListenerAdapter         | Indicar la clase y el método al que se derivarán los mensajes recibidos. |

#### Probando lo hecho

Para probar el circuito de descarga de mensajes desde el _**RabbitMQ**_ y sin perder los mensajes que actualmente estan pendientes de descarga, podemos ejecutar nuestra aplicación en modo de depuración, estableciendo un punto de parada en la línea `if (Objects.isNull(mensaje) ...` de la clase `ProcesadorMensajes`.

Ejecutamos la depuración y si todo esta correctamente configurado se tendría que poder ver que el valor de la variable `mensaje` es igual al primer mensaje que teníamos en la `queue`.

<div align="center">
<img src="https://drive.google.com/uc?id=1Ijc243bwpTfoTU4cEIXpRpXlYxmeoLqP" width="600" alt="run debugging 0"/>
</div><br/>

### Persistiendo los Eventos

Ahora, ya teniendo todo el circuito  desde el origen hasta nuestro métod de procesamiento en el consumidor, sólo falta persistir los datos en la base de datos. Para esto, sólo se deben realizar unas líneas de modificaciones a la clase `ProcesadorMensajes` para que quede como sigue:

```java
import java.util.Objects;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import com.fasterxml.jackson.databind.ObjectMapper;

import lombok.val;
import lombok.extern.slf4j.Slf4j;

/**
 * Clase de implementación de métodos de procesamiento de Mensajes de Eventos de
 * Logging
 * 
 * @author Cristian Garay :
 *         <a href='cdaniel.py@gmail.com'>cdaniel.py@gmail.com</a>
 *
 */
@Slf4j
@Component
public class ProcesadorMensajes  {

	final ObjectMapper serializadorJson = new ObjectMapper();

	@Autowired
	LogsLineasRepositorio repositorio;
	
	/**
	 * Método de procesamiento y persistencia de mensajes
	 * 
	 * @param mensaje
	 *            Instancia de la cadena de texto con el mensaje a procesar
	 */
	public void procesar(String mensaje) {

		// si no hay mensaje, salimos
		if (Objects.isNull(mensaje) || mensaje.trim().length() == 0)
			return;

		// evaluamos si el mensaje se puede deserializar
		try {
			val eventoLog = serializadorJson.readValue(mensaje, LogLinea.class);

			log.info("Mensaje Recibido: {}", eventoLog);

			this.repositorio.save(eventoLog);
			
		} catch (Exception e) {
			log.error(e.getLocalizedMessage(), e);
		}
	}
}
```

Hasta aquí tenemos todo listo para que los mensajes recibidos sean insertados en la colección correspondiente.

> Recomendaciones: Cuando ejecutamos por primera vez cada modificación realizada, lo ideal es que lo hagamos estableciendo puntos de paradas en las líneas críticas de nuestro código y ejecutándolo en modo de depuración, para verificar que todo esté fluyedo según lo planeado y tener la posibilidad de cortar la ejecución si fuere necesario.

Aquí lo que corresponde corroborar es que el mensaje se puede convertir de la cadena `json` a objeto java

``````java
val eventoLog = serializadorJson.readValue(mensaje, LogLinea.class);
``````

si pasa de este punto, entonces es que tenemos una alta probabilidad de que el resto del programa se ejecute exitosamente.

<div align="center">
<img src="https://drive.google.com/uc?id=1Fzx7RGTx17SBXI1-md4lYAu_gGRPoJWX" width="600" alt="run debugging 1"/>
</div><br/>

#### Verificamos los datos 

Utilizando nuestro cliente de exploración de datos de _**MongoDB**_, si ejecutamos el método `find()` , deberíamos tenes como resultado todos los documentos correspondientes a las instancias de mensajes descargados y procesados por nuestra aplicación.

<div align="center">
<img src="https://drive.google.com/uc?id=1CYqhXiMpi5OBe1-UrMYOGnpyawMvAuME" width="600" alt="messages docs"/>
</div><br/>

Notemos que la estructura de los mensajes se conserva tal cual y que el modelo de persistencia le agregó dos atributos en forma automática:

| Atributo | Descripción                                                  |
| -------- | ------------------------------------------------------------ |
| `_id`    | Es el identificador del documento, que lo calcula el mismo _**MongoDB**_. Haciendo homología con una tabla `SQL` sería como la clave primaria de la tabla. |
| `_class` | Esto lo inyecta el componente del _framework_ para poder manejar el proceso de _de/serializacion_ del documento |



Si miramos la `queue` en el _**RabbitMQ**_ tendriamos que ver que los mensajes fueron consumidos en su totalidad.

<div align="center">
<img src="https://drive.google.com/uc?id=1meYvNg9n8x1AAF9A0dnANj7HMsZuF6NU" width="400" alt="rabbitmq queue empty"/>
</div><br/>

----

### Felicidades!

A lo largo de este tutorial hemos hecho el circuito desde el origen de los eventos de nuestra aplicación de pruebas hasta su persistencia en la base de datos _**MongoDB**_!

De aquí en más sólo quedaría una aplicación cliente que facilite la tarea de consultar y visualizar los datos desde la colección en algún monitor de aplicaciones.

También se podrían agregar funcionalidades como por ejemplo el envío de notificaciones a direcciones de correo o como mensajes al teléfono móvil para alertar de eventos críticos, como los de nivel _`ERROR`_. Nuestra imaginación es el límite.

----

## Bonus:

**Imaginemos el contexto siguiente:**

Tenemos varias aplicaciones distribuidas en diferentes servidores y queremos centralizar los eventos de todas en una misma colección de documentos en la misma base de datos.

Cómo identificaríamos a que aplicación corresponde cáda mensaje, sin tener que evaluar el nombre de los paquetes que figuran en el atributo `logger` de los mismos, además de que las clases se pueden compartir entre proyectos (sino de que serviría la _POO_?).

Entonces lo que necesitamos es agregar a cada mensaje de evento **el nombre de la aplicación, la versión y el nombre (o dirección) del equipo** donde se está ejecutando. Esto te lo dejo en el siguiente enlace:

### [Bonus: Identificar el Origen de cada Evento](./bonus)
