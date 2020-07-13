## 1. Motivación

A pesar de los grandes avances en las tecnologías de telecomunicaciones, es evidente la susceptibilidad de las mismas. No solo hablamos de tecnologías, sino de las personas, quienes son vulnerables a los efectos secundarios del aislamiento comunicacional resultante de la centralización y regulación de los estándares de comunicación global.

Los avances surgidos desde la aparición de las redes inalámbricas en 1970, su crecimiento y demanda en esta última década ha sido exponencial. Concretamente, existen dos vertientes en cuanto a redes inalámbricas se refiere, atendiendo a la arquitectura de red subyacente encontramos a las redes inalámbricas con infraestructura y las redes inalámbricas sin infraestructura.

Sobre las redes inálambricas con infraestructura podemos hablar de las redes celulares, éstas conectan de forma inalámbrica al usuario con la red telefónica básica siempre y cuando esté dentro del radio de cobertura de la red, acomodando un gran número de usuarios dentro de un área geográfica extensa empleando un espectro de frecuencia limitado; su servicio es de alta calidad, comparable con el de los sistemas telefónicos cableados. En este tipo de redes, los enrutadores o estaciones base, son fijos y cableados y las unidades móviles se conectan y se comunican con su estación base más cercana dentro de su radio de alcance. Si el dispositivo móvil sale del alcance de su estación base, se produce una transferencia o handoff desde la estación base inicial hasta una nueva.

Podemos enfocar la popularidad de las redes inlámbricas en 1980 con el despliegue de teléfonos análogos de 1G, limitados solo a un país debido a los diferentes estándares establecidos por situación geográfica. Estas condiciones impulsaron el desarrollo de la generación 2G para la decada siguiente, añadiendo fax, datos y mensajería, sin ser adoptada como estándar homogéneo a nivel mundial. Esta generación recientemente ha sido extendida a 2.5G mejorando el soporte de transmisión de datos de baja y alta velocidad. El cambio a la generación 3G determinó la adopción a un estándar globalizado, con capacidad de roaming internacional, incrementando el ancho de banda considerablemente. A pesar de estos avances, la red 3G presentó dificultades en su despliegue, también en el cumplimiento de prestaciones debido a las limitaciones en la arquitectura de su red, naciendo la red 4G, la cual se basa en una aproximación de sistemas abiertos, combinando tecnologías inalámbricas con líneas cableadas, convergiendo voz, contenido multimedia y datos sobre una misma troncal IP.

Otros ejemplos relevantes de redes inalámbricas son las redes de área local inalámbrica WLAN (Wireless Local Area Network) y Bluetooth, siendo ampliamente utilizados en millones de productos que se interconectan entre sí o con aquellos de uso diario sirviendo de puente para estos a internet.

Para servir al objetivo de Locha Mesh, las redes inalámbricas con infraestructura son inviables y físicamente vulnerables a los cambios repentinos que pueden afectar su infraestructura, como también sus limitaciones presentes en la misma y su cobertura de red, o las regulaciones mediadas por políticas restrictivas del libre uso de las redes, las cuales limitan al público el acceso a contenidos, también usado para censurar y perseguir tanto a los individuos como los contenidos solicitados por ellos, lo cual lleva nuestra investigación al segundo tipo de redes inalámbricas, sin infraestructura.

Las redes inalámbricas sin infraestructura no poseen enrutadores fijos, por tanto los integrantes de la red deben conectarse arbitrariamente entre sí. Es aquí donde encontramos las redes ad hoc, que surgen de manera espontánea, son dinámicas, y gozan por definición de capacidad de autoconfiguración. Este tipo de red permite interconectar nodos independientes, limitados en potencia de transmisión y procesamiento para proporcionar una mayor cobertura de red y capacidad de cómputo.

Los componentes de estas redes, denominados nodos, funcionan como enrutadores que descubren y mantienen las rutas de otros nodos en la red, sin que exista un acuerdo previo en cuanto al papel que cada nodo debe asumir. Por el contrario, cada nodo toma sus decisiones autónomamente basándose en la situación de la red, por ejemplo, dos ordenadores personales equipados con tarjetas de red inalámbricas pueden configurar una red independiente siempre y cuando cada uno de los equipos este dentro del radio de cobertura del otro.

