# Capitulo 1

# 1. Introducción y Objetivos

En este capítulo se describe el contexto en el que se circunscribe el presente proyecto y sus aspectos organizativos. En primer lugar, en la Sección 1.1, se exhibe el marco en el que cobra sentido este trabajo. En la Sección 1.2, se enumeran los objetivos a cumplir para su correcta consecución. La Sección 1.3 muestra la secuencia de fases en las que se descompone el trabajo realizado y la planificación programada para cada una de ellas.


## 1.1 Motivación

Es un hecho que una de las revoluciones tecnológicas más significativas en los últimos tiempos ha sido la aparición de las redes inalámbricas. Desde su invención en los años 70, la historia de la industria de las telecomunicaciones ha sido testigo de su vertiginoso crecimiento durante la  última década.

Existen dos vertientes en cuanto a redes inalámbricas se refiere, atendiendo a la arquitectura de red subyacente. En primer lugar, se tienen las redes inalámbricas con infraestructura; dentro de este tipo de redes, uno de los ejemplos más significativos son las redes celulares.

Un sistema de telefonía celular proporciona conexiones inalámbricas a la red telefónica básica desde cualquier localización del usuario dentro del radio de cobertura de la red. Los sistemas celulares acomodan un gran número de usuarios dentro de un área geográfica extensa, empleando un espectro de frecuencia limitado. 

Los sistemas radio celulares proporcionan un servicio de alta calidad que en ocasiones es comparable con el de los sistemas telefónicos cableados. En este tipo de redes, los encaminadores o estaciones base, son fijos y cableados y las unidades móviles se conectan y se comunican con su estación base más cercana dentro de su radio de alcance. 

Cuando el dispositivo móvil sale del alcance de su estación base, se produce una transferencia o handoff desde la estación base inicial hasta una nueva, de tal modo que el dispositivo móvil es capaz de comunicarse de manera transparente a través de la red. Otros ejemplos relevantes de redes inalámbricas son las redes de área local inalámbrica (WLAN, Wireless Local Area Network).

En segundo lugar, se consideran las redes inalámbricas sin infraestructura. 

En esta clase de redes no existen encaminadores fijos y por tanto, los integrantes de la red deben conectarse espontánea y arbitrariamente entre sí. Dentro de este tipo de redes,se encuentran las redes ad hoc. Literalmente, las redes ad hoc son aquellas que surgen de manera espontánea para cubrir una necesidad de comunicación con un propósito específico. Puesto que estas redes se forman de manera dinámica, gozan por definición de capacidad de autoconfiguración. 

Los componentes de estas redes, también denominados nodos, funcionan como encaminadores que descubren y mantienen las rutas de otros nodos en la red, sin que exista un acuerdo previo en cuanto al papel que cada nodo debe asumir. Por el contrario, cada nodo toma sus decisiones autónomamente basándose en la situación de la red. 

Por ejemplo, dos ordenadores personales equipados con tarjetas de red inalámbricas pueden configurar una red independiente siempre y cuando cada uno de los equipos este dentro del radio de cobertura del otro. Otros campos de aplicación incluyen las operaciones de emergencia de búsqueda y salvamento, congresos o convenciones en los que los participantes desean compartir información de manera inmediata y la adquisición de datos en terrenos inhóspitos. 

Las redes ad hoc inalámbricas son un tema de gran interés, desde el punto de vista de la investigación y del mundo empresarial, por la creciente demanda de conectividad anywhere and everywhere, es decir, con o sin infraestructura. Mientras que las redes tradicionales se componen de nodos fijos cableados, las redes ad hoc inalámbricas pueden describirse genéricamente como redes inalámbricas multi-salto compuestas de nodos potencialmente móviles. 

Se puede definir una red ad hoc inalámbrica como una red en la que todos los nodos se conectan a través de enlaces inalámbricos sin existir un nodo central o dominante, estando todos al mismo nivel jerárquico. Las redes ad hoc permiten encadenar nodos independientes, limitados en potencia de transmisión y procesamiento para proporcionar una mayor cobertura de red y capacidad de cómputo.

La Figura 1.1 muestra de modo simplificado la arquitectura de una red celular frente a la de una red ad hoc inalámbrica. 
En la Figura 1.1 (a) se representan tres estaciones base que se comunican con sus correspondientes nodos móviles. Por el contrario, en la Figura 1.1 (b) se exhibe una red ad hoc inalámbrica en la que los nodos no solo pueden ser dispositivos móviles, sino que también pueden formar parte otros elementos como un punto de acceso.


