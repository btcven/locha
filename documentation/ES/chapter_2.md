# 2. Trabajo propuesto

En el presente trabajo se pretende como objetivo principal desarrollar un dispositivo electrónico capaz de comportarse como un nodo de de la red LOCHA-MESH, dotado con un módulo de radiofrecuencia que puede interconectar nodos lejanos con distancias alrededor de los 6 Km, el dispositivo también posee un módulo Wi-Fi que puede comportarse como un modem Wi-Fi, servidor web, cliente web, servidor de websockets, cliente websocket, entre otras bondades que lo adecuan a las necesidades de este proyecto.
Como segundo objetivo implementar AODVv2, para que se adecue a las necesidades de la red Locha y pensando siempre en la seguridad y privacidad de la información que fluye por la red.
El dispositivo debe ser capaz de comportarse como un cliente y un servidor de la red, debe ser capaz de comportarse como un router y conocer muy bien información relevante de los nodos vecinos que conforman la red.

## 2.1 ¿Qué es Locha Mesh?

Locha Mesh es una iniciativa para crear una red en malla completamente open source.

## 2.2 ¿Por qué una red en malla?

Este tipo de red se ajusta a la necesidad actual de interconectar nodos que usualmente están en movimiento y debido a esto, la topologia de la red es de tipo cambiante o dinámica, por lo que se opta por desplegar una red con topologia en malla para disminuir los puntos de falla y dependencias en algún tipo de infraestructura, ya que la idea detrás de esto es evitar las infraestructuras e imposibilitando cualquier tipo de censura a la red o manipulación de la misma.  

## 2.3 ¿Por qué no usar 2.4 GHz como frecuencia base en la transmisión de paquetes?

Si analizamos el espectro electromagnético podemos ver que la banda de 2.4 Ghz es en la actualidad muy usada para las interconexiones de internet a través del medio inalámbrico, es por esta razón que se decide usar el subgigahercio (915Mhz), como frecuencia base, además de que este tipo de frecuencias tienen un mejor desempeño en zonas pobladas, debido a que la longitud de onda de la señal de RF es mayor cuando su frecuencia es menor, lo que quiere decir que son inversamente proporcionales, si la frecuencia aumenta la longitud de onda disminuye y viceversa.


## 2.4 Por que la red Locha usa el stack de red?

Locha Mesh intenta adherirse a los estándares de red existentes para maximizar interoperabilidad, a partir de un transceptor de radio IEEE 802.15.4g compatible con 6LoWPAN; Lo que da la posibilidad de ser independiente de internet, pero ofreciendo la posibilidad de interconectar la red Locha con la red de internet.