Las aplicaciones de este tipo de red pueden incluir otros como las operaciones de emergencia de búsqueda y salvamento, congresos o convenciones en los que los participantes desean compartir información de manera inmediata y la adquisición de datos en terrenos inhóspitos. Las redes ad hoc inalámbricas son un tema de gran interés, desde el punto de vista de la investigación y del mundo empresarial, por la creciente demanda de conectividad _anywhere and everywhere_, que los actuales estándares no logran satisfacer dada su naturaleza centralizada.

Vivimos en un mundo interconectado, las invenciones en este campo permiten realizar grandes puentes hacia un futuro en el que las comunicaciones mantengan una relación simbiótica con la infraestructura actual, y donde la descentralización de prestaciones sea el estándar que garantice el acceso de cualquier persona a la red y los contenidos dispuestos en ella, la cual sea un medio resiliente que permita aún en situaciones extremas e impredecibles la comunicación, la cooperación y el comercio entre los individuos.

La Figura 1.1 muestra la arquitectura simplificada de una red celular frente a una red ad hoc inalámbrica.
En la Figura 1.1 (a) se representan tres estaciones base que se comunican con sus correspondientes nodos móviles. Al contrario, en la Figura 1.1 (b) se exhibe una red ad hoc inalámbrica en la que los nodos no solo pueden ser dispositivos móviles, sino que también pueden formar parte otros elementos como punto de acceso.

<figure>
  <img src="../pics/network-topology.png" alt="network-topology" />
  <figcaption>Fig.1.1</figcaption>
</figure>

## 1.2 Objetivos

El objetivo de Locha Mesh es la implementación del protocolo de enrutamiento AODVv2 para habilitar un sistema de comunicación resiliente, que comprende las siguientes fases:

1. Selección del protocolo de enrutamiento más adecuado para la transmisión de paquetes entre nodos.
2. Selección del hardware más adecuado con los recursos necesarios para ejecutar el software y almacenar los datos necesarios.
3. Implementación del protocolo AODVv2 en un entorno basado en un sistema operativo embebido, en este caso [RIOT-OS](https://www.riot-os.org/).
4. Verificar el rendimiento del protocolo AODVv2 en escenarios reales y contrastarlo con lo previsto por el marco teórico desarrollado con anterioridad.
5. Desarrollo del driver necesario para la integración con el sistema operativo con el hardware.
6. Cálculo y establecimiento de valores de constantes para el funcionamiento de la red.

Este propósito fundamental puede a su vez descomponerse en los objetivos parciales enumerados a continuación:

- Estudio teórico sobre las redes ad hoc inalámbricas y sus protocolos de enrutamiento.
- Familiarización con las herramientas de simulación.
- Determinación de los parámetros a considerar para la generación de datos.
- Implementación y ejecución de los bancos de experimentos.
- Obtención y filtrado de datos.
- Generación y análisis de resultados.
- Extracción de conclusiones.
- Redacción de la documentación.
- Selección del sistema operativo a utilizar en el proyecto que cumpla con los requerimientos necesarios, basados en criterios expuestos más adelante.


El protocolo AODVv2 enfrenta algunos desafíos para poder trabajar de manera óptima en las áreas de aplicación provistas para este tipo de redes, como son:

* Topología dinámica
* Restricciones de recursos (hardware)
* Heterogeneidad entre nodos (diferencias en el hardware)

## 1.3 Planificación
Hacemos énfasis en establecer y seguir una secuencia lógica de pasos para llevar a cabo este proyecto; el desarrollo de este proyecto se puede dividir en las siguientes subtareas:
- Preparación y planificación: Durante la fase de preparación se acudió a diferentes fuentes bibliográficas para la obtención del conocimiento necesario acerca de las redes `ad-hoc` y de los diferentes `protocolos de enrutamineto` para completar la fase de documentación necesaria.
- Diseño del Hardware necesario para completar este proyecto; el diseño del hardware a su vez se puede descomponer en varias subtareas como lo son:
  - Diseño de la fuente de voltaje que provee eficiencia energética y alimentación al circuito digital.
  - Diseño del circuito de carga para la batería.
  - Diseño del sistema de radio compatible con el estándar IEEE 802.15.4.
  - Diseño del sistema de control, el cual estará encargado de recibir información desde la aplicación móvil y transferirla al módulo de radio para su correcto manejo en la red ad-hoc.
  - Diseño del circuito impreso necesario para montar todos los componentes.
  - Test del hardware para comprobar que todos los periféricos necesarios son funcionales.
- Diseño de la aplicación móvil, la cual sirve como GUI o interface gráfica.
- Diseño del driver necesario para hacer la integración del hardware con el sistema operativo.