<figure>
  <img src="imple_pic/network-topology.png" alt="drawing" height="200" width="450" align="center"/>
  <figcaption>Fig.1.1</figcaption>
</figure>


Con el fin de comprender las tendencias en tecnologías inalámbricas y entender porqué las redes ad hoc inalámbricas están preparadas para desempeñar un papel fundamental en la evolución de las redes inalámbricas del futuro, se hace necesaria una revisión sucinta de las generaciones tecnológicas en este campo. 

Se puede afirmar que la historia comercial de las redes inalámbricas comenzó entorno al año 1980 con la primera generación o 1G, en la que los teléfonos eran analógicos. Estos dispositivos solo se podían emplear dentro de un país, ya que existían diferentes estándares, dependiendo del área geográfica mundial. 

El despliegue de la segunda generación, 2G, surgió en la década de los 90, añadiendo fax, datos y capacidades de mensajería al servicio de voz tradicional. Sin embargo, tampoco se adopto como un estándar homogéneo mundial. Recientemente,2G se ha extendido a 2.5G, con el fin de mejorar el soporte de transmisión de datos de baja velocidad. Luego se hace la transición desde 2G hacia 3G, con la que se adopta un estándar global y con capacidades de roaming en todo el planeta, además de incrementar considerablemente el ancho de banda. 

Desafortunadamente, a pesar de las grandes expectativas puestas sobre 3G, estas redes experimentaron dificultades en ser desplegadas y en el cumplimiento de las prestaciones que se esperaba de ellas debido a las limitaciones de la arquitectura de la red. Por ello, nació la nueva generación 4G, una red global basada en una aproximación de sistemas abiertos. Los dos principales objetivos de las redes 4G son integrar diferentes tipos de tecnologías inalámbricas con líneas cableadas de manera transparente y la convergencia de voz, contenido multimedia y datos sobre una sola red troncal IP. Las redes ad hoc inalámbricas móviles encajan en este contexto puesto que se espera que sean una de las partes más importantes de la arquitectura de las redes 4G y 5G.

Gran parte de la investigación realizada en el campo de las redes ad hoc inalámbricas se ha centrado en los protocolos de encaminamiento. La razón principal por la que se hace necesario desarrollar protocolos eficientes para este tipo de redes es que la experiencia y el conocimiento adquiridos con las redes fijas no son aplicables a las redes ad hoc inalámbricas. Mientras que en las redes fijas los enlaces son fiables y el encaminamiento se puede estudiar independientemente de las características físicas de la red, las redes ad hoc inalámbricas no admiten esa asunción básica. Ademas, la naturaleza de las redes ad hoc inalámbricas conlleva cambios frecuentes e imprevisibles en la topología de la red, añadiendo complejidad al enrutamiento.

Estas nuevas dificultades, antes inexistentes en las redes cableadas, junto con la importancia crítica de los protocolos de encaminamiento para establecer la comunicación entre nodos, hacen que el área del encaminamiento sea una de las más activas en la investigación de las redes ad hoc inalámbricas. 

El propósito del presente trabajo se enmarca aquí, ya que ofrece un estudio sobre las prestaciones de uno de los protocolos de encaminamiento para redes ad hoc inalámbricas más ampliamente extendidos. Concretamente, se busca evaluar las prestaciones del protocolo en un ambiente real además de las simulaciones para lograr obtener resultados cuando el número de componentes de la red es elevado; puesto que el despliegue de la red real con un número considerable de nodos no es fácil en la etapa de inicio del proyecto, por esto es necesario hacer un estudio previo que sirva como base para las futuras mejoras del producto final. Por ello, la evaluación del protocolo de encaminamiento se realiza mediante herramientas de simulación que modelan el sistema real cuyo comportamiento se pretende analizar para grandes cantidades de nodos.

## 1.2 Objetivos

El objetivo de este proyecto es la implementación del protocolo de enrutamiento  **AODVv2**, que comprende las siguientes fases:

