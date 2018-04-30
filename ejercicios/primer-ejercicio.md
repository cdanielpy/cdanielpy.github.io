# Primer Ejercicio: Spring Boot + Web Services
<img src="https://i0.wp.com/mydevgeek.com/wp-content/uploads/2017/02/spring-boot-project-logo.png" width="150" alt="logo spring boot"/>

Aún en desarrollo..

## Inicio ##
Para este mini tutorial se verá cómo implementar una aplicación de servicios web de tipo REST(ful), básica, valiéndonos de una aplicación modelo provista por la guía del la página del [Spring Framework](http://spring.io/guides) y luego se ampliarán sus capacidades para poder acceder a los registros de una base de datos local.

### Porqué Spring Boot? ###
- Desarrollo rápido
- Múltiples dependencias manejadas por nosotros (en lugar de nosotros)
- No requiere configuración XML (aunque puede)
- Aplicaciones auto-contenidas
- Framework Maduro
- Bien documentado


## Nuevo proyecto Spring Boot ##

### 1- Crear el proyecto 

- Desde el menú **Archivo/File** del STS, seleccionar **[File] -> [new] -> [Spring Boot] -> [Import Spring Getting Started Content]**.

- Seleccionar el proyecto **"Rest Service"**, marcar la opción **[Maven]** del conjunto "**Build Type**" y del conjunto "**Code Sets**", la opción **[complete]**.

- Cuando haya finalizado la descarga de los archivos requeridos, se haya generado la estructura de directorios y haya construido/compilado el proyecto por primera vez, nos aparecerá el proyecto listo para ejecutar y probar el servicio web de ejemplo que trae por defecto y la estructura del mismo será como en la imagen de a continuación:

  _**Img 1.01:** Estructura inicial del primer ejercicio._

![](http://drive.google.com/uc?export=view&id=1LWqvXz9CQQIWqAe-sL99O0RP8SA4376R)

  ​

### 2- Resolver las dependencias del proyecto ###

En este paso lo que hacemos es asegurarnos de que las dependencias del proyecto estén descargadas a nuestro repositorio local (en nuestro equipo), a modo de que puedan ser referenciados por el programa que estaremos preparando.

- Click derecho sobre el proyecto, del menú contextual seleccionar las opciones **[Maven] -> [Update project...]**.
- Si están actualizadas y/o se resolvieron todas las dependencias, podemos pasar al siquiente punto.

### 3- Probar el proyecto base

- Click derecho sobre el nodo principal del proyecto, del menú contextual, seleccionar las opciones **[Run As] -> [Spring Boot App]**.

- Si no hay inconveniente, deberíamos ver en la consola algo parecido a esto:

  ```shell
    .   ____          _            __ _ _
   /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
  ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
   \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
    '  |____| .__|_| |_|_| |_\__, | / / / /
   =========|_|==============|___/=/_/_/_/
   :: Spring Boot ::        (v2.0.1.RELEASE)

  2018-04-27 17:19:28.294  INFO 17464 --- [           main] hello.Application                        : Starting Application on PY-WSSCONTIC13 with PID 17464 (E:\CDGS\PROYECTOS_JFP_VS\CAPACITACIONES\gs-rest-service-complete\target\classes started by cgaray in E:\CDGS\PROYECTOS_JFP_VS\CAPACITACIONES\gs-rest-service-complete)
  2018-04-27 17:19:28.294  INFO 17464 --- [           main] hello.Application                        : No active profile set, falling back to default profiles: default
  2018-04-27 17:19:28.354  INFO 17464 --- [           main] ConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6156496: startup date [Fri Apr 27 17:19:28 PYT 2018]; root of context hierarchy
  2018-04-27 17:19:28.962  INFO 17464 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
  2018-04-27 17:19:28.977  INFO 17464 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
  2018-04-27 17:19:28.977  INFO 17464 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.29
  2018-04-27 17:19:28.982  INFO 17464 --- [ost-startStop-1] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [C:\Program Files\Java\jdk1.8.0_152\bin;C:\Windows\Sun\Java\bin;C:\Windows\system32;C:\Windows;c:\oracle\instantclient_11_2;E:\CDGS\no_instalables\gradle\gradle-4.0.1\bin;C:\app\Administrador\product\11.2.0\client_1;C:\app\Administrador\product\11.2.0\client_1\bin;C:\Python36\Scripts\;C:\Python36\;e:\CDGS\no_instalables\maven-3.3.3\bin;c:\Program Files\java\jdk1.8.0_152\bin;C:\Program Files (x86)\NVIDIA Corporation\PhysX\Common;C:\ProgramData\Oracle\Java\javapath;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Program Files\Microsoft SQL Server\Client SDK\ODBC\110\Tools\Binn\;C:\Program Files (x86)\Microsoft SQL Server\120\Tools\Binn\ManagementStudio\;C:\Program Files (x86)\Microsoft SQL Server\120\Tools\Binn\;C:\Program Files\Microsoft SQL Server\120\Tools\Binn\;C:\Program Files (x86)\Microsoft SQL Server\120\DTS\Binn\;C:\Program Files\Microsoft SQL Server\120\DTS\Binn\;C:\Program Files\Microsoft SQL Server\130\Tools\Binn\;C:\Program Files\dotnet\;C:\Program Files\TortoiseSVN\bin;C:\Program Files\cURL\bin;C:\Users\cgaray\AppData\Local\GitHubDesktop\bin;.]
  2018-04-27 17:19:29.042  INFO 17464 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
  2018-04-27 17:19:29.042  INFO 17464 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 693 ms
  2018-04-27 17:19:29.127  INFO 17464 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Servlet dispatcherServlet mapped to [/]
  2018-04-27 17:19:29.132  INFO 17464 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
  2018-04-27 17:19:29.132  INFO 17464 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
  2018-04-27 17:19:29.132  INFO 17464 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
  2018-04-27 17:19:29.132  INFO 17464 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
  2018-04-27 17:19:29.207  INFO 17464 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
  2018-04-27 17:19:29.362  INFO 17464 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6156496: startup date [Fri Apr 27 17:19:28 PYT 2018]; root of context hierarchy
  2018-04-27 17:19:29.393  INFO 17464 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/greeting]}" onto public hello.Greeting hello.GreetingController.greeting(java.lang.String)
  2018-04-27 17:19:29.398  INFO 17464 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
  2018-04-27 17:19:29.398  INFO 17464 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
  2018-04-27 17:19:29.413  INFO 17464 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
  2018-04-27 17:19:29.413  INFO 17464 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
  2018-04-27 17:19:29.513  INFO 17464 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
  2018-04-27 17:19:29.538  INFO 17464 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
  2018-04-27 17:19:29.543  INFO 17464 --- [           main] hello.Application                        : Started Application in 1.441 seconds (JVM running for 2.319)

  ```

  Como se puede ver, lo primero que aparece es el logo de Spring hecho con _ascii_, debajo del logo tenemos la versión del Spring Boot, en este caso corresponde a la versión 2.0.1.-

  A continuación tenemos, datos del equipo donde se está ejecutando el programa, el ID de Proceso, el directorio de las clases y el usuario bajo cuya sesión se está ejecutando.

  El inicio del servicio del _Tomcat_ y su puerto de escucha de peticiones _http(8080)_, las reglas de filtrado de direcciones expuestas, las direcciones expuestas, el registro de beans, el mensaje de finalización del arranque del _Tomcat_ y finalmente, el tiempo total ocupado en el proceso de arrranque de la aplicación.

  Si hubieran otras líneas, que no fueran errores, corresponderían, bien a mensajes de loggings, mensajes de la propia aplicación, mensajes de las librerías utilizadas o mensajes de errores durante el inicio o posteriores.

- Suponiendo que el arranque haya sido completamente normal, podremos probar el servicio web expuesto inicialmente, para ellos, ingresamos en la barra de direcciones de nuestro navegador web favorito la dirección correspondiente a nuestro equipo local, en el puerto de escucha del servicio _Tomcat_ [http://localhost:8080/greeting](http://localhost:8080/greeting) y nos tendría que retornar la siguiente cadena `JSON`:  

  ```json
  {"id":1,"content":"Hello, World!"}
  ```

  **_Donde_:**

  El atributo `id` corresponde al identificador único de la respuesta y el atributo `content` hace referencia al contenido del saludo.

  **_Obs._:**

  - Este resultado corresponde a la respuesta por defecto del servicio, es decir, cuando no recibe datos como parte de la solicitud. Si nos fijamos en el código de la clase `GreetingController` y en su método `greeting`, veremos que acepta un parámetro a través de la `url` y cuyo nombre es `name`, y también vemos que tiene un valor por defecto para el mismo `World`.  

  - Con la anotación `@RequestMapping` estamos indicando al framework que el método debe responder a la llamada en una `url` con un sufijo asignado, que en este caso es `/greeting` y cuyo método `http` corresponde al `GET`, que no se especifica, pero que es el valor por defecto.

  - Sabemos que el parámetro se espera sea pasado como parte de la `url` a través de la anotación `@RequestParam` (parámetro de petición), entonces podemos probar incluirlo como parte de la dirección ingresada en el navegador web e indicando un nombre al cual se realizará el saludo en la respuesta devuelta. Por ejemplo si pasamos como parámetro `name=diego` la respuesta sería esta:

    ```java
    {"id":3,"content":"Hello, diego!"}
    ```

    ​

- Analizar las clases y sus correspondientes funciones:

  - Application

  - Greeting

  - GreetingController

    ​

## Modificar el proyecto ##

En esta parte lo que haremos será comenzar a modificar el programa e ir probando los diferentes métodos de pasos de datos desde el cliente hacia los servicios web.



##### 1- Agregar un nuevo método de despedida

1. Crea la clase `Farewell` que extienda de `Greeting`

2. Agregar el constructor requerido.

3. Refactorizar (renombrar la clase `GreetingController` a `MainController` para hacer que el sea genérico.

4. Refactorizar la variable final `template` a `greet` y crear la variable final `fare`, similar pero con la cadena **"Goodbye, %s!"**.

5. Crear el método `farewell` que devuelva como resultado una instancia de la clase `Farewell` y que reciba como parámetros, de URL `name` y tenga un valor por defecto a elección.

6. Escribir el resultado del método, similar al método `greeting`.

7. Agregar la anotación correspondiente al método, podemos probar utilizar la anotación `@GetMapping`.

8. Probar el nuevo servicio expuesto invocando a su [URL local](http://localhost:8080/farewell?name=usuario).

   ​

**_Tarea 1-01_:** Investigar diferencias entre `@GetMapping` y `@RequestMapping`.



##### 2- Pruebas unitarias automáticas #####

Si bien no es el objetivo de esta sección aún podemos fijarnos que en el directorio de nuestro proyecto existe una rama correspondiente a los códigos de pruebas en `scr/test/java` y que contiene un archivo de clase llamado `MainControllerTests`.



  _**Img 1-02:** Directorio de códigos de programas de pruebas automáticas._

![](http://drive.google.com/uc?export=view&id=1kbdwFEctaGKKUj0d_1kvVUsI-loh1HaV)



Abriendo el archivo `MainControllerTests` podemos ver que contiene dos métodos que son para ejecutar pruebas sobre el servicio que originalmente venía con el proyecto base, el primero `noParamGreetingShouldReturnDefaultMessage()` es para probar la llamada al servicio `greeting` tal y como está, o sea, sin pasarle parámetro alguno.

El segundo método `paramGreetingShouldReturnTailoredMessage()` es para probar el mismo servicio pero pasando la cadena "Spring Community" como valor del parámetro `name`.

Ambos métodos ejecutan la llamada al servicio web y también se encargan de validar el resultado devuelto por el mismo (sino no sería una prueba completa).

Los métodos se ejecutan, por defecto, en orden alfabético y sólo si tienen la anotación `@Test`.

Este este es el mecanismo más común para hacer pruebas unitarias con _JUnit_ y _Spring_, y que lo que hace es, cuando se ejecuta la compilación, sí la opción de ejecutar pruebas está marcada, al final del proceso, las corre y devuelve el resultado de ejecución de las mismas. Nos ahorra el tener que hacerlas en forma manual y también a reducir la posibilidad de poner códigos con errores en producción.



**_Tarea 1-03_:**  Escribir los métodos de pruebas del servicio `Farewell` para los casos de paso de parámetro y sin el mismo.



##### 3- Crear una Base de Datos en Memoria para simular ABM #####


> ***Primer truco:*** Ahorrar líneas de código manuales.
> El proyecto [Lombok](https://projectlombok.org/) provee una librería que nos ayuda a reducir la cantidad de líneas de código que debemos escribir para las clases POJOs, esto tiene evidentes ventajas a la hora de iniciar un proyecto debido al tiempo que nos ahorramos.
>
> **Instalación:** Procedemos a descargar desde el [sitio oficial](https://projectlombok.org/download) e instalarlo según la [guía ofrecida](https://projectlombok.org/setup/eclipse).
>
> **Implementación:**
> Una vez terminada la instalación, procedemos a incluir la librería en nuestro proyecto siguiendo estos pasos:
>
> 1. Localizamos el archivo `pom.xml` y lo abrimos.
>
> 2. En la pestaña **Dependencias** hacemos click en el botón **[Add...]**
>
> 3. En la ventana emergente de selección de dependencia, en el cuadro de búsqueda ingresamos la cadena **"projectlombok"** e inmediatamente comenzará a listar aquellas que coincidan, que para este caso será una sola.
>
> 4. Seleccionamos el único ítem que devuelve como resultado y hacemos click en el botón **[OK]**
>
>    _Obs._: En caso de que no se encuentre la dependencia, podemos agregarla manualmente al archivo `POM.xml` descargando el fragmento correspondiente desde aluno de los repositorios abiertos en la red, como por ejemplo `https://mvnrepository.com`.
>
> 5. El STS comenzará a descargar las librerías correspondientes (en caso de que no las tengan aún disponibles en su repositorio local), compilará de vuelta el proyecto y quedará listo para utilizarse.


1. Creamos el paquete `hello.modelos`

2. Creamos la clase `Alumno` con los atributos `id`, `nombre`, `documento` y `activo`, sin los getters y setters.

3. Aplicamos las ventajas del proyecto Lombok:
  1. A nivel de clase añadimos la anotación `@Data`, verificamos que importe desde la librería correspondiente y nos fijamos en la pestaña **Outline** del STS. Qué vemos?

  2. Agregamos igualmente la anotación `@NoArgsConstructor`.

  3. Luego creamos un constructor con todos los atributos "seteables" y lo anotamos con `@Builder`.

  4. Por último `@EqualsAndHashCode`, y le indicamos que debe utilizar el atributo `documento`.

  5. Cuántas líneas de código nos hemos ahorrado?.

  6. Debería quedar algo así:

```java
     @Data
     @NoArgsConstructor
     @EqualsAndHashCode(of = { "documento" })
     public class Alumno  {

     	long id;
     	String nombre;
     	long documento;
     	boolean activo = false;
     	
     	@Builder
     	public Alumno(Long id, String nombre, long documento, boolean activo) {
     		super();
     		this.documento = documento;
     		this.activo = activo;
     	}
     }
```

4. Creamos la clase `Materia` con los atributos `id`, `nombre`, `activa`. Seguimos los mismos pasos que con `Alumno`.

5. Creamos la clase `Aula`, con los atributos `id`, `nombre`, una lista de `materias` y otra de `alumnos`, ambas de tipo **_final_**.
  - Notar que para las listas no se crean los ***setters*** correspondientes. Porqué?
  - Cuál es la ventaja que las listas sean de tipo ***final***?

6. Creamos el paquete `hello.servicios`.

7. Dentro del nuevo paquete, creamos la clase `BaseDatos`, de tipo ***final***, con los atributos siguientes:
  - Una lista de instancias de la clase `Aula`, otra de `Materia` y una más de `Alumno`, todas de tipo ***final***
  - Las listas de instancias no deben aceptar instancias repetidas, deben ser únicas. 
  - Creamos instancias de `AtomicInteger` para utilizarlos como generadores de IDs para cada colección.
  - Antes continuar, crearemos una _super clase_ `Entidad`, que implemente la interface `java.io.Serializable` y que haga de padre a nuestras clases de datos actuales. Ésta clase deberá tener un atributo `id` de tipo genérico y otro `nombre` de tipo `String`.  Ya veremos las ventajas que brindan.
  - Modificamos las entidades originales para que extiendan de `Entidad` y resolvemos los warnings que nos aparezcan.
  - Hagamos que la clase `BaseDatos` sea un _Singleton_, para reducir las probabilidades de inconsistencias de datos:
    - Creamos una instancia estática de sí misma como atributo.
    - "Privatizamos" el constructor de la clase.
    - Creamos el método `getInstancia()` que retorne la referencia a la instancia estática.
  - Agregamos los métodos para manejar los datos contenidos:
    - Persistir
    - Remover
    - Listar (con y sin criterios de filtrado)


_**Tarea 1-004:**_ Probemos nuestra aplicación hasta este punto escribiendo los métodos en la clase de testing.


  - Para cada entidad.
  - Para la base de datos.
  - Para los métodos de persistencia y listado.


### Fin del primer ejercicio

Hasta aquí ya se tiene todo lo necesario, sólo faltaría enlazarlo a una base de datos.

----------

## Referencias ##
- Guía Spring Rest Services.
	- [http://spring.io/guides/gs/rest-service/](http://spring.io/guides/gs/rest-service/)
- Ejemplos completos de aplicaciones con Spring (en modo boot).
	- [https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples)
