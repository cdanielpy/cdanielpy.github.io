# Reactivación de Ordenes de Compras Vencidas

![facturas][facturas]

## Intro
---
Aprovechando esta cuarentena por el [Covid-19](https://g.co/kgs/P4sQEd) y por las quejas que supuestamente presentan mis usuarios constantemente respecto a la burocracia que implemento a la hora de cumplir con sus pedidos de asistencia, me puse a escribir esto y pretendo seguir con cada caso nuevo que se presente en este periodo a modo de ir exponiendo mi punto de vista y defendiendo mi posición.

Espero que mis superiores también se interesen y se sirvan tener un punto de vista más claro y así evitar estar del lado del usuario sólo por ser usuario.

Y por supuesto, que mis usuarios aprendan un poco más acerca de los procesos involucrados en cada caso que vaya exponiendo. Claro está que acepto comentarios o sugerencias a mi dirección de correo particular cuya dirección les dejo al final del post.

## Presentación
---
En un negocio como el de los supermercados lo cotidiano es tener una gran cantidad de proveedores esperando a entregar sus mercaderías para la venta. 

El personal del área de recepción de mercaderías se pasan el día recibiendo las facturas, verificando la cantidad y calidad de los productos descargados de los vehículos de transporte, registrando los datos de las facturas en sus sistemas y entregando las actas de recepción a los encargados de cada móvil como prueba de lo recibido por uno y entregado por el otro. Son pocos pasos pero el tiempo requerido para ejecutarlos siempre está sujeto a la cantidad y el tipo de mercaderías.

Cuando se acuerdan los términos comerciales entre el proveedor y el cliente, uno de los puntos es plazo de entrega de un pedido de compras, habitualmente es de 7 días desde la emisión de la _**Orden de Compra**_ (**OC**), por lo que para los operadores de recepción en la sucursal, el sistema les muestra sólo disponible las **OCs** que están pendientes de recepción, parcial o totalmente, y que no se hayan vencido, es decir, que no tengan más de 7 días desde su creación.

> Los días se cuentan tomando en cuenta la hora de creación de la _**Orden de Compra**_, por lo que el primer día se cumple recién al día siguiente a la misma hora en que fue creado el documento.

> El proceso de control de vigencias de Ordenes de Compras en el sistema se ejecuta por la noche, por lo que en consecuencia, recién al octavo día, contando desde el mismo día de la creación del documento, es que el sistema pasa a cambiarle su estado a **VENCIDA**, con lo que ya no puede ser recibida en la sucursal.

## El problema
---
Es muy común que un proveedor no pueda cumplir con los plazos de entrega acordados, por cualquier motivo, pero siempre se deja abierta la posibilidad de entregar el pedido de una **OC** con unos días de atraso, ya que ninguna de las partes quisiera perder la venta de los productos en cuestión y sobre todo si se trata de productos de la canasta básica o productos de temporada (o de moda).

Otro factor común que inside en el registro atrasado de las facturas de una recepción de mercaderías es la cantidad de trabajo que no se pudo terminar en el día y pasan para dias posteriores, a pesar de que el proveedor entregó sus mercaderías y documentos a tiempo y en forma.

Cuando se da alguna de las situaciones anteriores, la sucursal ya no tiene a disposición la **OC** original sobre la cual fue emitida la factura del proveedor.

> En realidad existen muchos factures que pueden retrasar el registro de facturas de compras de mercaderías en el sistema de la empresa, pero para el caso, los citados son los más frecuentes.

## El versión compleja
---

![acopio][acopio]

Las cadenas de supermercados pueden tener un centro de distribución de mercaderías (**CD**) que también funcionan como centros de acopio de mercaderías de proveedores y luego se encargan de distribuírlos a las sucursales. Por este motivo, también estan sujetos a los mismos factores de las sucursales además de alguno que otro particular del tipo de operación, como ser la _**Compra Centralizada**_ y el _**Cross Docking**_ de mercaderías.

* _**Las Compras Centralizadas:**_ no son más que la compra de productos las para sucursales con destino al **CD**, que sería el acopio de las mercaderías para su distribución posterior.

* _**El Cross Docking:**_ es el paso temporal de las mercarías de cada sucursal por el **CD** a modo de evitarle a los proveedores la movilización de sus vehículos a zonas muy alejadas de su propio centro de distribución.

Para cualquiera de estas operaciones, el proceso de compras es básicamente el mismo que para una sucursal, la empresa emite una **OC** y el proveedor entrega sus mercaderías y facturas.

## La sólucion al problema habitual
---
El problema citado al principio (recepción de facturas sobre **OCs** vencidas) normalmente, para las sucursales se resuelven emitiendo nuevas **OCs** con exactamente los mismos ítems que las originales, demás está decir que son los operadores de las sucursales quienes notifican de la situación al departamento de compras.

Por supuesto, pueden darse situaciones como en la que los precios del proveedor hayan variado desde la fecha de emisión de la **OC** original, con lo que podrían generarse _**Solicitudes de Notas de Crédito por Diferencias de Precios**_ para el proveedor que probablmente no correspondan, del todo.

La solución más tediosa suele ser solicitar al departamento de informática el cambio de estado de la **OC** original y pasarla de **VENCIDA** a *VIGENTE* actualizando el valor del campo correspondiente directamente en la base de datos.

> Este procedimiento suele pasarse como una vulnerabilidad crítica por parte del equipo de **auditoría externa**.

## La solución tecnológica
---
Cómo de costumbre, cuando los pedidos de cambios de estado al departamento de informática son demasiado frecuentes, lo que hacemos es ofrecer alternativas.

Si bien para los casos de entregas o registros de documentos atrasados es una decisión administrativa la que de debe autorizar o rechazar un pedido antes de que procedamos a ejecutar lo cambios, hay situaciones en que corresponde cederle el control a los usuarios del área de compras.

Nuestro sistema tiene la opción de que un comprador pueda _reactivar_ una **OC** que ya ha vencido siempre y cuando la misma cumpla alguna de las condiciones siguientes:

* _**Es una Orden de Compra Consolidada:**_ no es más que la suma de **OCs** cargadas por los compradores de las diferentes gerencias del área comercial _(perecederos, bebidas, etc.)_ y por proveedor, de tal modo a que el proveedor no tenga que emitir una factura por cada **OC** original. En este caso corresponde la _reactivación_ de la **OC** porque los días de entrega de mercaderías de cada gerencia no son los mismos, y puede que el día de emisión de la **OC** sea anterior al día de la semana acordado para la entrega de mercaderías de alguna de las gerencias cuyos pedidos se consolidaron y se toparán con la **OC** vencida.

* _**Es una Orden de Compra Centralizada:**_ éstas ordenes, al ser generadas en base a la composición de pedidos de varias sucursales a la vez, corresponden que sean reactivadas porque pueden generarse retrasos en la recepción de las mercaderías ya sea por parte del **CD** o bien por parte del proveedor por la gran cantidad de ítems que deben de preparar y entregar.

Otra opción para proveedores que siempre están con este tipo de inconvenientes es el de extender los plazos de entrega, por ejemplo, si el proveedor siempre entrega 2 días después del día correspondiente según el acuerdo sobre 7 días, actualizar el dato con el valor 9, el sistema tendria la opción de hacer esto. 

En resumen, el sistema tiene la opción de _**reactivar**_ las **OCs** vencidas para que los usuarios puedan hacerlo sin necesidad de requerir asistencia por parte del departamento de informática, como también la opción de extender los plazos de vigencia de las **OCs** para proveedores específicos que siempre están pasando por esta situación. 

## La complicación
---
Como habrán notado, no existe ninguna complicación en cualquiera de los casos, siempre y cuando dejen en claro las circunstancias por la que requieren la reactivación de las **OCs** no Centralizadas ni Consolidadas para que cuando auditoría externa note que se recibieron facturas después del vencimiento de las mismas, desde el departamento de informática podamos proveer nombres y apellidos de cada caso.

Para el caso del **CD** y los procesos tienen una ligera complicación extra y está relacionada a la distribución de los costos de los productos para las sucursales. 

El proceso _normal_ debería ser, recibir las facturas y enviar los productos a las sucursales, de este modo los costos de los productos recibidos se actualizan en el **CD** y luego de transfieren las mercaderías con los costos actualizados a las sucursales de destino, que a su vez al recibirlos actualizan sus costos locales.

El problema es que la operación física no va acompañada de la operación en el sistema:
* En la operación física primero se reciben las mercaderías y se despachan hacia las sucursales.
* En el sistema, primero se despachan y luego se reciben las facturas.

Lo que tiene como consecuencia un desface entre los costos de recepción del **CD** y los de envío a las sucursales y esto al parecer pasa desapercibido, y es lo que nos obliga a intentar bloquear el pedido de _reactivación_ de **OC** vencidas cuando se trata de los Centros de Distribución.

## Resumen
---
![auditoria][auditoria]

La idea de este post es esclacer los procesos involucrados en el proceso de recepción de facturas y los efectos colaterales que tiene el alterarlos.

Los usuarios tienen el modo de solucionar estos casos sin requerir del equipo de informática, pero siempre es les parece más fácil solicitar la alteración de los datos directamente en el sistema, sin considerar que todo es trazado posteriormente por el equipo de auditoría.  

No es que simplemente no querramos hacer el trabajo, lo que en mi caso particular pretendo es que los usuarios mismos sean quienes justifiquen las solicitudes enviadas y que expongan las causas que llevaron a cada situación, y si se tratase de errores que también lo aclaren para que las consecuencias puedan ser tenidas en cuenta, sobre todo lo que hace a la actualización de costos.

Ah, y por si no queda claro y por sobre todo, porque para las auditorías sería grave que el equipo del departamento de informática estuviera realizando alteraciones de datos saltandose las reglas del negocio, y legales en algunos casos.

## Contacto para comentarios y sugerencias
Pueden escribirme a mi dirección de correo particular para lo que quieran a daniel.garay.py@outlook.com.


[facturas]: https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcRGXREeiKenWo-R5G6Xp8h7uSPDQFUYDbIYpD-6F55g77oNHSHd&usqp=CAU

[acopio]:https://media-exp1.licdn.com/dms/image/C4E12AQFesk_n4G1PGA/article-cover_image-shrink_600_2000/0?e=1591228800&v=beta&t=5nwF3kHyTIGRTZdVa39_Ly-SYrXYwuBc98k0AM4LexE

[auditoria]:https://auditest.es/wp-content/uploads/2018/05/Captura-de-pantalla-2018-05-11-a-las-11.10.55.png
