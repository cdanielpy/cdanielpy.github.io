# Logging Centralizado

## Parte 3: El Publicador de Eventos
[Parte 1: Presentación](../mongolog)<br/>
[Parte 2: Preparar el RabbitMQ](./rabbitconf)

<div align="center">
<img src="https://drive.google.com/uc?id=1VMny-HWh1Ez-ezZ1Q1UqBBhS9d-1AzIx" width="600" alt="dfd logger"/>
</div><br/>

### Aplicación de Origen de Eventos

> Si bien en el diagrama de flujo de datos las aplicaciones corren dentro de un _Tomcat_, aquí reutilizaremos un proyecto anterior para no tener que reescribir el proceso de desarrollo completo del mismo, y luego continuaremos con las modificaciones requeridas para poder enviar los eventos a la cola de mensajes en el RabbitMQ.

Recreamos el proyecto desarrollado en el ejercicio [Spring Boot + Web Services](https://cdanielpy.github.io/entradas/retail/ejercicios/primer-ejercicio), pero sólo la parte inicial, antes de empezar con las modificaciones propias de ese ejercicio.

#### Realizamos las modificaciones siguientes para este ejercicio:

1. En el archivo `pom.xml` agregamos las lineas siquientes:

   1. Dentro de las propiedades (etiqueta `<properties>`) indicaremos al _**Maven**_ que referenciaremos a la propiedad `maven.build.timestamp` a través de un alias `timestamp` y que tendrá el formato `yyyyMMdd.HHmm`:
   ```xml
      <timestamp>${maven.build.timestamp}</timestamp>
      <maven.build.timestamp.format>yyyyMMdd.HHmm</maven.build.timestamp.format>
   ```

   2. Dentro de las etiquetas `<build>` indicamos al _**Maven**_ que debe buscar referencias a las variables del sistemas dentro de los archivos de recursos de la aplicación:
   ```xml
      <resources>
          <resource>
              <directory>src/main/resources</directory>
              <filtering>true</filtering>
          </resource>
      </resources>
   ```


2. Agregamos los archivos `appplication.properties` y `logback.xml` dentro de la carpeta `scr\main\resources`.

   1. Si la carpeta no existe, la creamos seleccionando la carpeta `src`, hacemos click derecho encima y en el menú desplegado seguimos _**[New] > [Source Folder]**_ , escribimos la ruta _**src/main/resources**_ y hacemos click en _**[Finish]**_.

<div align="center">
<img src="https://drive.google.com/uc?id=1siYGhmwfxA0UNtitAbQHJqv2s29CNV2I" width="300" alt="new source folder menu"/>
<img src="https://drive.google.com/uc?id=1-ujUwU2iWYtGN7tjh67uFS1JySQRkI86" width="300" alt="new source folder wizzard"/>
</div><br/>

3. En el archivo `appplication.properties` copiamos y pegamos las líneas siguientes:


```properties
###############################################################################
# CONFIGURACION DEL LOGBACK
###############################################################################
# identificador de la hora de compilacion mediante maven (referenciando al elemento <timestamp> del archivo pom.xml)
logger.application.version = @timestamp@
#nombre del proyecto maven (referenciando al elemento <artifactId> del archivo pom.xml)
logger.application.id = @artifactId@

#patron de lineas de log a imprimir en consola
logger.patron.consola = %d{HH:mm:ss.SSS} [%thread] %green(%-5level) %yellow(%logger{35}) - %msg %n
#patron de lineas de log a imprimir en archivos
logger.patron.archivo = %d{HH:mm:ss.SSS} [%thread] %-5level %logger{35} - %msg %n

#parametros de host virtual que aloja tanto al 'exchange' y la cola de destino de los mensajes de eventos
logger.rabbitmq.virtual-host= /
logger.rabbitmq.routingKeyPattern = mensaje_de_log
logger.rabbitmq.declareExchange = true
logger.rabbitmq.exchangeType = direct
logger.rabbitmq.exchangeName = logger.exchange

#direccion del servidor y puerto de 'escucha'
logger.rabbitmq.host = rabbit-svr
logger.rabbitmq.port = 5672

#credenciales de acceso al rabbitmq (si el host virtual es diferente, tambien se debe conceder acceso al mismo)
logger.rabbitmq.username = guest
logger.rabbitmq.password = guestoo
```

3. En el archivo `logback.xml` copiamos y pegamos las líneas siguientes:


```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true">
    <!-- Archivo de carga de parametros extras -->
	<property resource="application.properties" />
    
    <include resource="org/springframework/boot/logging/logback/defaults.xml" />
    
    <!-- Parametros de salida a consola (salida estandar del sistema) -->
	<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<charset>UTF-8</charset>
			<pattern>${logger.application.version} > ${logger.patron.consola}
			</pattern>
		</encoder>
	</appender>
    <!-- Parametros de salida a archivos -->
	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<encoder>
			<pattern>${logger.application.version} > ${logger.patron.archivo}</pattern>
		</encoder>
		<file>./logs/current.out</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>./logs/%d{yyyy-MM-dd}.log</fileNamePattern>
			<maxHistory>30</maxHistory>
		</rollingPolicy>
	</appender>
	<!-- -->
	<root level="INFO">
		<appender-ref ref="FILE" />
        <appender-ref ref="CONSOLE" />
	</root>
</configuration>
```

##### Especial atención a las líneas siguientes:

```xml
<property resource="application.properties" />
```
Aquí se le indica al **Logback** que incluya como origen de parámetros utilizados en su configuración al archivo `application.properties`.

```xml
<pattern>${logger.application.version} > ${logger.patron.consola}</pattern>
```
En esta línea, se define el patrón a utilizar para la salida de las líneas de evento mediante la consola.

```xml
<pattern>${logger.application.version} > ${logger.patron.archivo}</pattern>
```
Y aquí se define el patrón a utilizar para la salida de las líneas de evento mediante archivos.


#### Probamos de vuelta nuestra aplicación

Antes de volver a probar la ejecución del programa, debemos hacer que _**Maven**_  _"asuma"_ los cambios realizados en la estructura del proyecto, para eso seguimos los pasos siguientes:

1. Seleccionamos la carpeta principal del proyecto y hacemos click derecho con el mouse encima.

2. Del menú desplegable seleccionamos la opción _**Run as > Maven build**_.

3. En el asistente que se abre, en el cuadro de texto _**[Goals]**_ ingresamos las siguientes palabras clave: _**"clean compile"**_, en el mismo orden.

4. Marcamos las opción  _**[  ] Resolve Workspace artifacts**_ y hacemos click en el botón _**[Run]**_.

5. Se inicia la compilación del proyecto.


Si terminó exitosamente, entonces procedemos a ejecutar de vuelta nuestro programa y tendríamos que ver la salida por consola parecida a esto:


```sh
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.0.5.RELEASE)

20181018.1530 > 14:01:58.695 [main] INFO  hello.Application - Starting Application on PY-WSSCONTIC13 with PID 13316 (E:\CDGS\PROYECTOS_JFP_VS\CDGS\gs-rest-service-complete\target\classes started by cgaray in E:\CDGS\PROYECTOS_JFP_VS\CDGS\gs-rest-service-complete) 
20181018.1530 > 14:01:58.697 [main] INFO  hello.Application - No active profile set, falling back to default profiles: default 
20181018.1530 > 14:01:58.726 [main] INFO  o.s.b.w.s.c.AnnotationConfigServletWebServerApplicationContext - Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@15c43bd9: startup date [Thu Oct 18 14:01:58 PYST 2018]; root of context hierarchy 
20181018.1530 > 14:01:59.420 [main] INFO  o.s.b.w.e.tomcat.TomcatWebServer - Tomcat initialized with port(s): 8080 (http) 
20181018.1530 > 14:01:59.435 [main] INFO  o.a.catalina.core.StandardService - Starting service [Tomcat] 
20181018.1530 > 14:01:59.435 [main] INFO  o.a.catalina.core.StandardEngine - Starting Servlet Engine: Apache Tomcat/8.5.34 
```

##### Especial atención al formato de cada línea:

```sh
20181018.1530 > 14:01:59.420 [main] INFO  o.s.b.w.e.tomcat.TomcatWebServer - Tomcat initialized with port(s): 8080 (http)
```

Empieza con la fecha y hora de compilación del programa en el formato que definimos en el archivo `pom.xml` en la propiedad `<maven.build.timestamp.format>` y sigue con los demás parámetros según la propiedad `logger.patron.consola`  del archivo `application.properties`.

##### Salida a archivo en disco

Hemos de notar que también se ha creado un archivo con el nombre `current.out` en el directorio `logs` en la misma carpeta raíz de nuestro proyecto. El mismo contiene las mismas líneas de eventos que se visualizan en la consola, a excepción del logo de _**Spring**_.

<div align="center">
<img src="https://drive.google.com/uc?id=1tp4exUYcc6yD-yK6oqDGj4-w3J4Wb5r2" alt="directorio logs"/>
</div><br/>


> _**Obs:**_ Es posible que la carpeta no se visualize a la primera ya que el _explorador de paquetes_ no se actualiza automáticamente, entonces probar actualizar el directorio del proyecto desde el _explorador de paquetes_ o bien seleccionar la carpeta del proyecto y presionar la tecla _**[F5].**_
>
> El archivo `current.out` se renombra cada vez que se registran eventos después de la media noche, cada día, y se vuelve a crear uno nuevo. Además se almacenan sólo los correspondientes a los últimos 30 días. Esto se configura en el archivo `logback.xml`.

----

En el siguiente capítulo, realizaremos los ajustes de configuración en el archivo `logback.xml` para que direccione los mensajes de eventos en notación `json` hacia el `exchange` del _**RabbitMQ**_.

### [Paso 3.1: Configurar el Logback](./logback)
