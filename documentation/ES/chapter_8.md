
# 8. AODVv2.

[7,9] _AODV_ es un protocolo de enrutamiento para redes móviles ad-hoc (_MANETs_) inalámbricas. _AODV_ es la evolución de su anterior protocolo llamado _DYMO_ (2003), que luego adopta el nombre de _AODVv2_ (2013).

El _AODV_ es ideal para las redes ad-Hoc. Intercambia mensajes cuando necesita establecer una comunicación; es decir, envía mensajes a los vecinos para calcular cada ruta. _AODV_ evita la problemática que tiene _DYMO_, pero por el contrario incrementa la latencia en el primer paquete a enviar cada vez que se calcula la ruta.


En la siguiente figura [11] podemos observar las diferentes versiones de _AODVv2_ y _DYMO_.

![draft version](imple_pic/aodv-versions.png "draft version")

En **Locha Mesh**, nos centramos en la evolución de _AODV_ llamado _AODVv2_, pero simplemente hablaremos de _AODV_ para referirnos a la versión más actual del protocolo de enrutamiento.

_AODV_ es uno de los cuatro protocolos estandarizados por el grupo de trabajo _IETF MANET_. El protocolo encuentra rutas alternativas bajo demanda siempre que sea necesario, lo que significa que primero establece una ruta entre un nodo de origen y un destino (descubrimiento de ruta), y luego mantiene una ruta entre los dos nodos durante los cambios de topología (mantenimiento de la ruta).

Las versiones más recientes aplican [13] más restricciones para actualizar la tabla de enrutamiento y de esta manera garantizar la libertad del bucle.

Mantiene como máximo dos rutas para cada destino: mientras una es inválida y la otra no está confirmada.

Para evitar bucles en esta versión, una ruta entrante actualiza la ruta existente con el mismo estado. La tabla de enrutamiento siempre mantiene las mejores rutas para cada estado.

## 8.1 Características.

<h2>Las características del protocolo son:</h2>

<ol>
 <li>[7] Señalización de control baja. </li>
 <li>Señalización de procesamiento mínima.</li>
 <li>Prevención de bucles.</li>
 <li>Funciona sólo con enlaces bidireccionales.</li>
</ol> 

Cada nodo tiene asociada una tabla de enrutamiento que utiliza para poder enlazar con otros nodos. Estas tablas de enrutamiento contienen los siguientes campos: 

<ol>
 <li>Dirección IP origen</li>
 <li>Tiempo de vida (TTL) </li>
 <li>Dirección IP destino</li>
 <li>Número secuencia destino</li>
 <li>Contador de saltos (hop count)</li>
</ol>

Aparecen los campos de las direcciones IP del origen y del destino para saber en todo momento el origen los paquetes y su destino.

También aparece un campo con el número de secuencia del nodo de destino que sirve para distinguir entre información nueva e información antigua y de esta forma evitar formación de bucles y transmisiones de rutas antiguas. 

Otro parámetro que se almacena en las tablas es el tiempo de vida. Sirve para evitar que viajen paquetes perdidos por la red y utilizar enlaces de los que no se conoce su estado desde hace mucho tiempo. 

Cuando a un destino le llegan dos paquetes desde la misma fuente por caminos distintos, el campo _hop count_ muestra el número de saltos que han tenido que hacer para cada una de las rutas. De esta forma se sabe cuál de ellas es la ruta más corta y que por tanto tiene que seleccionarse para hacer el envío de información. 

Cada vez que se quiere comunicar un origen con un destino se inicia un proceso de descubrimiento de ruta, que finaliza cuando recibe un paquete con la ruta calculada.

Existe otro concepto conocido como "mantenimiento de ruta", que sirve para actuar en caso de que se rompa un enlace a lo largo de una ruta. Esto se consigue dando tiempo de vida (TTL) a las rutas descubiertas antes de considerarlas como inválidas.


## 8.2 Descubrimiento de rutas. 
[7] Cuando un nodo quiere transmitir un paquete a un destino, lo primero que debe hacer es buscar en su tabla de rutas y ver si existe una hacia este destino, previamente calculada. En el caso de encontrarla no iniciaría ningún proceso de descubrimiento de ruta, supondría que la que tiene almacenada en su tabla de rutas es correcta y está actualizada. En el caso contrario, comenzará el proceso de descubrimiento para encontrar un nuevo camino válido. 

El proceso comienza con el envío de un paquete _RREQ_ (Route Request) en modo _broadcast_. Este paquete llega a los nodos vecinos que se encuentran a un salto de distancia y éstos a su vez lo reenvían a sus vecinos y así sucesivamente hasta llegar al destino. 

