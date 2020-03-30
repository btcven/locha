# Capitulo 1

# 1. Introducción y objetivos

En este capitulo introductorio se describe el contexto en el que se circunscribe el presente proyecto y sus aspectos organizativos. En primer lugar, en la Sección 1.1, se exhibe el marco en el que cobra sentido este trabajo. En la Sección 1.2, se enumeran los objetivos a cumplir para su correcta consecución. La Sección 1.3 muestra la secuencia de fases en las que se descompone el trabajo realizado y la planificación programada para cada una de ellas.


## 1.1 Motivación

Es un hecho bien sabido que una de las revoluciones tecnológicas mas significativas en los últimos tiempos ha sido la aparición de las redes inalámbricas. Desde su invención en los anos 70, la historia de la industria de las telecomunicaciones ha sido testigo de su vertiginoso crecimiento durante la  ultima decada.

Existen dos vertientes en cuanto a redes inalambricas se refiere, atendiendo a la arquitectura de red subyacente. En primer lugar se tienen las redes inalambricas con infraestructura. Dentro de este tipo de redes, uno de los ejemplos mas significativos son las redes celulares. 

Un sistema de telefonía celular proporciona conexiónes inalambricas a la red telefonica básica desde cualquier localización de usuario dentro del radio de cobertura de la red. Los sistemas celulares acomodan un gran numero de usuarios dentro de un area geográfica extensa, empleando un espectro de frecuencia limitado. 

Los sistemas radio celulares proporcionan un servicio de alta calidad que en ocasiones es comparable con el de los sistemas telefónicos cableados. En este tipo de redes, los encaminadores o estaciones base, son fijos y cableados y las unidades móviles se conectan y se comunican con su estación base mas cercana dentro de su radio de alcance. 

Cuando el dispositivo movil sale del alcance de su estación base, se produce una transferencia o handoff desde la estación base inicial hasta una nueva, de tal modo que el dispositivo movil es capaz de comunicarse de manera transparente a través de la red. Otros ejemplos relevantes de redes inalambricas son las redes de area local inalambricas (WLAN,Wireless Local Area Network).

En segundo lugar se consideran las redes inalambricas sin infraestructura. 

En esta clase de redes no existen encaminadores fijos y por tanto, los integrantes de la red deben conectarse espontanea y arbitrariamente entre sı. Dentro de este tipo de redes,se encuentran las redes ad hoc. Literalmente, las redes ad hoc son aquellas que surgen de manera espontanea para cubrir una necesidad de comunicación con un propósito especifico. Puesto que estas redes se forman de manera dinámica, gozan por definición de capacidad de autoconfiguracion. 

Los componentes de estas redes, también denominados nodos, funcionan como encaminadores que descubren y mantienen las rutas de otros nodos en la red, sin que exista un acuerdo previo en cuanto al papel que cada nodo debe asumir. Por el contrario, cada nodo toma sus decisiones autónomamente basándose en la situación de la red. 

Por ejemplo, dos ordenadores personales equipados con tarjetas de red inalambricas pueden configurar una red independiente siempre y cuando cada uno de los equipos este dentro del radio de cobertura del otro. Otros campos de aplicación incluyen las operaciones de emergencia de búsqueda y salvamento, congresos o convenciones en los que los participantes desean compartir información de manera inmediata y la adquisición de datos en terrenos inhóspitos. 

Las redes ad hoc inalambricas son un tema de gran interés, desde el punto de vista de la investigación y del mundo empresarial, por la creciente demanda de conectividad any where and any way, es decir, con o sin infraestructura. Mientras que las redes tradicionales se componen de nodos fijos cableados, las redes ad hoc inalambricas pueden describirse genéricamente como redes inalambricas multi-salto compuestas de nodos potencialmente móviles. 

Se puede definir una red ad hoc inalambrica como una red en la que todos los nodos se conectan a través de enlaces inalambricos sin existir un nodo central o dominante, estando todos al mismo nivel jerárquico. Las redes ad hoc permiten encadenar nodos independientes, limitados en potencia de transmisión y procesamiento para proporcionar una mayor cobertura de red y capacidad de computo.

La Figura 1.1 muestra de modo simplificado la arquitectura de una red celular frente a la de una red ad hoc inalambrica. 
En la Figura 1.1(a) se representan tres estaciones base que se comunican con sus correspondientes nodos móviles. Por el contrario, en la Figura 1.1(b) se exhibe una red ad hoc inalambrica en la que los nodos no solo pueden ser dispositivos móviles, sino que también pueden formar parte otros elementos como un punto de acceso.


