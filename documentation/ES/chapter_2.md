# 2. Trabajo propuesto

En el presente trabajo se pretende como objetivo principal desarrollar un dispositivo electrónico capaz de comportarse como un nodo de de la red LOCHA-MESH, dotado con un módulo de radiofrecuencia que puede interconectar nodos lejanos con distancias alrededor de los 6 Km, el dispositivo también posee un módulo Wi-Fi que puede comportarse como un modem Wi-Fi, servidor web, cliente web, servidor de websockets, cliente websocket, entre otras bondades que lo adecuan a las necesidades de este proyecto.
Como segundo objetivo implementar AODVv2, para que se adecue a las necesidades de la red Locha y pensando siempre en la seguridad y privacidad de la información que fluye por la red.
El dispositivo debe ser capaz de comportarse como un cliente y un servidor de la red, debe ser capaz de comportarse como un router y conocer muy bien información relevante de los nodos vecinos que conforman la red.

## 2.1 ¿Qué es Locha Mesh?

Locha Mesh es una iniciativa para crear una red en malla completamente open source.

## 2.2 ¿Por qué una red en malla?

Este tipo de red se ajusta a la necesidad actual de interconectar nodos que usualmente están en movimiento y debido a esto, la topologia de la red es de tipo cambiante o dinámica, por lo que se opta por desplegar una red con topologia en malla para disminuir los puntos de falla y dependencias en algún tipo de infraestructura, ya que la idea detrás de esto es evitar las infraestructuras e imposibilitando cualquier tipo de censura a la red o manipulación de la misma. 

## 2.3 ¿Por qué 915MHZ como frecuencia base

La comunicación por radio frecuencia es una de las características básicas de una red de sensores inalámbrica, debido a las claras ventajas que presenta frente a otras tecnologías inalámbricas, como por ejemplo la transmisión por infrarrojos. El uso de dispositivos inalámbricos está regulado mundialmente, por lo que la mayoría de los países tiene un espacio en el espectro de radio que es de uso libre o sin licencia, es decir, que no se necesita un permiso especial para cada aplicación. 

La mayoría de los productos comerciales operan en estas bandas libres, también conocidas como bandas ISM (Industrial, Scientific and Medical) para evitar los mencionados costes de las licencias. Por ser las bandas ISM de uso libre, un gran número de tecnologías inalámbricas como ZigBee, Bluetooth, WiFi, y la telefonía inalámbrica, entre otras,utilizan estas frecuencias. 

El espectro de radio está divido en bandas, y a su vez cada banda se divide en canales de ancho fijo. Las bandas ISM pueden subdividirse también en dos grandes grupos: 2.4 GHz y frecuencias sub-GHz, que incluyen las bandas de 315, 433, 784, 868 y 915 MHz; La elección de un grupo u otro dependerá siempre de las características de la aplicación, entre las que cabe destacar el alcance, el consumo de energía, la tasa de datos, el tamaño de la antena, el coste, etc.


Hoy en día, las comunicaciones de las WSNs se basan en gran medida tanto en el estándar 802.15.4 como en el protocolo ZigBee. Este último añade funcionalidades a la red no disponibles con el mencionado estándar, y opera en las bandas ISM de 2.4 GHz y 868/915 MHz, ya que fue creado con la finalidad de proporcionar un protocolo para dispositivos de redes inalámbricas, con bajo consumo de energía y que resultara competitivo en precio.


El estándar IEEE 802.15.4 divide el espectro disponible de frecuencias mencionadas anteriormente en un total de 27 canales:

- Canal k=0, para la frecuencia de 868.3 MHz, para uso en países de Europa.
- Canales k=1...10, para frecuencias de 906+2*(k+1) MHz, para uso en Estados Unidos, Australia y otros pocos países.
- Canales k=11...26, para frecuencias de 2405+5*(k-11) MHz de uso global

### 2.3.1 Comparativa: 2.4 GHz vs. 868/915 MHz