Cualquier nodo que durante el proceso de búsqueda conozca la ruta hacia el destino, puede contestar con un paquete de _RREP_ (Route Reply) al nodo origen indicando la ruta que necesita. Mientras se va realizando el proceso de búsqueda, todos los nodos van actualizando las tablas de enrutamiento.

En el formato del paquete _RREQ_ del protocolo _AODV_, nos encontramos los siguientes campos: 

<ol>
 <li>Dirección IP origen.</li>
 <li>Número de secuencia del origen.</li>
 <li>Dirección IP del destino.</li>
 <li>Número de secuencia del destino.</li>
 <li>RREQ identificador.</li>
 <li>Contador de saltos (hop count).</li>
</ol>

Uno de los campos es el identificador que se va modificando cada vez que se genera un envío de _RREQ_. Esto sirve para que los nodos que lo vayan recibiendo (nodos intermedios) sepan si el paquete es idéntico al anterior (tiene el mismo identificador) y deben descartarlo, o por el contrario, si deben retransmitirlo (porque el identificador de paquetes es distinto). 

<p>
<img src="imple_pic/RREQ-table-route.png" alt="drawing" height="250" width="350" align="left"/>
</p>


En esta figura podemos observar como el nodo **A** desea buscar una ruta hacia el nodo **I**.
El primer paso será buscar en su tabla de rutas y ver si tiene una ruta almacenada al destino. De no ser así debe iniciar un proceso de descubrimiento de ruta, donde envía un mensaje _multicast_ a todos sus vecinos al alcance de la señal de radiofrecuencia.

<br>
<br>
<br>
<br>
<img src="imple_pic/aodv-family.png" alt="drawing" height="290" width="380" align="left"/>
</p>

Comparando este proceso con la vida cotidiana sería como salir a la calle a buscar a tu hijo pero no lo ves. Lo primero que haces es gritar y esperar respuesta. Este grito representa un mensaje _multicast_ que puede ser escuchado por tus vecinos. Si ellos saben dónde está tu hijo, pueden informarte con un nuevo mensaje (_RREP_), entregando una ruta hacia donde se encuentra tu hijo. Pero si no saben dónde está, podrían iniciar un nuevo grito a sus vecinos más cercanos (_multicast_), para ver si pueden ayudar en la búsqueda y así sucesivamente hasta encontrar el destino, en este caso tu hijo.

<br>

## 8.3 Proceso de requerimiento de ruta en una red ad hoc.

Mostraremos [7] gráficamente cómo se inunda la red con mensajes de requerimiento de ruta, con el fin de encontrar un nodo destino del cual no se conoce más que la dirección IP que tiene asignada.

El protocolo _AODV_ por ser reactivo, debe esperar a que un nodo intente enviar un mensaje a otro nodo remoto. Las siguientes imágenes representan la secuencia lógica hasta llegar al nodo de  destino.

<br>

<h2> Paso 1. </h2>

<p>
<img src="imple_pic/RREQ1.png" alt="drawing" height="250" width="350" align="left"/>
</p>

- El nodo **S** desea enviar un paquete con información hacia el nodo **D**. 

- Primero debe buscar en su tabla de rutas y confirmar si la ruta al destino existe o no.

- Si la ruta hacia el destino existe,  enviará el mensaje con la información del usuario de la aplicación.
Pero si no se tiene una ruta, el nodo debe iniciar un proceso de búsqueda de ruta. 

- Este tipo de mensaje (_multicast_) será escuchado por todos sus vecinos dentro del radio de cobertura.

<br>


<h2> Paso 2. </h2>

<p>
<img src="imple_pic/RREQ2.png" alt="drawing" height="250" width="350" align="left"/>
</p>


- El nodo **S** envía un mensaje RREQ (requerimiento de ruta) a todos sus vecinos: **B**, **C** y **E**. Como se puede ver, los vecinos directos del nodo **S** no tienen información de la ruta requerida así que deben iniciar la retransmisión del _RREQ_, a sus vecinos más cercanos. Los nodos pueden recibir el mismo paquete de requerimiento de ruta desde diferentes nodos.
<br>
<br>
<br>
<br>
<br>
<br>

<h2> Paso 3. </h2>

<p>
<img src="imple_pic/RREQ3.png" alt="drawing" height="250" width="350" align="left"/>
</p>

- El **nodo H** recibe el RREQ de dos vecinos distintos, lo que podría dar lugar a una colisión.

- _AOdvv2_ maneja una tabla de mensajes de ruta [10] para verificar que un nodo nunca recreará un _RREQ_ que ya ha recreado antes, no importa de dónde venga.