<figure>
  <img src="imple_pic/network-topology.png" alt="drawing" height="200" width="450" align="center"/>
  <figcaption>Fig.1.1</figcaption>
</figure>


Con el fin de comprender las tendencias en tecnologías inalambricas y entender porque las redes ad hoc inalambricas están preparadas para desempeñar un papel fundamental en la evolución de las redes inalambricas del futuro, se hace necesaria una revisión sucinta de las generaciones tecnológicas en este campo. 

Se puede afirmar que la historia comercial de las redes inalambricas comenzó entorno al ano 1980 con la primera generación o 1G, en la que los teléfonos eran analógicos. Estos dispositivos solo se podían emplear dentro de un país, ya que existían diferentes estándares, dependiendo del area geográfica mundial. 

El despliegue de la segunda generación, 2G, surgió en la decada de los 90, añadiendo fax, datos y capacidades de mensajería al servicio de voz tradicional. Sin embargo, tampoco se adopto como un estándar homogéneo mundial. Recientemente,2G se ha extendido a 2.5G, con el fin de mejorar el soporte de transmisión de datos de baja velocidad. Luego se hace la transición desde 2G hacia 3G, con la que se adopta un estándar global y con capacidades de roaming en todo el planeta, ademas de incrementar considerablemente el ancho de banda. 

Desafortunadamente, a pesar de las grandes expectativas puestas sobre 3G, estas redes experimentaron dificultades en ser desplegadas y en el cumplimiento de las prestaciones que se esperaba de ellas debido a las limitaciones de la arquitectura de la red. Por ello, nació la nueva generación 4G, una red global basada en una aproximación de sistemas abiertos. Los dos principales objetivos de 4G son integrar diferentes tipos de tecnologías inalambricas con lineas cableadas de manera transparente y la convergencia de voz, contenido multimedia y datos sobre una sola red troncal IP. Las redes ad hoc inalambricas móviles encajan en este contexto puesto que se espera que sean una de las partes mas importantes de la arquitectura de las redes 4G y 5G. 

Gran parte de la investigacion realizada en el campo de las redes ad hoc inalambricas se ha centrado en los protocolos de encaminamiento. La razon principal por la que se hace necesario desarrollar protocolos eficientes para este tipo de redes es que la experiencia y el conocimiento adquiridos con las redes fijas no son aplicables a las redes ad hoc inalambricas. Mientras que en las redes fijas los enlaces son fiables y el encaminamiento se puede estudiar independientemente de las caracterısticas fısicas dela red, las redes ad hoc inalambricas no admiten esa asuncion basica. Ademas, la naturaleza de las redes ad hoc inalambricas conlleva cambios frecuentes e imprevisibles en la topologıa de la red, anadiendo complejidad al enrutamineto. 

Estas nuevas dificultades, antes inexistentes en las redes cableadas, junto con la importancia crıtica de los protocolos de encaminamiento para establecer la comunicacion entre nodos, hacen que el area del encaminamiento sea una de las mas activas en la investigacion de las redes ad hoc inalambricas. 

El proposito del presente trabajo se enmarca aquı, ya que ofrece un estudio sobre las prestaciones de uno de los protocolos de encaminamiento para redes ad hoc inalambricas mas ampliamente extendidos. Concretamente, se busca evaluar las prestaciones del protocolo en un ambiente real ademas de las simulaciones para lograr obtener resultados cuando el numero de componentes de la red es elevado;Puesto que el despliegue de la red real con un numero considerable de nodos no es facil en la etapa de inicio del proyecto,por esto es necesario hacer un estudio previo que sirva como base para las futuras mejoras del producto final. Por ello, la evaluacion del protocolo de encaminamiento se realiza mediante herramientas de simulacion que modelan el sistema real cuyo comportamiento se pretende analizar para grandes cantidades de nodos.

## 1.2 Objetivos

El objetivo de este proyecto es la implementación del protocolo de enrutamiento  **AODVv2**, el cual se enmarca en uno de mayor envergadura que comprende las siguientes fases.