Cada una de las bandas de radiofrecuencia mencionadas anteriormente, tiene una característica distintiva que la coloca en ventaja con respecto a las demás,según las necesidades de la aplicación final.Como se mencionó anteriormente, la banda de 2.4 GHz es la más utilizada, por permitir una mayor tasa de datos y por ser de uso libre mundialmente. Sin embargo, para aplicaciones de baja tasa de datos y donde el alcance sea la prioridad principal, las bandas sub-GHz parecen ser las que mejor se adapten a estas condiciones.

Es evidente que la frecuencia y el ancho del canal de transmisión están directamente relacionados con la calidad de la comunicación inalámbrica. A mayores frecuencias, hay más espectro, y por lo tanto los canales son más anchos.Por ejemplo, hay 1000 veces más espacio de espectro entre 1 y 2 GHz que entre 1 y 2 MHz.Es por esto que la banda de 2.4 GHz tiene capacidad para transmitir una mayor tasa de datos que las bandas de menor frecuencia. Sin embargo, esta capacidad de datos trae como consecuencia una disminución de la distancia de transmisión, lo que penaliza la funcionalidad de redes a altas frecuencias en entornos de áreas muy amplias.

Existen dos razones fundamentales que justifican este fenómeno: la potencia de transmisión de radiofrecuencia y las pérdidas por propagación. A medida que la onda de radio se propaga por el aire, su intensidad decrece, hasta el punto de no poder extraerse los datos modulados de la señal.Entonces, las señales de radio que se transmiten con una potencia mayor, viajarán más lejos antes de que se hagan demasiado débiles. Además, la señal de las ondas de radio de mayor frecuencia disminuye mucho más rápidamente.

En Europa, los dispositivos de 2.4 GHz tienen una potencia de RF regulada a un máximo de 100 mW, mientras que la potencia de los de 915 MHz es de 500 mW, lo que significa que estos últimos tienen una distancia de transmisión fiable teóricamente de cinco veces la distancia que podría alcanzar un dispositivo de 2.4 GHz.

En entornos industriales, como en los que pueden desplegarse las redes de sensores, el funcionamiento de la comunicación se ve influenciado también por la habilidad de la señal de radio de penetrar, reflejarse y curvarse alrededor de obstáculos.

Se conoce que a mayor frecuencia de la señal menor es su longitud de onda con lo que se dice que la onda de radio frecuencia tiene mas perdidas al atravesar las paredes.

Teniendo todas estas consideraciones en cuenta ademas de que las frecuencias sub-GHz ofrecen la alternativa a la congestionada banda de 2.4 GHz, por ejemplo por routers WiFi en oficinas o casas, ordenadores y teléfonos móviles con Bluetooth activo, hornos microondas, que provocan mucha interferencia en el medio.

Aún cuando la banda de 2.4 GHz es la más utilizada actualmente debido a su menor coste y su posibilidad de uso mundial, las bandas sub-GHz ofrecen un mayor alcance, un menor consumo energético y una mayor eficiencia en la transmisión de datos. Claro está, con la respectiva restricción geográfica y con un coste de la tecnología ligeramente mayor. Sin embargo, el coste final de cada aplicación es relativo, pues aunque los módulos de radio de 2.4 GHz son más económicos, en un despliegue real se necesitará una mayor cantidad de nodos para cubrir la misma área que con módulos de 915 MHz, cuyo coste individual por nodo es superior, pero al tener un alcance significativamente mayor, el número de nodos disminuye bastante.

## 2.4 Por que la red Locha usa el stack de red?

Locha Mesh intenta adherirse a los estándares de red existentes para maximizar interoperabilidad, a partir de un transceptor de radio IEEE 802.15.4g compatible con 6LoWPAN; Lo que da la posibilidad de ser independiente de internet, pero ofreciendo la posibilidad de interconectar la red Locha con la red de internet.
