# 5. Protocolos de enrutamiento

Existe un gran número de protocolos usados para el manejo de la información, pero no todos son aplicables a las redes de sensores (WNS), ya que el medio en el que se emplean es totalmente diferente. En las redes cableadas la pérdida de información se debe a la gran cantidad de tráfico que corre por el medio, mientras que en las WNS el medio usado para la comunicación es más hostil, el aire, donde los dispositivos y las transmisiones están expuestas a las implicaciones medioambientales, al ruido y otros inconvenientes que se pueda dar en el medio, todo esto sin mencionar que los dispositivos deben _luchar_ por acceder al medio de comunicación. Es por ello que el enfoque que se le da a un protocolo para redes ad hoc es muy diferente al usado en redes cableadas, ya que en este último caso los protocolos no están pensados para trabajar con tanto _stress_. 

## 5.2 Clasificación de los protocolos según su funcionamiento 
Los protocolos usados en las redes inalámbricas se clasifican en tres tipos, estos protocolos se han desarrollado ante la necesidad de controlar el enrutamiento en las redes ad hoc [3], teniendo en cuenta las limitaciones de los dispositivos:

### 5.2.1 Protocolos Proactivos

Son aquellos que actualizan periódicamente las tablas de enrutamiento de todos los nodos de la red aunque no estén enviando información. Cuando surge algún cambio entre las conexiones de la red, la tabla se actualiza y el protocolo elige la ruta mas óptima para enviar información. Esto se debe al intercambio de mensajes de control, lo que incrementa el consumo de batería por el número de paquetes enviados a la red.

### 5.2.2 Protocolos Reactivos 

Estos únicamente tienen rutas de encaminamiento en sus tablas cuando un nodo origen tiene que realizar una comunicación con otro nodo en la red. Los protocolos reactivos al iniciar una comunicación y no tener una ruta para llegar al nodo destino, envían un mensaje de descubrimiento de ruta, al recibir la respuesta a dicho mensaje añade esta ruta en su tabla de enrutamiento. Es entonces posible la comunicación con el destino. El mayor inconveniente es la latencia que se añade al primer paquete de la transmisión por esa nueva ruta, pero a su vez mejora las prestaciones de la batería en los nodos. Dentro de este protocolo existen dos subclases: 

- **Enrutamiento origen**: La ruta de los nodos por donde tiene que pasar la información es almacenada en las cabeceras de los paquetes, de modo que los nodos intermedios no necesitan tablas de enrutamiento, ya que solo basta con leer las cabeceras para saber a quien reenviar la información. 

No es aconsejable usarlo en redes extensas, ya que a medida que el mensaje pasa por cada nodo se incrementa la cabecera del paquete.

- **Enrutamiento salto a salto**: La ruta la escoge cada nodo en cualquier momento, ya que cuando se envía la información la cabecera del paquete contiene la dirección del nodo destino y la dirección del siguiente salto. 

Este se adapta más rápido a los cambios de la topología pero genera un gasto superior de recursos en los nodos intermedios pues tienen que almacenar en tablas de enrutamiento las rutas correspondientes. 

### 5.2.3 Protocolos híbridos 
Los protocolos híbridos son una mezcla de los protocolos proactivos y los reactivos. El fin de estos es usar las mejores características que ofrecen ambos. Los protocolos dividen las redes en zonas, y los nodos que están lejos del destino utilizan enrutamiento reactivo mientras que los que están cerca utilizan enrutamiento proactivo, como es el caso de ZRP (The Zone Routing Protocol). 

Otros ejemplos de protocolos híbridos son IS-IS de OSI (Intermediate System to Intermediate System) y EIGRP (Enhanced Interior Gateway Routing Protocol) de CISCO. 


### 5.2.4 Criterios para seleccionar el protocolo

El tipo de red a implementar debe ser una red basada en dispositivos embebidos de bajo consumo energético, lo cual limita el hardware que lo compone, por lo tanto este debe:
- Obtener las mejores rutas de encaminamiento.
- Hallar un mínimo de transmisión de mensajes.
- Baja latencia de los paquetes entre la fuente y el destino.
- La red debe estabilizarse en el menor tiempo posible.
- No sobrecargar la red con mensajes de control.
- Sea resiliente frente a posibles fallos.

En todos los protocolos de encaminamiento usados para redes inalámbricas y _WNS_ es importante el establecimiento de ruta. Si un protocolo elige buenas rutas de encaminamiento ofrecerá menor latencia entre la fuente y el destino, por consiguiente realizará menos transmisiones de mensajes, por ende consumirá menos energía del dispositivo.


## 5.3 Elección del protocolo para la implementación de Locha Mesh

<figure>
<img src="../pics/protocolo_seleccion.svg"  height="650" width="450"/>
</figure>

En este diagrama resumimos los criterios importantes para la elección del protocolo que más se adapta a Locha Mesh. Podemos destacar que: 

- Debe ser libre de `loops`.
- El costo computacional debe ser muy bajo, dado que se ejecuta dentro de un sistema embebido con recursos limitados.
- El protocolo debe ser reactivo, para evitar inundar la red con mensajes aún cuando las rutas no se están utilizando, y para ahorrar batería mientras no se requieren rutas por parte del usuario.

Se puede apreciar en la tabla que la puntuación más alta al sumar cada columna es para el protocolo _AODVv2_ y _TORA_. Hemos decidido trabajar con el protocolo _AODVv2_, ya que cuenta con documentación actualizada en la web, aunque es plausible una revisión de _TORA_.

Otro punto a tener en cuenta es la topología de red se utilizará para la implementación, según la tabla, respetando los criterios por parte del cliente y reduciendo los puntos de fallo la topología en estrella es descartada y en su lugar se utilizará la topología en malla para Locha Mesh.
