# Preguntas Frecuentes

¡Bienvenido al FAQ de Locha Mesh! Tenga en cuenta que algunas preguntas pueden redirigirle a nuestra Documentación para más información.

#### ¿Qué es una red mesh?
Una red en malla (mesh network) es una topología de red en la que todos los participantes, conocidos como nodos, se conectan unos a otros para transmitir (enrutar) datos desde o hacia otro nodo. La conexión es dinámica y puede estar compuesta por cualquier número disponible de dispositivos que hablan el mismo protocolo.


#### ¿Qué es Locha Mesh?
Locha Mesh es un proyecto de software y hardware de código abierto desarrollando una tecnología resiliente de transmisión de datos para comunicaciones y pagos sin tener que depender de una conexión a Internet, usando la topologia de red en malla para habilitar conexiones p2p directas entre nodos. Locha Mesh tiene soporte completo para IPV6 por lo que la mayoría de las aplicaciones actuales pueden ejecutarse en ellas.


#### ¿Por qué Locha Mesh está licenciada bajo la Apache License 2.0?
Elegimos la [Licencia Apache 2.0](https://github.com/btcven/locha/blob/master/LICENSE) porque tiene algunas ventajas adecuadas para éste proyecto, es una de las licencias aprobadas por la [Open Source Initiative](https://opensource.org/licenses), también es ampliamente utilizada en la industria. La licencia Apache 2.0 permite a cualquier usuario utilizar libremente nuestro código (mencionando la fuente) y modificarlo. Además, los usuarios del código fuente no están obligados a contribuir de vuelta los cambios que hacen o a publicarlos.

#### ¿Qué es un nodo 'Turpial'?
Un nodo Turpial es un router portátil que puede tener telefónos móviles o computadoras conectados a él, usando su hotspot de WiFi o USB para enviar datos sobre Locha Mesh a otros dispositivos, haciendo saltos de uno a otro hasta alcanzar al destinatario. Un Turpial puede conectar cualquier dispositivo a la red de Locha Mesh.


#### ¿Qué es un nodo 'Harpia'?
Un nodo Harpia es un nodo independiente de Locha Mesh que puede proveer servicios en la red como puente a Internet, transmisión de transacciones de Bitcoin, los datos de los últimos bloques, servidor de Electrum, monerod remoto, u otros. Este dispositivo puede tener una antena más grande conectada, un amplificador de potencia, o incluso un plato de satélite, que amplía el rango de transmisión en varios kilómetros.


#### ¿Cómo funciona Locha Mesh?
Locha Mesh funciona teniendo múltiples nodos colaborando entre sí en la red, cada nodo implementa un protocolo de enrutamiento lo cual hace que todos los nodos conectados a la red estén disponibles a través de múltiples saltos. Utilizamos radios IEEE 802.15.4g, y el stack de red 6LoWPAN/IPv6.

Para enrutar los paquetes sobre la mesh, implementamos el protocolo AODVv2 que solo encuentra una ruta a un nodo cuando se lo pedimos, por lo que es relativamente eficiente de usar. Puedes encontrar más información sobre el protocolo de enrutamiento en nuestra documentación, dirígete a [AODVv2](./chapter_08.md).

#### ¿Qué se puede transmitir a través de Locha Mesh?
Locha Mesh utiliza conexiones de radio de larga distancia en la banda ISM (915 MHz en América, 868 MHz Europa), en esta banda puedes transmitir datos encriptados a una velocidad teórica de ~200 kilobits por segundo (kbit/s). Los datos transmitidos por Locha Mesh desde la aplicación móvil LoChat, o una computadora pueden ser cualquier tipo de archivo: imágenes, grabaciones de audio, transacciones firmadas sin conexión, datos de bloques, etc.


#### ¿Qué tan lejos en kilómetros (km) puedes transmitir datos por Locha Mesh?
Los nodos Turpial son capaces de alcanzar otros nodos Turpial en un radio de 1 a 2 km en zonas urbanas. Tenga en cuenta que el ruido electromagnético, obstaculos entre antenas, altitud de la antena o el tipo de antena puede afectar el rango de cobertura.


#### ¿Los datos están 'cifrados'? ¿Qué método de cifrado utiliza el protocolo Locha Mesh?
El protocolo de Locha Mesh no distingue entre datos cifrados o texto plano. El cifrado de datos ocurre en el nivel de la aplicación, una aplicación de chat usando el protocolo Locha Mesh puede cifrar la información antes de enviarla a la red, o dejarla sin cifrar para que cualquiera lo vea.


#### ¿Qué frecuencias de radio puede un nodo Turpial o una versión DIY del Turpial usar y por qué?
El dispositivo Turpial utiliza un módulo de radio de 915 MHz, otras versiones del mismo dispositivo Turpial podrían funcionar con diferentes módulos de radio para la banda 868 MHz o 433 MHz para diferentes regiones.


#### ¿Por qué necesitamos una red mesh descentralizada?
Las comunicaciones por Internet o teléfono podrían dejar de funcionar durante apagones, fallas de infraestructura, desastres naturales, censura del gobierno o la falta de infraestructura propiamente dicha. Estas situaciones ocurren regularmente en muchos lugares de todo el mundo y no existen alternativas para las comunicaciones existentes.

Usar Internet hoy parece simple e inocente, pero en realidad, Internet está plagado de aplicaciones invasivas de la privacidad, vigilancia, bloqueos, cortes y también sitios web, proveedores de Internt (ISPs), gobiernos e incluso algoritmos espiando su actividad en Internet y plataformas de redes sociales. Además, para tener Internet en casa o datos móviles en tu teléfono debes entregar tu información personal. Si estás viajando y necesitas una tarjeta SIM del país que estás visitando, debes entregar tu documento de identidad o incluso escanear tu cara, sino no activarán el servicio.

This can be very dangerous if you are a political dissident, or even if you are just expressing your views in a hostile place, or denouncing abuses, it could get you in trouble, jailed or worse inmediately as they already have the information they need and the way to find you (your phone data connection).

Creemos que estas situationes se pueden evitar y que las comunicaciones deben ser privadas y seguras. Para leer más sobre esto, dirígete a [Motivación](./chapter_01.md).


#### ¿Dónde puedo comprar un dispositivo Locha Mesh?
El dispositivo Turpial de Locha Mesh puede actualmente pre-ordenarse haciendo una donación [aquí](https://locha.io/#pre-order).


#### ¿Pueden otros dispositivos unirse a la red de Locha Mesh?
¡Sí! El protocolo de Locha Mesh es agnóstico al hardware usado. Esto significa que no hay "especificaciones técnicas propietarias o cerradas" para que un dispositivo sea un nodo dentro de Locha Mesh, aparte de lo que hemos especificado en las guías para la versión [DIY (hazlo tú mismo) del Turpial para Locha Mesh](./diy_turpial.md).


#### How many devices running the Locha Mesh protocol are needed to create a mesh?
Two or three devices can alredy be a mesh, but it would not be a resilient communication method. The more devices there are like the Turpial, the DIY version, and Harpia nodes, the better the Locha Mesh will be because it will provide more routes and hops for data to be transmitted over.


#### Do I need to be ‘on-line’ at all times to receive messages?
Yes. The Locha Mesh is completely peer-to-peer, there are no servers or intermediaries, so the messages sent to you over the Locha Mesh while you are disconnected are not stored anywhere waiting for you to connect again. If a user is online, your message will send back an ACK response (acknowledgement response) when has been received by the other party, but if the user is offline you’ll receive a NACK response (negative acknowledgement response).

For these situations, other nodes can work as ‘servers’ and store messages for an amount of time and then try to deliver them to you in a later time. The owner of the ‘server’ node may charge you for the service provided.


#### I want to contribute. Where do I start?
Thank you for joining us! Please read our [Contributing section](https://github.com/btcven/locha/blob/master/CONTRIBUTING.md) before you make a PR or open an [issue](https://github.com/btcven/locha/issues).


#### I can’t donate any money, nor code. How else can I help?
We appreciate any help effort made by anyone. You can help with [translations to other languages](https://crowdin.com/project/locha-mesh) so this project reaches more people, you can also try to assemble your own Turpial device following the [DIY instructions](./diy_turpial.md) and share your experience with us. Spreading the word and social media interactions (posts, likes, tweets, retweets) could also help us reach a bigger audience and attract more collaborators.


### Can’t find an answer to your question?
If you have a question [send us an email](mailto:contacto+lochameshquestion@bitcoinvenezuela.com) describing the matter and we’ll get back to you as soon as possible. If you'll like to suggest a question to be added to this FAQs, please, open an issue and make a pull request with your suggestion so we can review it.

You can also join the discussions on our public channels. We’d love to hear feedback from the community!
