# 2. What is Locha Mesh?

Locha Mesh is an open source project of networks and wireless resilient communications in order to build a free data transmission, with a incorruptible focus in the security and the privacy of the information flowing in the network. A network created and managed by its users. Locha Mesh is a distribuited network that doesn't belong to anyone in particular but to us all. Locha Mesh offers free and permissionless access to the network. Anyone can join it at any time, and use it to find its recipient inside the mesh, finding different paths to send and receive data packets foreseeing failures in the conection.

## 2.1 Why a Mesh?

This type of network suits the need to connect nodes that are constantly moving around, due this the topology is a dynamic one, diminishing the points of failure and the dependency on fixed infrastructure, to preclude the censorship or the manipulation of the mesh.

The concepts of a wireless network or ad-hoc has been studied for a long time, creating diverse protocols that with more or less success deal with their biggest enemy, the _single-hop_ or is there life beyond my neighbors?

Today, its use is very popular and has promoted an emergent field which offers a vast amount of applications that needs to be interconnected at all times wirelessly, interacting efficiently with eachother without having to give up the security in the communication.

These networks can proportionate a safe and reliable communication system, where no person, or business can censor, veto or access any information unless it is the receipient of it. Another interesting application if the disaster hit countries, poor infrastructure, or those suffering the effects of war, pollitical repression, and its also replicable for those who value their personal information.

## 2.2 Why use 915 MHz as base frecuency?

Radio frecuency (RF) communication is a basic feature of a Wireless Sensor Network (WSN), its clearly and advantage in front of, for example, infra red transmission too. The use of wireless devices is regulated worldwide, most countries have a space in their radio spectrum for unlicensed use, meaning, it doesn't need a special permit for each application.

Most commercial products operate in these free bands, also known as ISM (Industrial, Scientific and Medical) to avoid the cost of the licenses. Because of this, a big number of wireless technologies like ZigBee, Bluetooth, Wi-Fi, and wireless telephony among others, use these frecuencies.

Radio spectrum is divided in bands, at the same time each band subdivides into fixed width channels. The ISM bands subdivides into two larger groups: 2.4 GHz and sub-GHz frecuencies, including the 315, 433, 784, 868 and 915 MHz bands. The choice between a group or another will depend on the characteristics of the application, like the range, battery consumption, data rate, size of the antenna, cost, etc.

Hoy, las comunicaciones de las WSNs se basan en gran medida tanto en el estándar 802.15.4 como en el protocolo ZigBee. Este último añade funcionalidades a la red no disponibles con el mencionado estándar, y opera en las bandas ISM de 2.4 GHz y 868/915 MHz, ya que fue creado con la finalidad de proporcionar un protocolo para dispositivos de redes inalámbricas, con bajo consumo de energía y que resultara competitivo en precio.

Currently, communication of the WNSs are based on the standard 802.15.4 like the ZigBee protocol. That protocol adds features to the network that are not available in such standard, and it operates in the ISM band of 2.4 GHz and 868/915 MHz. ZigBee was created to proportionate a protocol for wireless networks with low energy consumption and competitive price.


The standard EEEI 802.15.4 divides the available frecuency spectrum in 27 channels:

- Channel k=0, for frecuency 868.3 MHz, used for European countries.
- Channels k=1...10, for frecuencies of 906+2*(k+1) MHz, used for United States, Australia and some other countries too.
- Channels k=11...26, for frecuencies of 2405+5*(k-11) MHz worldwide used.

### 2.2.1 2.4 GHz vs 868/915 MHz

Each of those RF bands have a distinctive characteristic which gives some advantage over the others, according to the needs of the final application. As previously mentioned, the 2.4 GHz is widely used, because it allows a major data rate and its worldwide free of charge. However, for applications with low data rate, and where the reach its the major priority, sub-GHz bands seems to be a better candidate for it.

The frecuency and the bandwidth for the transmission are directly related to the quality of the wireless communication. At major frecuencies, there's more spectrum so the channels have more bandwidth. For instance, there are a 1000 times more spectrum space between 1 and 2 GHz than in 1 and 2 MHz. Hence, the 2.4 GHz band has more capacity to transmit a major data rate than the lower frecuency bands. Nonetheless, this capacity has a drawback, it decreases the distance of the transmission, which penalize the network functionality at high frecuencies in wide areas.

There are two reasons to justify this phenomenon: the power transmission of RF and the losses over propagation. As the radio wave travels over the air, its intensity decreases to the point its not possible to extract the data modulated of the signal. The radio signal transmitted with more power will travel further before they weaken too much. Besides, the signal of the wave of a major frecuency decreases very quickly.

In Europe, the devices of 2.4 GHz have a regulated RF power to maximum of 100 mW, meanwhile the power of the 915 MHz is 500 mW, which means the latter theorically can reach a reliable transmission range of five times the range a device of 2.4 GHz could reach.  

On industrial enviroments in which a WSN can be deployed, the functionality of the communication its influenced for the ability of the radio signal to penetrate, reflect and bend towards obstacles.

Consequently, in the presence of a major signal frecuency the lower its bandwidth will be, as a result the RF wave has more losses going thru the walls.

Taking all these considerations into account, sub-GHz frecuencies offer an alternative to the congested 2.4 GHz band, to which devices like Wi-Fi routers inside offices or homes, computers and mobile phones with active Bluetooth, microwaves, all cause interference in the air.

Even though the 2.4 GHz band is currently the top pick due to its costs and global usage, the sub-GHz offer a major reach, less power consumption and broad efficiency in the data transmission with some geographic restrictions and a slightly higher technology cost. Nonetheless, the final cost for each application may vary, although 2.4 GHz radio modules are less expensive, in a real deployement it will be needed more nodes to cover the same area than by using 915 MHz radio modules, whose individual cost per node is superior but by having a bigger reach, the number of nodes needed decreases.


## 2.3 Why Locha Mesh uses the protocol stack?

Locha Mesh intents to adhere to the existing network standards in order to maximize its interoperability through a radio transceiver EEEI 802.15.4g compatible with 6LoWPAN, which allows it to be independent from the internet, and making possible the interconection of the Locha Mesh with internet.