<br>
<br>
<br>
<br>
<h2> Paso 4. </h2>

<p>
<img src="imple_pic/RREQ4.png" alt="drawing" height="250" width="350" align="left"/>
</p>

- El nodo **C** recibe el mensaje _RREQ_ desde **G** y **H**, pero no lo recrea, porque el **nodo C** ya ha recreado este mensaje antes.
  
- La verificación se hace por medio de la tabla de mensajes de ruta, la cual debe ser interpolada cada vez que se recibe algún mensaje tipo _RREQ_ o _RREP_.

<br>
<br>
<br>
<br>
<h2> Paso 5. </h2>

<p>
<img src="imple_pic/RREQ5.png" alt="drawing" height="250" width="350" align="left"/>
</p>

- En este caso el nodo **J** y el **K** retransmiten el paquete hacia **D**, debido a que estos nodos no se conocen el uno del otro y sus transmisiones podrían colisionar. 

- Es posible que el paquete con el RREQ no se entregue al nodo **D**, a pesar de la inundación de mensajes en la red.


<br>
<br>
<br>
<br>

<h2> Paso 6. </h2>

<p>
<img src="imple_pic/RREQ6.png" alt="drawing" height="250" width="350" align="left"/>
</p>

- El **nodo D** no recrea el paquete debido a que es el destinatario del mensaje de solicitud de ruta.

- En el proceso cada nodo intermedio debería conocer la manera de regresar al nodo que recreó el mensaje RREQ.

- Al ejecutar el _RREQ_, cada nodo involucrado en el proceso está aprendiendo una ruta inversa al nodo originador del mensaje de requerimiento de ruta.

- Cuando se empiezan a crear los mensajes _RREP_, los nodos intermedios aprenden una ruta inversa hacia el nodo originador del mensaje de _RREP_, y de esta manera se podría establecer una ruta bidireccional entre los nodos **S** y **D**.

<br>

<h2> Paso 7. </h2>

<p>
<img src="imple_pic/RREQ7.png" alt="drawing" height="250" width="350" align="left"/>
</p>

- Ya se ha completado la inundación del mensaje del _RREQ_ por toda la red.

- Los nodos que no están en la ruta de **S** o aislados de la red no recibirán el paquete, por ejemplo el **Z**.

- Los nodos que pasan a través del destinatario tampoco reciben el paquete, por ejemplo el **N**.


<br>
<br>
<br>

### 8.3.1 Ventajas de la búsqueda de rutas por inundación de la red.

Las ventajas son:

- Simplicidad.
- Más eficiente que otros protocolos cuando la frecuencia de la transmisión es suficientemente baja.
- Confiable en la entrega de paquetes.
  Los paquetes al destino podrían ser entregados por distintas rutas. 


### 8.3.2 Desventajas de la inundación de mensajes en la red.

- Potencialmente, se pueden entregar paquetes de datos a demasiados nodos que no necesitan recibirlos.
En el ejemplo anterior, **J** y el **K** pueden transmitir al **D** simultáneamente y en este caso el destinatario podría no recibir el paquete.

### 8.3.3 Entrada de ruta inversa.

Cuando un nodo intermedio recibe un mensaje _RREQ_, el nodo debe configurar una entrada en una tabla de rutas local con la siguiente información:
  - Dirección IP de la fuente del mensaje.
  - Número de secuencia de la fuente.
  - Número de saltos al nodo fuente.
  - Dirección IP del nodo del cual el RREQ fue recibido.  
  - Usando una ruta inversa un nodo puede enviar un RREP a la fuente.
  - Una entrada en la tabla de rutas también incluye un tiempo de vida (TTL) de una ruta.

Estas rutas aprendidas por medio de los mensajes _RREQ_, aún no se pueden confirmar como bidireccionales. Son enlaces que sabemos que son capaces de enviar mensajes , pero debemos asegurarnos que puede recibir también, a través de los mensajes _RREQ_ack_ o del mismo mensaje _RREP_, del cual hablaremos más adelante.

<br>

## 8.4 Mantenimiento de rutas. 
El [8] mantenimiento de las rutas de las tablas de enrutamiento es el proceso mediante el cual el algoritmo asegura que las rutas activas de la tabla siguen siendo válidas. Para realizar esta tarea se utilizan los Route Error Message (_RERR_). Estos mensajes de control los genera  _AODVv2_ cuando quiere informar a uno o varios nodos de que una o varias rutas han dejado de ser válidas. Hay tres eventos que provocan el envío de un mensaje _RERR_:

