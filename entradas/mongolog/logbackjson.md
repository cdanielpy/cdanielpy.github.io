# Logging Centralizado

## Parte 2.2: Logback a JSON

[Parte 2.1: Configurar el Logback](./logback)

<div align="center">
<img src="https://drive.google.com/uc?id=1AocSmYP20Lu3o0jIai-O3N2dCkwzRMl2" alt="logback logo" width=""/>
<img src="https://drive.google.com/uc?id=1gIX_QEYZFQTYoAlCS6Ab9XlkERxFStUS" alt="logback logo" width=""/></div><br/>

### Conversión de Cadenas de Eventos a Notación `JSON`

En el paso anterior pudimos hacer que los mensajes de eventos de logging registrados en nuestra aplicación llegaran hasta la `queue` de mensajes en el _**RabbitMQ**_, pero vimos que el formato actual no ayuda a su procesamiento ya que, a psar de tener un formato establecido por nosotros, el procesamiento del mismo puede no ser tan fácil como si fuera, por ejemplo, una cadena `xml` o `json`, donde los valores de atributos estuvieran claramente definidas.

En esta parte, lo que haremos será algo muy sencillo y consisitirá básicamente en agregar unas dependencias que nos faciliten el trabajo.


### Agregando las dependencias requeridas

Afortunadamente alguien más ya vio la misma necesidad antes que nosotros y los _geniolitos_ del proyecto _**Logback**_ se dedicaron a desarrollar las dependencias que la cubrieran y la compartieron con el mundo mediante las extensiones `logback-json-classic` y `logback-jackson` que permiten que los mensajes de eventos se formateen en notación `json` y se escriban así en la salida establecida en el _appender_ donde se utilice (_consola, archivo, etc_).

Abrimos nuestro archivo `pom.xml` y agregamos como otras dependencias más las siguientes:

```xml
<dependency>
    <groupId>ch.qos.logback.contrib</groupId>
    <artifactId>logback-json-classic</artifactId>
    <version>0.1.5</version>
</dependency>
<dependency>
	<groupId>ch.qos.logback.contrib</groupId>
	<artifactId>logback-jackson</artifactId>
	<version>0.1.5</version>
</dependency>
```

Actualizamos el proyecto (click derecho sobre la carpeta principal y del menú, la opción _**[Maven] > [Update project...]**_ o bien la combinación de teclas _**[Alt] + [F5]**_) y luego click en el botón _**[OK]**_.



### Modificando nuestro "appender" AMQP

Después de agregar la extensión que formatea los mensajes de eventos a notación `json`, entonces debemos decirle a nuestro _appender_ que lo utilice para el registro de eventos.

Para esto,  realizamos las modificaciones siguientes el archivo `logback.xml`:

Al elemento `layout` le agregamos el valor de atributo `class="ch.qos.logback.contrib.json.classic.JsonLayout"`, con lo que le indicamos que utilizará dicha clase para gestionar los eventos y alojar sus atributos.

Removemos el elemento `pattern` y en su lugar ingresamos el elemento `<jsonFormatter class="ch.qos.logback.contrib.jackson.JacksonJsonFormatter"/>` que se encarga de darle el formato de impresión como una cadena `json`.

Nuestro _appender_ debería quedar así:

```xml
...
<appender name="AMQP" class="org.springframework.amqp.rabbit.logback.AmqpAppender">
	<layout class="ch.qos.logback.contrib.json.classic.JsonLayout">
		<jsonFormatter class="ch.qos.logback.contrib.jackson.JacksonJsonFormatter"/>
	</layout>
...
```

##### Probamos los cambios hechos

Ahora toca probar lo hecho, ejecutamos nuestro programa (Click derecho sobre la carpeta raíz del proyecto, _**[Run As] > [Spring Boot App]**_).



> _**Obs:**_ Antes de cada ejecución, lo ideal es _**purgar**_ la cola de mensajes para que sea más fácil la visualización de los mismos, al menos hasta que tengamos lista la aplicación suscriptora, ya que con cada ejecución el programa envía los eventos a la cola, y ésta va acumulando los datos.



Si miramos de vuelta los mensajes en la cola veremos que ahora son cadenas `json`:

<div align="center">
<img src="https://drive.google.com/uc?id=1sciLww0xWMH0L7095OfAZDLraKMWlHhp" alt="rabbit json logs" width="600"/></div><br/>

Cada mensaje en la cola (`queue`) del _**RabbitMQ**_ tiene la siguiente estructura:

```json
{
"timestamp":"1540047141391",
"level":"INFO",
"thread":"main",
"logger":"hello.Application",
"message":"Started Application in 1.58 seconds (JVM running for 2.265)",
"context":"default"
}
```

##### Donde:

| Atributo    | Tipo     | Descripción                                        |
| ----------- | -------- | -------------------------------------------------- |
| `timestamp` | `String` | Es la fecha y hora del evento en notación _epoch_. |
| `level`     | `String` | El nivel del evento.                               |
| `thread`    | `String` | El hilo de ejecución en que se produjo.            |
| `logger`    | `String` | La clase que lo disparó.                           |
| `message`   | `String` | El mensaje de evento, propiamente dicho.           |
| `context`   | `String` | El contexto del _logger_ que lo registró.          |

Con éstos datos ya tenemos básicamente lo necesario para hacerle seguimiento a los eventos registrados tanto por las clases de las que dependen nuestras aplicaciones como también los eventos personalizados que le agreguemos, casí como si lo estuvieramos viendo en la misma consola de nuestra aplicación en ejecución.



----

Hasta aquí tenemos ya los mensajes de eventos listos para ser procesados por la _**aplicación "suscriptora"**_, que es lo que desarrollaremos en el siquiente paso.

### [Parte 3: El Suscriptor](./suscriptor)
