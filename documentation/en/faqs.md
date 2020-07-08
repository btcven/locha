# FAQ

Welcome to the FAQ of Locha Mesh! Please note that some questions may redirect you to our Documentation for further explaining. 

#### What is a mesh network?
A mesh network is a network topology in which all of the participants, known as nodes, connect to each other in order to be able to transmit (route) data from/to another node. The connection is dynamic and can be composed of any number available of devices speaking the same protocol.


#### What is Locha Mesh?
Locha Mesh is an open-source software and hardware project developing an alternative and resilient data transmission technology for communications and payments without having to rely on an Internet connection, using the mesh network topology to enable direct p2p connections between nodes. The Locha Mesh has full IPv6 support so most of the current applications can run on it.


#### Why is Locha Mesh licensed under the Apache License 2.0?
We chose the [Apache License 2.0](https://github.com/btcven/locha/blob/master/LICENSE) because it has some advantages fit for this project, it is one of the licenses approved by the [Open Source Initiative](https://opensource.org/licenses), and it's also widely used in the industry. The Apache License 2.0 allows any user to use our code freely (giving proper attribution) and modify it. Also, users of the source code are not forced to contribute back the changes they make or to publish them.

#### What is a ‘Turpial’ node?
A Turpial node is a portable router which can have smartphones or computers connected to it using its WiFi hotspot or USB in order to send data over the Locha Mesh to other devices, doing hops from one to another until it reaches the destinatary. A Turpial can connect any device to the Locha Mesh network.


#### What is a ‘Harpia’ node?
A Harpia node is a Locha Mesh standalone node which can provide services on the network such as an Internet gateway, Bitcoin transactions broadcast, latest blocks data, Electrum Server, a remote monerod, or any other. This device can have a larger antenna plugged, a power amplifier, or even a satellite dish, extending the transmission range in several kilometers. 


#### How does the Locha Mesh work?
The Locha Mesh works by having multiple nodes collaborating with each other on the network, each node implements a routing protocol which makes all nodes connected to the network available through multiple hops. We use IEEE 802.15.4g radios, and a 6LoWPAN/IPv6 stack.

In order to route packets over the mesh, we implemented AODVv2 protocol which only finds a route to a node when we ask for it, so it's relatively efficient to use. You can find more information on the routing protocol in our documentation, see [AODVv2](./chapter_08.md).

#### What can be transmitted over the Locha Mesh?
The Locha Mesh uses long-range radio connections in the ISM band (915 MHz in America, 868 MHz EU), in this band you can transmit encrypted data at a theoretical speed of ~200 kilobits per second (kbit/s). The data transmitted over the Locha Mesh from the LoChat mobile app or a computer can be any type of file: text, images, audio recordings, offline signed transactions, blocks data, etcetera.


#### How far in kilometers (km) can you transmit data over the Locha Mesh?
Turpial nodes are able to reach other Turpial devices in 1-2 km radius in urban areas. Take into account that electromagnetic noise, obstacles between antennas, antenna altitude or antenna type can affect the coverage range.


#### Is the data ‘encrypted’? What encryption method the Locha Mesh protocol uses?
The Locha Mesh protocol doesn’t make a distinction between encrypted data or plain text. Data encryption happens at the app level, a chat app using the Locha Mesh protocol can encrypt the information before sending it to the network, or leave it unencrypted for anyone to see it.


#### What radio frequencies can a Turpial node or a DIY version of the Turpial use and why?
The Turpial device uses a 915 MHz radio module, other versions of the same Turpial device could work with different radio modules for the 868 MHz band or 433 MHz band for different regions.


#### Why do we need a decentralized mesh network?
Communications over the Internet or phone could stop working during power outages, infrastructure failures, itself, natural disasters, government’s censorship, or the lack of infrastructure itself. These situations happen regularly in many places around the globe and there are no alternatives for communications in place.  

Using the Internet today seems simple and innocent, but in reality, the Internet is plagued with privacy invasive apps, surveillance, blockades, cuts, and also websites, ISPs, governments and even algorithms spying on your activity on the Internet and social media platforms. Also, in order to have Internet at home or mobile data in your phone, you must provide your personal information. If you are travelling and you need a SIM card of the country you’re currently visiting, you must provide your ID or even scan your face otherwise it will not be activated.

This can be very dangerous if you are a political dissident, or even if you are just expressing your views in a hostile place, or denouncing abuses, it could get you in trouble, jailed or worse as they already have the information they need and the way to find you (your phone data connection). 

We believe these situations can be avoided and that communications must be private and safe. To read more on this, see our [Motivation](./chapter_01.md).


#### Where can I buy a Locha Mesh device?
The Turpial device by Locha Mesh can currently be pre-ordered by making a donation [here](https://locha.io/#pre-order).


#### Can other devices join the Locha Mesh network?
Yes! The Locha Mesh protocol is hardware agnostic. This means that there are no “exclusive closed-source” specs for a device to be a node inside the Locha Mesh, other than what we have specified on the guides for a [DIY version of the Turpial for the Locha Mesh](./diy_turpial.md).


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