- Cuando un nodo tiene que reenviar un paquete IP pero no existe una ruta válida en su tabla de enrutamiento, el nodo enviara un _RERR_ a la fuente para informar que no existe una ruta hacia el destino.
- Cuando no se puede reenviar un mensaje _RREP_ porque la ruta hacia el generador del _RREQ_ no es válida. En este caso el nodo debe enviar el mensaje  _RERR_ hacia el generador del mensaje _RREP_ para informar que la ruta hacia el origen del mensaje _RREQ_ no es válida.
- Cuando un nodo detecta que uno de los enlaces de un nodo vecino se ha roto, debe informar a todos los nodos que usan ese enlace de todas las rutas han pasado a ser invalidas.


Cuando una ruta es encontrada se le da un tiempo de vida (TTL) y se considera útil hasta que un temporizador asociado a esta ruta no expire. Esto se utiliza para no tener que iniciar un descubrimiento de ruta para cada mensaje de información que se quiere enviar. 

Durante una comunicación entre el nodo origen y el nodo destino pude ocurrir que alguno de los nodos modifique su posición. Esto puede dar lugar a que se rompa el enlace y que la ruta quede inutilizada. 
El nodo vecino al enlace roto debe ser el encargado de informar al resto de nodos sobre dicho suceso. Para ello se utiliza el envío del mensaje _RERR_. 

El mensaje viene a ser igual que el mensaje _RREP_ pero con un número de salto igual a infinito. Es decir, el nodo que detecta el enlace roto envía al origen un _RERR_ con valor de _hop count_ de valor infinito, lo que hace que cualquier otra ruta sea mejor y deban reencaminarse los paquetes por otro sitio. De esta manera, el nodo origen decide si ha terminado la comunicación con el nodo destino o si por el contrario debe iniciar un nuevo proceso de descubrimiento de ruta.

_AODV_ presenta una serie de opciones de optimización, como la posibilidad de reparar a nivel local un enlace roto que forma parte de una ruta activa. Cuando se rompe un enlace, en lugar de enviar un paquete de _RERR_ al origen, el nodo que ha detectado la rotura puede intentar repararlo localmente enviando un _RREQ_ con el número de secuencia del destino incrementado en uno hacia ese destino. 

Los paquetes de datos se quedan almacenados en este nodo esperando recibir un _RREP_ con una nueva ruta disponible hacia el destino. Si este nuevo procedimiento no tiene éxito y el _RREP_ no llega, entonces sí que será necesario informar sobre la rotura del enlace enviándole un paquete _RERR_.

## 8.5 Tabla de rutas.

Tenemos también un ejemplo para ilustrar  la tabla de rutas en cada nodo después de un requerimiento de ruta a todos los nodos

La siguiente figura muestra una topología básica de nodos y la tabla de rutas que es usada para encontrar a otros nodos.

<p>
<img src="imple_pic/RREQ-table-route.png" alt="drawing" height="250" width="450" align="center"/>
</p>


<h3>Tabla de rutas para nodo A</h3>

<div>
<table id="tblOne" style="width:100%; float:left">
 <tr>
    <th>Seq</th>
    <th>Dest</th>
    <th>Next</th>
    <th>Hop</th>
 </tr>
  <tr>
    <td>1</td>
    <td>B</td>
    <td>B</td>
    <td>1</td>
 </tr>
 <tr>
    <td>1</td>
    <td>E</td>
    <td>B</td>
    <td>2</td>
 </tr>
 <tr>
    <td>1</td>
    <td>H</td>
    <td>B</td>
    <td>3</td>
 </tr>
 <tr>
    <td>1</td>
    <td>J</td>
    <td>B</td>
    <td>4</td>
 </tr>

</table>
</div>



<h3>Tabla de rutas para nodo B</h3>

<div>
<table id="tblOne" style="width:100%; float:left">
 <tr>
    <th>Seq</th>
    <th>Dest</th>
    <th>Next</th>
    <th>Hop</th>
 </tr>
  <tr>
    <td>1</td>
    <td>A</td>
    <td>A</td>
    <td>1</td>
 </tr>
 <tr>
    <td>1</td>
    <td>E</td>
    <td>E</td>
    <td>1</td>
 </tr>
 <tr>
    <td>1</td>
    <td>H</td>
    <td>E</td>
    <td>2</td>
 </tr>
 <tr>
    <td>1</td>
    <td>J</td>
    <td>E</td>
    <td>3</td>
 </tr>

</table>
</div>


<h3>Tabla de rutas para nodo E</h3>

