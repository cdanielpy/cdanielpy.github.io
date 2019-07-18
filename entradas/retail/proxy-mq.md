# Proxy-MQ
_Uno de mis mejores trabajos (autobombo)._

![Logo RabbitMQ](http://drive.google.com/uc?export=view&id=10DadrJ-pfkwcWLIEQXkrnt0A7lHIxZyk)

## La presentación
Cuando me separaron de la línea principal del equipo de desarrollo de sistemas con el fin de que el mismo pudiera funcionar con mayor independencia, me encargaron dedicarme a investigar la implementación de un sistema de interfaces de datos entre sistemas a través un software de intercambio de mensajes, un tal [RabbitMQ](https://www.rabbitmq.com/), que implementa el protocolo [AMQP](https://es.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol).

En un principio pensé que se trataba de un software de mensajería instantánea tipo Skype, Messenger, etc, porque con la descripción, es lo primero que se le viene a uno a la cabeza "sistema de intercambio de mensajes", con el tiempo descubrí que se podría implementar algo similar, pero que la funcionalidad del mismo estaba muy por encima de eso.

Gracias a la excelente documentación y a los ejemplos de modos de operación que se proveen en la página oficial del proyecto, hacer las primeras pruebas fue cuestión de minutos, y a partir de ahí fue cuestión de horas hacer pruebas pilotos entre dos sistemas de desarrollo propio. Después de comunicar el estado de avance de la investigación y pruebas, se habilitó el camino para implementar el sistema como "middleware" de comunicación entre los sistemas del grupo empresarial, tanto de desarrollo propio como de terceros.

### El primer reto: Que sea auditable.
----
Estando a cargo del desarrollo y la implantación del mismo, uno de los requisitos del proyecto era que los datos pudieran ser auditables, es decir, que pudieran quedar registros de los datos intercambiados en un único repositorio, a fin de tener que evitar recurrir a los publicadores y suscriptores para "ver" qué datos estaban intercambiando. El RabbitMQ en sí mismo no implementa una base de datos relacional donde alojar los datos de los mensajes en forma permanente, sí posee una base de datos que mantiene una copia temporal de los mensajes marcados como persistentes hasta que el cliente suscriptor confirma la recepción del mismo, la finalidad de esto es cumplir con el compromiso de "a prueba de fallos".

La mejor idea que se me ocurrió en ese momento fue desarrollar una aplicación que haría de "mensajero proxy", que se encargaría de tomar los mensajes, persistir una copia del mismo a una base de datos transaccional y reenviarlo al sistema de destino, parecía una solución aplicable y muy simple, aunque después creció un poco más ya que debía implementarse un modelo estándar de mensajes a procesar, independientes de la carga útil de los mismos, requerir una aplicación para consultar los mensajes, llevar un registro de sus cambios de estados (el siguiente reto), un ABM para el registro y mantenimiento de los sistemas clientes y la capacidad de no perder mensajes que cumplieran con el estándar definido. La idea más sencilla había crecido por sí misma.

Con todo lo anterior, la aplicación que hacía de proxy podía recibir, persistir una copia a la base de datos transaccional y reenvíar el mensaje al sistema de destino con un mínimo de impacto en el tiempo de transferencia entre los extremos del canal.

Una idea que se me ocurrió para poder evaluar el impacto en los tiempos era manejar los estados de cada paso en el proceso de reenvío de mensajes y también el estado final del mismo una vez procesado por el sistema de destino.

### El segundo reto: Control de tiempos y estados
----
Una vez que teníamos el desarrollo inicial del _"mensajero proxy"_, la siguiente necesidad que apareció fue la de llevar un control del impacto en el proceso de paso de mensajes, previendo que con el tiempo la cantidad de sistemas clientes y mensajes iría creciendo y se necesitarían métricas para dimensionar el crecimiento del sistema.

La idea al rescate era muy simple, llevar un control de los cambios de estado a medida que pasa por cada uno durante el proceso de persistencia del mismo. Entonces, lo que hicimos fue simplemente dejar un registro del momento de recepción del mensaje, otro del momento de reenvío y uno final, del momento de retorno de respuesta de procesamiento. "Respuesta de procesamiento", cómo sabríamos cuál era el estado de procesamiento final del mensaje en el sistema de destino si sólo estábamos haciendo una "banda de transporte" que funcionaba en un sólo sentido? Éste era, el tercer reto.

### Tercer reto: Registro de Estado de Procesamiento Final
----
La última cuestión a resolver era el registro de tiempo de descarga de cada mensaje por el sistema de destino del mismo, si bien el _"mensajero proxy"_ tenía un control del paso de los mensajes por sus procesos internos, una vez que lo reenvía a la cola de destino del mismo le pierde el rastro. Aquí la idea a implementar requería de una mano por parte del cliente receptor, que tendría que incluir un proceso de retorno en algún punto del procesamiento del mensaje recibido, mientras, por parte mía, tendría que desarrollar un _"servicio de recepción de notificaciones"_.

Después de verificar la factibilidad de implementación de la idea, que lo hicimos fue implementar una cola de entrada de mensajes de acuse de recepción de los mensajes de datos, mediante el cual, el sistema receptor enviaría un mensaje, con una estructura estándar también, conteniendo los detalles correspondientes que se deseaban dejar como constancia de la recepción y/o el resultado del procesamiento del mensaje original. 

Con el mensaje _"en mano"_, el servicio procedía a hacer una actualización del mensaje en la base de datos transaccional, agregando el momento de la recepción de la notificación correspondiente y los detalles de la notificación recibida. Con esto, el ciclo estaba cerrado.

### Puesta en Producción
----
Después de haber resuelto todas las cuestiones derivadas del simple pedido de poder acceder a los registros de mensajes de datos intercambiados entre los sistemas clientes comunicados, era el momento de poner a prueba lo diseñado y desarrollado. Con la cautela que corresponde a un proyecto nuevo y que apuntaba a interactuar con los grandes sistemas empresariales del grupo, se decidió que los primeros sistemas a comunicar serían el de ServiceDesk y el Redmine (sistema de registro de reclamos de usuarios y el de registro de tareas del equipo de IT).
Obviamente, como era de esperarse con los buenos desarrolladores de software que somos, hubo ajustes que hacer tras las primeras pruebas, pero se pudo cumplir con el objetivo de la interface de datos, que debía funcionar del siguiente modo:

+ Cuando un operador del Centro de Asistencia y Soporte (CAS) asignaba un ticket registrado en el ServiceDesk a un miembro del equipo de IT, el sistema debía de enviar un mensaje con los detalles del ticket al Redmine.
+ El Redmine, al recibir el mensaje, debía proceder a dar de alta una "tarea de soporte" asignada al miembro correspondiente del equipo de IT, con el identificador del ticket como atributo, y notifica del estado de procesamiento del mensaje al "servicio de recepción de notificaciones".

A la inversa, el funcionamiento era el siguiente:
+ En el Redmine, cuando un miembro del equipo de IT procede a dar por cerrada una tarea (cambio de estado a "Cerrada"), el sistema envía un mensaje con el identificador del ticket al ServiceDesk, y este, al recibir el mensaje, procede a cambiar el estado del mismo a "Resuelto".

Con este primer flujo, a parte de poner a prueba el nuevo sistema de intercambio de datos, lo que se pretendía era evitar que miembros del equipo de IT tuviera que acceder a ambos sistemas a registrar los detalles de sus tareas realizadas para los casos de soporte a usuarios.

Esquema del flujo de datos durante la implantación inicial del proyecto RabbitMQ.
![Esquema ServiceDesk - Redmine](http://drive.google.com/uc?export=view&id=1nyLsg6El0v9kyUFDng4gu-898zQ4Clrd)

----

### Al día de hoy...
Casi cuatro años después, el sistema es un elemento esencial para el área de tecnología a nivel de grupo empresarial y ha crecido al punto de contar con una configuración de alta disponibilidad basada en dos nodos servidores, en clúster, y un balanceador de carga y, como era de esperarse, opera _24/7/365_.
También se ha implementado la misma infraestructura para las empresas del grupo en el exterior y se han establecidos canales de comunicación para entre los sistemas externos y los sistemas locales.

#### Algunas estadísticas actuales, a nivel local, son las siguientes:

+ 2 procesos/sistema en promedio.
+ 22 sistemas y aplicaciones clientes conectadas.
+ 280K+/mes mensajes canalizados (6.4 mensajes/segundo).

![Grafico de Mensajes por Mes](http://drive.google.com/uc?export=view&id=1dJjF1wvxSdu1-UAL4a5BoOkE6bzwhiqc)
![Grafico de Mensajes por Estados](http://drive.google.com/uc?export=view&id=1kNKc0gVpCVUZklURVVVVZCvjsdf9cWza)

> Obs.: Los estados corresponden al estado final asignado por el programa que procesa el mensaje, a excepción de los "reenviados", que en realidad no tienen un "acuse de recepción" por parte del sistema destino.

Otro uso que le dimos a partir de este año, es el de canalizador de eventos de logging de algunos sistemas y aplicaciones, de modo a tener un repositorio unificado de consulta de los eventos, sin tener que recurrir a los archivos .log alojados en cada servidor de aplicación, salvo incidentes relacionados a la conectividad de red, obviamente. Éstos mensajes de eventos de logging no pasan a través del "mensajero proxy" ya que no se requiere auditarlos ni son de alta importancia, ya que se en caso de fallo, se cuenta aún con los archivos generados por cada sistema en su propia instalación.
[Desarrollo del Proyecto](https://cdanielpy.github.io/entradas/mongolog)

> Éste es uno de los trabajos de los que me siento más orgulloso de llevar a cabo, sobre todo por la importancia que ha ganado y la performance que presenta, si bien en el fondo parece simple, implementarlo parecía todo un reto en su momento.