1. Selección del protocolo de enrutamiento más adecuado para este proyecto, basados en ciertos criterios expuestos más adelante.
2. Selección del hardware más adecuado con los recursos necesarios para ejecutar el software y almacenar los datos necesarios. 
3. Implementación del protocolo **AODVv2** en un entorno basado en un sistema operativo embebido, en este caso [RIOT-OS](https://www.riot-os.org/).
4. Verificar el rendimiento del protocolo **AODVv2** en escenarios reales y contrastarlo con lo previsto por el marco teórico desarrollado con anterioridad.
5. Desarrollo del driver necesario para la integración con el sistema operativo con el hardware.
6. Cálculo y establecimiento de valores de constantes para el funcionamiento de la red.

Este proposito fundamental puede a su vez descomponerse enlos objetivos parciales enumerados a continuación:

- Estudio teórico sobre las redes ad hoc inalámbricas y sus protocolos de encaminamiento.
- Familiarización con las herramientas de simulación.
- Determinación de los parámetros a considerar para la generación de datos.
- Implementación y ejecución de los bancos de experimentos.
- Obtención y filtrado de datos.
- Generación y análisis de resultados.
- Extracción de conclusiones.
- Redacción de la documentación.
- Selección del sistema operativo a utilizar en el proyecto que cumpla con los requerimientos necesarios, basados en criterios expuestos más adelante.


El protocolo AODVv2 enfrenta algunos desafíos para poder trabajar de manera optima en las áreas de aplicación provistas para este tipo de redes, como lo son:

<ul class="w3-ul w3-border">
 <li><h4>Topología dinámica.</h4></li>
 <li><h4>Restricciones de recursos (hardware).</h4></li>
 <li><h4>Heterogeneidad entre nodos (diferencias en el hardware).</h4></li>
</ul>


**LOCHA-MESH** es una iniciativa para reunir gente interesada en redes y comunicaciones inalámbricas, es un proyecto **open source** en el cual se intenta construir una red de datos libre, para así contar con un nuevo medio de comunicación abierto, libre y resiliente. Una red, en este caso inalámbrica, creada, administrada y gestionada por los propios usuarios. Una red libre mesh es distribuida, no pertenece a nadie en particular pero nos pertenece a todos. Una red libre mesh ofrece acceso libre y gratuito a la propia red. Por acceso libre se entiende que cualquier persona puede acceder a la red en cualquier momento, pudiendo hacer uso de la misma para encontrar al destinatario dentro de la red.

Los conceptos de red inalámbrica de tipo malla o Ad-hoc, han sido estudiados a lo largo del tiempo, estableciendo diversos protocolos que con mayor o menor éxito lidian con uno de sus mayores enemigos; “single-hop” (único salto) o ¿Hay vida más allá de mis vecinos?.

Hoy, su uso se ha popularizado y ha dado pie a un campo emergente, el cual ofrece una gran cantidad de aplicaciones en las que impera la necesidad de mantener dispositivos interconectados sin desplegar una red cableada y que estos interactúen con la mayor eficiencia sin renunciar a la seguridad en la comunicación.


Sin embargo, este tipo de redes también pueden proporcionar un medio de comunicación fiable y seguro, donde ninguna persona, empresa o administración, aún siendo partícipe activo de la misma, pueda vetar, censurar o acceder a cualquier información, a menos que de forma expresa, sea el destinatario. Otro interesante campo de aplicación es en zonas afectadas por catástrofes naturales, pobre infraestructura, aquellas que han sufrido los efectos de conflictos bélicos o políticos, o simplemente personas que valoran su anonimato.



## 1.3 Planificacion
Hacemos énfasis en establecer y seguir una secuencia lógica de pasos para llevar a cabo este proyecto; el desarrollo de este proyecto se puede dividir en las siguientes subtareas:
- Preparación y planificación: Durante la fase de preparación se acudió a diferentes fuentes bibliográficas para la obtención del conocimiento necesario acerca de las redes ```ad-hoc``` y de los diferentes ```protocolos de enrutamineto``` para completar la fase de documentación necesaria.
- Diseño del Hardware necesario para completar este proyecto; el diseño del hardware a su vez se puede descomponer en varias subtareas como lo son:
  - Diseño de la fuente de voltaje que provee eficiencia energética y alimentación al circuito digital.
  - Diseño del circuito de carga para la batería.
  - Diseño del sistema de radio compatible con el estándar IEEE 802.15.4.
  - Diseño del sistema de control, el cual estará encargado de recibir información desde la aplicación móvil y transferirla al modulo de radio para su correcto manejo en la red ad-hoc.
  - Diseño del circuito impreso necesario para montar todos los componentes.
  - Test del hardware para comprobar que todos los periféricos necesarios son funcionales.
- Diseño de la aplicación móvil, la cual sirve como GUI o interface gráfica.
- Diseño del driver necesario para hacer la integración del hardware con el sistema operativo.