<div>
<table id="tblOne" style="width:100%; float:left">
 <tr>
    <th>Seq</th>
    <th>Dest</th>
    <th>Next</th>
    <th>Hop</th>
 </tr>
  <tr>
    <td>1</td>
    <td>B</td>
    <td>B</td>
    <td>1</td>
 </tr>
 <tr>
    <td>1</td>
    <td>A</td>
    <td>B</td>
    <td>2</td>
 </tr>
 <tr>
    <td>1</td>
    <td>H</td>
    <td>H</td>
    <td>1</td>
 </tr>
 <tr>
    <td>1</td>
    <td>J</td>
    <td>H</td>
    <td>2</td>
 </tr>

</table>
</div>


<h3>Tabla de rutas para nodo H</h3>

<div>
<table id="tblOne" style="width:100%; float:left">
 <tr>
    <th>Seq</th>
    <th>Dest</th>
    <th>Next</th>
    <th>Hop</th>
 </tr>
  <tr>
    <td>1</td>
    <td>A</td>
    <td>E</td>
    <td>3</td>
 </tr>
 <tr>
    <td>1</td>
    <td>B</td>
    <td>E</td>
    <td>2</td>
 </tr>
 <tr>
    <td>1</td>
    <td>E</td>
    <td>E</td>
    <td>1</td>
 </tr>
 <tr>
    <td>1</td>
    <td>J</td>
    <td>J</td>
    <td>1</td>
 </tr>

</table>
</div>


<h3>Tabla de rutas para nodo J</h3>

<div>
<table id="tblOne" style="width:100%; float:left">
 <tr>
    <th>Seq</th>
    <th>Dest</th>
    <th>Next</th>
    <th>Hop</th>
 </tr>
  <tr>
    <td>1</td>
    <td>A</td>
    <td>H</td>
    <td>4</td>
 </tr>
 <tr>
    <td>1</td>
    <td>B</td>
    <td>H</td>
    <td>3</td>
 </tr>
 <tr>
    <td>1</td>
    <td>E</td>
    <td>H</td>
    <td>2</td>
 </tr>
 <tr>
    <td>1</td>
    <td>H</td>
    <td>H</td>
    <td>1</td>
 </tr>

</table>
</div>
Hasta ahora se ha presentado una descripción general del protocolo AODVv2. Ahora vamos a detallar los procesos, estructuras de datos y mensajes implementados y testados en escenarios reales.

## 8.6 Router Client Set.
El Router Client Set es es una tabla conceptual en la cual almacenamos los clientes del router _AODV_, con el fin de limitar los mensajes de ruta que recrea el nodo (_RREQ_, _RREP_) solo a los clientes registrados en dicha tabla.

## 8.7 Neighbor Set.
La tabla Neighbor Set contiene información relativa a los routers vecinos. Se actualiza a partir de los mensajes de control. También contiene información relativa a la bidireccionalidad del enlace: una ruta solo se considerará válida cuando se confirme que el enlace es bidireccional.

## 8.8 Sequence Number.
Los números de secuencia permiten a los enrutadores _AODVv2_ determinar el orden temporal de los mensajes de descubrimiento de ruta, identificando la información de enrutamiento obsoleta para que pueda descartarse. 
Cada router _AODVv2_ debe mantener su propio Sequence Number, incluido en todos los mensajes _RREQ_ y _RREP_ creados por él.
Es necesario garantizar que el número de secuencia crece de uno en uno por cada mensaje _RREQ_ o _RREP_ creado. Si el valor llega a 65535, se debe resetear este valor a 1, el valor 0 está reservado para indicar que el número de secuencia del nodo es desconocido.
Para determinar si un mensaje de ruta está obsoleto, debe comparar el número de secuencia adjunto en el mensaje con la información existente sobre esa ruta.

## 8.9 Local Route Set.

Todos los routers _AODVv2_ deben mantener un conjunto de rutas locales, con información sobre las rutas aprendidas a partir de los mensajes de control. 
Cuando una ruta se considere válida se deberá añadir la entrada correspondiente en la tabla de rutas, y cuando una ruta pasa de válida a inválida se debe borrar la entrada correspondiente en la tabla de rutas.

## 8.10 Multicast Route Message Set.

Los mensajes _RREQ_, por defecto, son multicast y pueden ser reenviados varias veces. El _multicast route message set_ tiene como finalidad proporcionar información relativa a los mensajes _RREQ_ y _RREP_ recibidos previamente, y así poder compararlos con los mensajes de ruta recibidos y determinar si la información que contienen es antigua. Esto permite al router controlar el envío de tráfico redundante.

## 8.11 Mensajes.

Vamos a detallar los mensajes de control que el protocolo utiliza para comunicar entre los nodos la información relativa a las rutas. _AODVv2_ define 4 tipos de mensajes de control:

