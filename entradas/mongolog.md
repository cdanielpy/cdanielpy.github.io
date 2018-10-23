# Logging Centralizado

## Spring Boot + Logback + RabbitMQ + MongoDB

<div align="center">
<img src="https://drive.google.com/uc?id=1AocSmYP20Lu3o0jIai-O3N2dCkwzRMl2" width="100" alt="logback logo"/>
<img src="https://drive.google.com/uc?id=10DadrJ-pfkwcWLIEQXkrnt0A7lHIxZyk" width="100" alt="logo rabbitmq"/>
<img src="https://drive.google.com/uc?id=173WPgVpK5eHkAyI84qLo_d8lNE0bcvb-" width="100" alt="logo spring-boot"/>
<img src="https://drive.google.com/uc?id=1VM92SaWbjKGYUAibjhEkrXI0K_RrADBm" width="100" alt="logo mongodb"/>
</div><br/>
<div align="center">
<img src="https://drive.google.com/uc?id=1gIX_QEYZFQTYoAlCS6Ab9XlkERxFStUS" width="200" alt="logo json"/>
</div>
<br/>

## Presentación

Pasado algún tiempo abandonado este blog estoy de vuelta para plasmar otro de mis trabajos  cubriendo una necesidad personal en mi ámbito laboral, que en esta ocasión era el poder visualizar en forma centralizada los eventos generados por mis aplicaciones instaladas en distintos servidores.

Después de mucho leer, evidentemente no era el primero en toparse con esta necesidad así que ya habían bastantes antedecentes y proyectos precendentes, como el  [Graylog](https://www.graylog.org/overview), que se puede utilizar gratuitamente hasta los 5GB/día de datos, y que en mi caso tengo proyectado incluir varias a aplicaciones más por lo que podría quedarse corto.


## Tecnologías

Aprovechando mi experiencia decidí investigar como desarrollar cada uno de los elementos citados en cada lectura valíendome de las herramientas que más conozco:

- [Logback](https://logback.qos.ch/)<sub>(v1.2.3)</sub>: para el registro de los eventos marcados a nivel de código de aplicación.
- [RabbitMQ](https://www.rabbitmq.com/)<sub>(v3.3.5)</sub>: para el transporte de los datos hasta la base de datos NoSQL.
- [Spring Boot](https://spring.io/projects/spring-boot)<sub>(v2.0.5)</sub>: utilizando la integración de fábrica con el [RabbitMQ](https://www.rabbitmq.com/) y una de sus clases, particularmente.
- [MongoDB](https://www.mongodb.com/es)<sub>(v3.4.1)</sub>: como almacén de los datos de eventos registrados, y
- [JSON](https://www.json.org/): como estructura modelo de los datos en todo el flujo.


## Diagrama de Flujo

<div align="center"><img src="https://drive.google.com/uc?id=1VMny-HWh1Ez-ezZ1Q1UqBBhS9d-1AzIx" width="600" alt="dfd logger"/></div>
<br/>


##### En la imagen podemos ver cómo sería el flujo de datos de eventos registrados (de izquierda a derecha):

1. Los eventos de cada aplicación se generan y se notifican a una cola en el RabbitMQ.
2. El RabbitMQ absorbe la carga de datos entrantes y se centralizan en la cola determinada.
3. La aplicación (que desarrollaremos) monitorea la cola de mensajes y recibe cada uno de ellos, los formatea y persiste en la base de datos MongoDB.
4. A partir de aquí se puede hacer lo que se quiera con los datos, reportes, otras aplicaciones de monitoreo, filtrado, envío de correos, etc.

----

## Manos a la obra

Despúes de tanta teoría ya podemos empezar con la parte dura en la que escribiremos el código de las aplicaciones de origenes de eventos como también la de recepción y persistencia de los mismos.

### [Paso 1: Preparar el RabbitMQ](./mongolog/rabbitconf)