1. Seleccion del protocolo de enrutamiento mas adecuado para este proyecto, basados en ciertos criterios expuestos mas adelante.
2. Seleccion del hardware mas adecuado con los recursos necesarios para ejecutar el software y almacenar los datos necesarios. 
3. Implementación del protocolo **AODVv2** en un entorno basado en un sistema operativo embebido, en este caso [RIOT-OS](https://www.riot-os.org/).
4. Verificar el rendimiento del protocolo **AODVv2** en escenarios reales y contrastarlo con lo previsto por el marco teórico desarrollado con anterioridad.
5. Desarrollo del driver necesario para la integracion con el sistema operativo con el hardware.
6. cálculo y establecimiento de valores de constantes para el funcionamiento de la red.

Este proposito fundamental puede a su vez descomponerse enlos objetivos parciales enumerados a continuacion:

- Estudio teorico sobre las redes ad hoc inalambricas y sus protocolos de encaminamiento.
- Familiarizacion con las herramientas de simulacion.
- Determinacion de los parametros a considerar para la generacion de datos.
- Implementacion y ejecucion de los bancos de experimentos.
- Obtencion y filtrado de datos.
- Generacion y analisis de resultados.
- Extraccion de conclusiones.
- Redaccion de la documentacion.
- Seleccion del sistema operativo a utilizar en el proyecto que cumpla con los requerimientos necesarios, basados en criterios expuestos mas adelante.


El protocolo AODVv2 enfrenta algunos desafíos para poder trabajar de manera optima en las areas de aplicación provistas para este tipo de redes, como lo son:

<ul class="w3-ul w3-border">
 <li><h4>Topología dinámica.</h4></li>
 <li><h4>Restricciones de recursos(hardware).</h4></li>
 <li><h4>Heterogeneidad entre nodos(diferencias en el hardware).</h4></li>
</ul>


**LOCHA-MESH** es un intento para reunir gente interesada en redes y comunicaciones inalámbricas, es un proyecto **open source** en el cual se intenta construir una red de datos libre, para así contar con un nuevo medio de comunicación abierto, libre y resiliente . Una red, en este caso inalámbrica, creada, administrada y gestionada por los propios usuarios. Una red libre mesh es distribuida, no pertenece a nadie en particular pero nos pertenece a todos. Una red libre mesh ofrece acceso libre y gratuito a la propia red. Por acceso libre se entiende que cualquier persona puede acceder a la red en cualquier momento y puede llegar hasta cualquier parte de la red.

Los conceptos de red inalámbrica de tipo malla o Ad-hoc, han sido estudiados a lo largo del tiempo, estableciendo diversos protocolos que con mayor o menor éxito lidian con uno de sus mayores enemigos; “single-hop” (único salto) o ¿Hay vida más allá de mis vecinos?.

Hoy día, su uso se ha popularizado y ha dado pie a un campo emergente, el cual ofrece una gran cantidad de aplicaciones en las que impera la necesidad de mantener dispositivos interconectados sin desplegar una red cableada y que estos interactúen con la mayor eficiencia sin renunciar a la seguridad en la comunicación.


Sin embargo, este tipo de redes también pueden proporcionar un medio de comunicación fiable y seguro, donde ninguna persona, empresa o administración, aún siendo partícipe activo de la misma, pueda vetar, censurar o acceder a cualquier información, a menos que de forma expresa, sea el destinatario. Otro interesante campo de aplicación es en zonas afectadas por catástrofes naturales, pobre infraestructura, aquellas que han sufrido los efectos de conflictos bélicos o políticos, o simplemente personas que valoran su anonimato.



## 1.3 Planificacion
Es de vital Importancia establecer y seguir una secuencia logica de pasos para llevar a cabo este proyecto;El desarrollo de este proyecto se puede dividir en las siguientes subtareas:
- Preparacion y planificacion: Durante la fase de preparacion se acudio a diferentes fuentes bibliograficas para la obtencion del conocimiento necesario acerca de las redes ```ad-hoc``` y de los diferentes ```protocolos de enrutamineto``` para completar la fase dedocumentacion necesaria. 
- Diseno del Hardware necesario para completar este proyecto; El diseno del harware a su vez se puede descomponer en varias subtareas como lo son:
  - Diseno de la fuente de voltaje que provee eficiencia energetica y alimentacion al circuito digital.
  - Diseno del circuito de carga para la bateria.
  - Diseno del sistema de radio compatible conel estandar IEEE 802.15.4.
  - Diseno del sistema de control, el cual sera el encargado de recibir informacion desde la aplicacion movil y transferirla al modulo de radio para su correcto manejo en la red ad-hoc.
  - Diseno del circuito impreso necesario para montar todos los componentes.
  - Test del hardware para comprobar que todos los perifericos necesarios son fncionales.
- Diseno de la aplicacion movil,la cual sirve como GUI o interface grafica.
- Diseno del driver necesario para hacer la integracion del hardware con el sistema operativo.