- Route Request (_RREQ_).
- Route Reply (_RREP_).
- Route Reply Acknowledgement (_RREP_Ack_).
- Route Error (_RERR_). 

### 8.11.1 Contenido del mensaje de requerimiento de ruta RREQ.

<p> 
 <img src="imple_pic/RREQMSG.svg" alt="drawing" height="260" width="280" align="left"/>


- **msg_hop_limit**: Contiene un número entero que decrece en 1 cada salto que atraviesa el mensaje _RREQ_. El _RREQ_Gen_ establece el número máximo de saltos que atravesará el mensaje _RREQ_.
- **AddressList**: Contiene _OrigPrefix_ y _TargPrefix_.
- **PrefixLengthList** (Opcional): Contiene _OrigPrefixLen_, si se omite,la longitud del prefijo (en bits) es igual a la longitud de la dirección _OrigAddr_.
- **OrigSeqNum** :Número de secuencia de _OrigPrefix_, que se incrementa como se indica [aqui](#88-Sequence-Number).
- **MetricType**: Tipo de métrica asociada con _OrigMetric_.
- **OrigMetric**: El valor de la métrica asociada a la ruta a _OrigPrefix_.
</p>

<br></br>

### 8.11.2 Contenido del mensaje de respuesta de ruta RREP.

<p> 
 <img src="imple_pic/RREPMSG.svg" alt="drawing" height="260" width="280" align="left"/>

- **msg_hop_limit**: msg_hop_limit: Contiene un número entero que decrece en 1 su valor en cada salto que atraviesa el mensaje _RREP_.El _RREP_Gen_ establece el número máximo de saltos que atravesaráel mensaje _RREP_.
- **AddressList**: Contiene _OrigPrefix_ y _TargPrefix_.
- **PrefixLengthList** (Opcional): Contiene _TargPrefixLen_, si se omite,la longitud del prefijo (en bits) es igual a la longitud de la dirección _TargAddr_.
- **TargetSeqNum** :El número de secuencia asociado a _TargPrefix_.
- **MetricType**: El tipo de métrica asociada a _TargMetric_.
- **TargetMetric**: El valor de la métrica asociada con la ruta a _TargPrefix_.
</p>
<br></br>

### 8.11.3 Contenido del Mensaje RREP_Ack.
<p> 
 <img src="imple_pic/ACKMSG.svg" alt="drawing" height="60" width="280" align="left"/>

- **AckReq** (Opcional): Si se incluye, informa al receptor que debe enviar un mensaje _RREP_Ack_ para confirmar la bidireccionalidad del enlace
</p>
<br>


### 8.11.4 Contenido del Mensaje RERR.

<p> 
 <img src="imple_pic/RERRMSG.svg" alt="drawing" height="220" width="280" align="left"/>

- **PktSource** (Opcional:) Representa la dirección IP del paquete que disparo el RERR.
- **AddressList**: Las direcciones de las rutas que no están disponibles.
- **PrefixLengthList**: El tamaño del prefijo en bits, asociado con las rutas que no están disponibles o enlaces rotos.
- **SeqNumList**: La secuencia de número conocida del link roto.
- **MetricTypeList**: El tipo de métricas asociadas a las rutas que ya no estan disponibles.



El mensaje Route Error (_RERR_) es utilizado para informar a los nodos que no existe una ruta. Este tipo de mensajes hace parte del mantenimiento de rutas y cada nodo logra actualizar sus tablas de rutas y eliminar rutas obsoletas o enlaces rotos, con la información de este tipo de mensajes

<br>
<br>


## 8.12 Procesos del protocolo AODVv2

Vamos a ver una descripción de cada proceso del protocolo. 

### 8.12.1 Next Hop Monitoring.

Este proceso tiene como finalidad [10] asegurar que no se establecen rutas a través de enlaces unidireccionales. Para ello los routers _AODV2_ deben verificar la bidireccionalidad del enlace con el siguiente salto antes de marcar una ruta como válida en el Local Route Set.

- Para comprobar si un enlace es bidireccional con un router upstream se utiliza el mensaje de control _RREP_Ack_. Al enviarlo, se espera otro _RREP_Ack_ como respuesta. Si este mensaje llega en un tiempo menor a _RREP_Ack_SENT_TIMEOUT_ demuestra que el enlace es bidireccional, en caso contrario el enlace se considera unidireccional.
- Para un router _downstream_, el hecho de recibir un mensaje _RREP_ que contiene en el campo _TargAddr_ la dirección destino de una solicitud de ruta, es una confirmación de que el enlace está activo y es bidireccional, ya que un mensaje _RREP_ requiere que un mensaje _RREQ_ previamente haya recorrido el enlace en dirección contraria.

### 8.12.2 Neighbor Set Update.

Este proceso tiene como finalidad [10] la de actualizar la tabla Neighbor Set. Cuando se recibe un mensaje de control se inicia el proceso para actualizar la tabla Neighbor Set. Esto permite registrar los vecinos del router _AODVv2_ y establecer la relación que mantiene con cada una de ellos. 

- Cuando un router recibe un mensaje _RREP_ y se esperaba su recepción,el enlace con el router que ha enviado el paquete es confirmado como bidireccional, y por lo tanto el estado de la entrada correspondiente de la Neighbor Set cambia a Confirmed. 
- Cuando un router recibe un mensaje _RREP_Ack_ y éste es debido al envío de un _RREP_Ack_ con _AckReq_, el enlace es confirmado como bidireccional y se tiene que actualizar la tabla Neighbor Set.


Para una descripción detallada de los procesos involucrados en la búsqueda y mantenimiento de rutas, pueden dirigirse a las especificaciones más actualizadas del protocolo [10].

## 8.13 Procesamiento de la información de los mensajes de ruta.

En todos los mensajes de ruta [10] hay información: los _RREQ_ contienen la ruta hacia _OrigPrefix_, y los _RREP_ hacia _TargPrefix_. Esta información se almacena en Local Route Set. 

Como paso previo al proceso de evaluación, se convierten las estructuras de los mensajes _RREQ_ y _RREP_ a una estructura tipo _AdvRte_, común para ambos. Esto facilita el proceso de desarrollo reduciendo el número de funciones a implementar.


### 8.13.1 Evaluación de la información de ruta.

Este proceso tiene como finalidad [10] evaluar si la información de la ruta que contiene el _AdvRte_ se utilizará para actualizar la tabla Local Route Set. Para ello se compara el coste y el número de secuencia del _AdvRte_ con la entrada correspondiente en la tabla Local Route Set.

### 8.13.2 Actualización de la información de las rutas.

Después de determinar [10] que el _AdvRte_ se utilizará para actualizar la tabla Local Route Set. Este proceso se encarga de añadir una nueva entrada o actualizar una existente.

### 8.13.3 Eliminación de los mensajes redundantes usando la Multicast Route Message Set.

Cuando los mensajes de ruta inundan una _MANET_, un nodo podría recibir varias veces el mismo mensaje de ruta. Si no se evita, parte de estos mensajes serán reenviados generando tráfico innecesario.

Para solucionar este problema cada router _AODVv2_ almacena información de los mensajes de ruta que recibe en la tabla Multicast Route Message Set.

Cada vez que se recibe un mensaje _RREQ_ o _RREP_,se consulta en la tabla Multicast Route Message Set si la información que contiene el mensaje entrante es redundante o no.

A partir de esto se toma la decisión si el mensaje es reenviado o no.

### 8.13.4 Creación de mensajes RREQ.
- Un mensaje _RREQ_ se genera cuando un Client Router registrado en la tabla *Local Route Set* de un router _AODVv2_ quiere enviar un paquete IP y no existe una ruta hacia al destino en su tabla _RIB_. Tras configurar los parámetros descritos [aquí](#8111-Contenido-del-mensaje-de-requerimiento-de-ruta-RREQ), se procede a su envío. La dirección IP de destino del paquete que contiene el mensaje _RREQ_ será la dirección *multicast* LL-MANET-Routers (`FF02:0:0:0:0:0:0:6D`) para IPV6 específicada por el [RFC 5498](https://tools.ietf.org/html/rfc5498).

- Antes de crear un _RREQ_, el enrutador debe verificar si recientemente se envió un _RREQ_ para el destino solicitado. Si es así, y aún no se ha alcanzado el tiempo de espera para una respuesta, el enrutador debe continuar esperando una respuesta sin generar un nuevo _RREQ_. 

- Si se ha alcanzado el tiempo de espera, puede generar un nuevo _RREQ_. Si se configura el almacenamiento en buffer, el paquete IP entrante debe almacenarse en buffer hasta que se complete el descubrimiento de ruta.

- Si se ha alcanzado el límite permitido de mensajes de control _AODVv2_, no debe generar ningún otro mensaje. 

- Si se acerca al límite, el mensaje debe enviarse si las prioridades en la Sección [7.5](https://datatracker.ietf.org/doc/draft-perkins-manet-aodvv2/?include_text=1) lo permiten.

- Este mensaje se usa para crear el mensaje [RFC5444] correspondiente (Sección[8](https://datatracker.ietf.org/doc/draft-perkins-manet-aodvv2/?include_text=1)), que se entrega al multiplexor RFC5444 para su posterior procesamiento.

- Por defecto, el multiplexor tiene instrucciones para el manejo de mensajes de multidifusion en _LL-MANET-Routers_


Para generar un mensaje _RREQ_ el router _AODVv2_ debe seguir estos pasos:

1. Set **msg_hop_limit** = MAX_HOPCOUNT
2. Set **msg_hop_count** = 0, if including it
3. Set **AddressList** = {OrigAddr, TargAddr}
4. Para PrefixLengthList:
 - Si **OrigAddr** es parte de un rango de direcciones configuradas como cliente del router, set 
 - PrefixLengthList = {OrigPrefixLen, null}.
 - en caso contrario omitir PrefixLengthList. 
5. Para OrigSeqNum:
 - Incrementar la secuencia de número del router como se especifica en la sección [4.4](https://datatracker.ietf.org/doc/draft-perkins-manet-aodvv2/?include_text=1).
 - Set OrigSeqNum = SeqNum. 
6. Para TargSeqNum:
 - Si existe una ruta no válida que coincida con TargAddr utilizando la coincidencia de prefijo más larga y tenga un número de secuencia válido, establecer TargSeqNum.
 - TargSeqNum = número de secuencia de la ruta almacenada.
 - Si no existen rutas inválidas que coincidan con la dirección de destino, o la ruta no tiene un número de secuencia, se omite el TargetSeqNum. 
7. Incluya el elemento de datos MetricType y establezca el tipo acorde o comparable con metricType.
8. Establecer OrigMetric = Route[OrigAddr]. Metric, es decir RouterClient.Cost.
9. Incluir el elemento de datos ValidityTime si anuncia que la ruta a OrigAddr a través de este enrutador se ofrece por un tiempo limitado, y configure ValidityTime en consecuencia.

### 8.13.5 Recepción de mensajes RREQ.
Este proceso se encarga de realizar las operaciones cuando un router _AODVv2_ recibe un mensaje _RREQ_. Entre ellas: 
- Comprueba que los contenidos de los campos son válidos.
- Actualiza las tablas Neighbor Set, Local Route Set y Multicast Route Message Set. 
- Por último si la solicitud de descubrimiento de ruta va dirigida a él, envía un mensaje _RREP_. Si no es así reenvía el mensaje _RREQ_.

### 8.13.6 Reenvío de mensajes RREQ.
Un mensaje _RREP_ se genera cuando un nodo recibe un _RREQ_ y el campo _AddressList_. El _TargPrefix_ del mensaje coincide con una entrada de la tabla Router Client Set del router. Cuando esto sucede genera un mensaje _RREP_ configurando los campos descritos [aquí](#8112-Contenido-del-mensaje-de-respuesta-de-ruta-RREP) y lo envía en dirección al _RREQ_Gen_
con el contenido del mensaje de requerimiento de ruta RREQ.


### 8.13.7 Recepción de mensajes RREP.
Este proceso se encarga de realizar las operaciones cuando un router _AODVv2_ recibe un mensaje _RREP_. Entre ellas comprueba que los contenidos de los campos son válidos, actualiza las tablas Neighbor Set, Local Route Set y Multicast Route Message Set. Si el destino final del mensaje es el propio router, y el mensaje contiene una ruta válida se da por finalizado el proceso de descubrimiento de ruta, añadiendo la entrada correspondiente a la tabla de enrutamiento.


### 8.13.8 Reenvío de mensajes RREP. 
Este proceso tiene como finalidad el reenvío de los mensajes _RREP_. Para ello comprobará si no se ha superado el número de saltos máximo. Si es así reenvía el mensaje.

### 8.13.9 Generación de mensajes RREP_Ack.
El acuse de recibo de respuesta de ruta se utiliza como solicitud y como
mensaje de respuesta para probar la bidireccionalidad de un enlace a través del cual la respuesta de ruta también se ha enviado.

### 8.13.10 Recepción de mensajes RREP_Ack.
Cuando un router _AODVv2_ recibe un _RREP_Ack_, comprobará si el mensaje contiene un _AckReq_ y si el mensaje era esperado o no. Si el mensaje contiene
un _AckReq_ iniciará el proceso para enviar una respuesta del tipo _RREP_Ack. Si no es así y el mensaje era esperado actualizará la tabla Neighbor Set para establecer el enlace con el emisor del _RREP_Ack_ como bidireccional.

### 8.13.11 Generación de RREP_Ack Response.
Un router _AODvv2_ generará un _RREP_Ack Response_ cuando reciba un _RREP_Ack_ que contenga un _AckReq_.
