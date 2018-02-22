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

- Desde el menú **Archivo/File** del STS, seleccionar **[File] -> [new] -> [Import Spring Getting Started Content]**.
- Seleccionar el proyecto **"Rest Service"**, marcar la opción **[Maven]** del conjunto "**Build Type**" y del conjunto "**Code Sets**", la opción **[complete]**.
- Cuando haya finalizado la descarga de los archivos requeridos, se haya generado la estructura de directorios y haya construido/compilado el proyecto por primera vez, nos aparecerá 

### 2- Resolver las dependencias del proyecto ###

En este paso lo que hacemos es asegurarnos de que las dependencias del proyecto estén descargadas a nuestro repositorio local (en nuestro equipo), a modo de que puedan ser referenciados por el programa que estaremos preparando.

- Click derecho sobre el proyecto, del menú contextual seleccionar las opciones **[Maven] -> [Update project...]**.

##### 3- Probar el proyecto base

- Click derecho sobre el proyecto, del menú contextual seleccionar las opciones **[Run As] -> [Spring Boot App]**.
- Probar la respuesta desde [http://localhost:8080/greeting](http://localhost:8080/greeting).
	- Obs.:
- Analizar las clases y sus correspondientes funciones.

## Modificar el proyecto ##


##### 1- Agregar un nuevo método de despedida

1. Crea la clase `Farewell` que extienda de `Greeting`
2. Agregar el constructor requerido
3. Renombrar (refactorizar) la clase `GreetingController` a `MainController` para hacer el nombre un poco más genérico.
4. Refactorizar la variable final `template` a `greet` y crear la variable final `fare`, similar pero con la cadena **"Goodbye, %s!"**.
5. Crear el método `farewell` que devuelva como resultado una instancia de la clase `Farewell` y que reciba como parámetros, de URL `name` y tenga un valor por defecto a elección.
6. Probar el nuevo servicio expuesto invocando a su [URL local](http://localhost:8080/farewell?name=usuario) 


##### 2- Crear una Base de Datos en Memoria para simular ABM #####


> ***Primer truco:*** Ahorrar líneas de código manuales.
> El proyecto [Lombok](https://projectlombok.org/) provee una librería que  nos ayudará a reducir la cantidad de líneas de código que debemos escribir para las clases POJOs.
> 
> **Instalación:** Procedemos a descargar desde el [sitio oficial](https://projectlombok.org/download) e instalarlo según la [guía ofrecida](https://projectlombok.org/setup/eclipse).
> 
> **Implementación:**
> Una vez terminada la instalación, procedemos a incluir la librería en nuestro proyecto siguiendo estos pasos:
> 
> 1. Localizamos el archivo `pom.xml` y lo abrimos.
> 2. En la pestaña **Dependencias** hacemos click en el botón **[Add...]**
> 3. En la ventana emergente de selección de dependencia, en el cuadro de búsqueda ingresamos la cadena **"projectlombok"** e inmediatamente comenzará a listar aquellas que coincidan, que para este caso será una sola.
> 4. Seleccionamos el único ítem que devuelve como resultado y hacemos click en el botón **[OK]**
> 5. El STS comenzará a descargar las librerías correspondientes (en caso de que no las tengan aún disponibles en su repositorio local), compilará de vuelta el proyecto y quedará listo para utilizarse.


1. Creamos el paquete `hello.modelos`
1. Creamos la clase `Alumno` con los atributos `id`, `nombre`, `documento` y `activo`, sin los getters y setters.
2. Aplicamos las ventajas del proyecto Lombok:
	1. A nivel de clase añadimos la anotación `@Data`, verificamos que importe desde la librería correspondiente y nos fijamos en la pestaña **Outline** del STS. Qué vemos?
	2. Agregamos igualmente la anotación `@NoArgsConstructor`.
	3. Luego `@AllArgsConstructor`.
	4. Por último `@EqualsAndHashCode`, y le indicamos que debe utilizar el atributo `documento`.
	5. Cuántas líneas de código nos hemos ahorrado?.
3. Creamos la clase `Materia` con los atributos `id`, `nombre`. Seguimos los mismos pasos que con `Alumno`.
4. Creamos la clase `Aula`, con los atributos `id`, `nombre`, `materia` y una lista de `alumnos`, de tipo final.
	- Notar que para la lista de alumnos no se crea el ***setter*** correspondiente. Porqué?
	- Porqué es mejor que la lista de alumnos sea de tipo ***final***?
5. Creamos el paquete `hello.servicios`.
6. Dentro del nuevo paquete, creamos la clase `BaseDatos`, de tipo ***final***, con los atributos siguientes:
	- Una lista de instancias de la clase `Aula`, otra de `Materia` y una más de `Alumno`, todas de tipo ***final***
	- Las listas de instancias no deben aceptar instancias repetidas, deben ser únicas. 
	- Creamos instancias de `AtomicInteger` para utilizarlos como generadores de IDs para cada colección.
	- Le creamos los métodos para manejar los datos contenidos:
		- Agregar
		- Modificar
		- Remover
		- Listar
7.   

> **Tarea:** AQUI VA UNA TAREA RESPECTO DE LO QUE SE VIO HASTA ESTE PUNTO

Hasta aquí ya se tiene todo lo necesario, sólo faltaría enlazarlo a una base de datos.

----------

## Referencias ##
- Guía Spring Rest Services.
	- [http://spring.io/guides/gs/rest-service/](http://spring.io/guides/gs/rest-service/)
- Ejemplos completos de aplicaciones con Spring (en modo boot).
	- [https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples)
