# 2. ¿Qué es Locha Mesh?

Locha Mesh es un proyecto open source de redes y comunicaciones inalámbricas resilientes para construir una red de datos libre, teniendo como principio incorruptible la seguridad y privacidad de la información que fluye por la red. Una red creada, administrada y gestionada por los propios usuarios. Locha Mesh es una red distribuida, no pertenece a nadie en particular, nos pertenece a todos. Locha Mesh ofrece acceso libre y gratuito a la red. Cualquier persona puede acceder a ella en cualquier momento, pudiendo hacer uso de la misma para encontrar al destinatario dentro de la red, previendo fallos en la conexión estableciendo diferentes rutas para enviar y recibir paquetes de información.

## 2.1 ¿Por qué una red en malla?

Este tipo de red se ajusta a la necesidad de interconectar nodos que usualmente están en movimiento y debido a esto, la topología de la red es de tipo dinámica, lo que disminuye los puntos de falla y dependencia de infraestructura, imposibilitando cualquier tipo de censura a la red o manipulación de la misma.

Los conceptos de red inalámbrica de tipo malla o Ad-hoc, han sido estudiados a lo largo del tiempo, estableciendo diversos protocolos que con mayor o menor éxito lidian con uno de sus mayores enemigos, _single-hop_ (único salto) o ¿Hay vida más allá de mis vecinos?

Hoy, su uso se ha popularizado y ha dado pie a un campo emergente, el cual ofrece una gran cantidad de aplicaciones en las que impera la necesidad de mantener dispositivos interconectados sin desplegar una red cableada, y que estos interactúen con la mayor eficiencia sin renunciar a la seguridad en la comunicación.

Este tipo de redes pueden proporcionar un medio de comunicación fiable y seguro, donde ninguna persona, empresa o administración, aún siendo partícipe activo de la misma, pueda vetar, censurar o acceder a cualquier información, a menos que de forma expresa, sea el destinatario. Otro interesante campo de aplicación es en zonas afectadas por catástrofes naturales, pobre infraestructura, o aquellas que han sufrido los efectos de conflictos bélicos, políticos, y replicable por aquellas personas que valoran su información personal.

## 2.2 ¿Por qué usar 915 MHz como frecuencia base?

La comunicación por radio frecuencia (RF) es una de las características básicas de una red de sensores inalámbrica (WSN), debido a las claras ventajas que presenta frente a otras tecnologías inalámbricas, como por ejemplo la transmisión por infrarrojos. El uso de dispositivos inalámbricos está regulado mundialmente, por lo que la mayoría de los países tiene un espacio en el espectro de radio que es de uso libre o sin licencia, es decir, que no se necesita un permiso especial para cada aplicación.

La mayoría de los productos comerciales operan en estas bandas libres, también conocidas como bandas ISM (Industrial, Scientific and Medical) para sortear los mencionados costes de las licencias. Por ser las bandas ISM de uso libre, un gran número de tecnologías inalámbricas como ZigBee, Bluetooth, WiFi, y la telefonía inalámbrica, entre otras, utilizan estas frecuencias.

Cada una de las bandas de radiofrecuencia mencionadas anteriormente, tiene una característica distintiva que la coloca en ventaja con respecto a las demás, según las necesidades de la aplicación final. Como mencionamos anteriormente, la banda de 2.4 GHz es la más utilizada, por permitir una mayor tasa de datos y por ser de uso libre mundialmente. Sin embargo, para aplicaciones de baja tasa de datos y donde el alcance sea la prioridad principal, las bandas sub-GHz parecen ser las que mejor se adaptan a estas condiciones.


Hoy, las comunicaciones de las WSNs se basan en gran medida tanto en el estándar 802.15.4 como en el protocolo ZigBee. That protocol adds features to the network that are not available in such standard, and it operates in  2.4 GHz and 868/915 MHz ISM band. ZigBee was created to proportionate a protocol for wireless networks with low energy consumption and competitive price.


El estándar IEEE 802.15.4 divide el espectro disponible de frecuencias mencionadas anteriormente en un total de 27 canales:

- Canal k=0, para la frecuencia de 868.3 MHz, para uso en países de Europa.
- Canales k=1...10, para frecuencias de 906+2*(k+1) MHz, para uso en Estados Unidos, Australia y otros pocos países.
- Canales k=11...26, para frecuencias de 2405+5*(k-11) MHz de uso global.

### 2.2.1 Comparativa: 2.4 GHz vs. 868/915 MHz

Cada una de las bandas de radiofrecuencia mencionadas anteriormente, tiene una característica distintiva que la coloca en ventaja con respecto a las demás, según las necesidades de la aplicación final. Como mencionamos anteriormente, la banda de 2.4 GHz es la más utilizada, por permitir una mayor tasa de datos y por ser de uso libre mundialmente. Sin embargo, para aplicaciones de baja tasa de datos y donde el alcance sea la prioridad principal, las bandas sub-GHz parecen ser las que mejor se adaptan a estas condiciones.

La frecuencia y el ancho del canal de transmisión están directamente relacionados con la calidad de la comunicación inalámbrica. A mayores frecuencias, hay más espectro, y por lo tanto los canales son más anchos. Por ejemplo, hay 1000 veces más espacio de espectro entre 1 y 2 GHz que entre 1 y 2 MHz. Es por esto que la banda de 2.4 GHz tiene capacidad para transmitir una mayor tasa de datos que las bandas de menor frecuencia. Sin embargo, esta capacidad de datos trae como consecuencia una disminución de la distancia de transmisión, lo que penaliza la funcionalidad de redes a altas frecuencias en entornos de áreas muy amplias.

Existen dos razones fundamentales que justifican este fenómeno: la potencia de transmisión de RF y las pérdidas por propagación. A medida que la onda de radio se propaga por el aire, su intensidad decrece, hasta el punto de no poder extraerse los datos modulados de la señal. Entonces, las señales de radio que se transmiten con una potencia mayor, viajarán más lejos antes de que se hagan demasiado débiles. Además, la señal de las ondas de radio de mayor frecuencia disminuye mucho más rápidamente.

En Europa, los dispositivos de 2.4 GHz tienen una potencia de RF regulada a un máximo de 100 mW, mientras que la potencia de los de 915 MHz es de 500 mW, lo que significa que estos últimos tienen una distancia de transmisión fiable teóricamente de cinco veces la distancia que podría alcanzar un dispositivo de 2.4 GHz.

En entornos industriales, como en los que pueden desplegarse las redes de sensores, el funcionamiento de la comunicación se ve influenciado también por la habilidad de la señal de radio de penetrar, reflejarse y curvarse alrededor de obstáculos.

Se conoce que a mayor frecuencia de la señal menor es su longitud de onda por lo que la onda de RF tiene más pérdidas al atravesar las paredes.

Teniendo todas estas consideraciones en cuenta, las frecuencias sub-GHz ofrecen la alternativa a la congestionada banda de 2.4 GHz, a la que dispositivos como routers WiFi en oficinas o casas, ordenadores y teléfonos móviles con Bluetooth activo, hornos microondas, provocan mucha interferencia en el medio.

Aún cuando la banda de 2.4 GHz es la más utilizada actualmente debido a su coste y su posibilidad de uso mundial, las bandas sub-GHz ofrecen mayor alcance, menor consumo energético y mayor eficiencia en la transmisión de datos con la respectivas restricciones geográfica y con un coste de tecnología ligeramente mayor. Sin embargo, el coste final de cada aplicación es relativo, pues aunque los módulos de radio de 2.4 GHz son más económicos, en un despliegue real se necesitará una mayor cantidad de nodos para cubrir la misma área que con módulos de 915 MHz, cuyo coste individual por nodo es superior, pero al tener un alcance significativamente mayor, el número de nodos disminuye.


## 2.3 ¿Por qué la red Locha Mesh usa el stack de red?

Locha Mesh intenta adherirse a los estándares de red existentes para maximizar su interoperabilidad a partir de un transceptor de radio IEEE 802.15.4g compatible con 6LoWPAN, lo que da la posibilidad de ser independiente de internet, ofreciendo la posibilidad de interconectar la red Locha con la red de internet.
