# 5. Routing Protocols

There are many routing protocols used for information management but not all of them are relevant to the WNS, their use case is completely different. The loss of information in wired networks its due to the great amount of data traffic traveling through the network, whereas in WNS the medium is very hostile, the air, where devices and transmissions are exposed to enviroment changes like noise and other alterations that may take place, to say nothing of how devices must _fight_ to access the medium. Therefore the approach given to an ad-hoc network protocol is different to the one used in wired networks, on the latter the protocols are not design to work with that much stress.

## 5.1 Types of routing protocols  

There are three types of routing protocols for wireless networks, these protocols have been developed due the need to control the routing on ad-hoc networks [3] keeping in mind the limitations of the devices:

### 5.1.1 Proactive Protocols

In this type of protocols the routing tables of the nodes are regularly updated eventhough they are not sending any information. When a change happens within the network, the routing table updates and the protocol chooses the optimal route to send the data. This is due to the exchange of control messages which increases the battery consumption by the number of packets sent into the network.

### 5.1.2 Reactive Protocols 

These only add routes on-demand. When a reactive protocols needs to communicate with a node and are not provided with a route to its destiny they will initiate a route discovery process, the answer received will add the route in its routing table. It is then when a communication with the destinatary is possible. The major inconvenience with this protocol is the latency added to the first packet of that transmission by that new route, but at the same time it improves the battery consumption in the nodes. This protocol has two subclasses:

- **Source routing**: The route of the nodes is stored in the header of the packets, so that intermediary nodes don't need routing tables as they only need to read the header to know to who resend the information.

Not recommended to larger networks, as the message hops through each node the header of the packet increases.

- **Next hop routing**: The route is choose by any node, at any time, as to when information is sent the header of the packet contains the address of the recipient node and the address of the next hop.

This one adapts faster to changes in the topology but it makes intermediate nodes to expend more resources storing suitable routing tables. 

### 5.1.3 Hybrid Protocols 

Hybrids protocols are a mix between proactive and reactive protocols. The point is to use the best features offered by each of them. The protocols divide the network in zones, and the nodes that are far of the node destiny use the reactive routing while those nodes near the destiny use proactive routing, this is the case of ZRP (Zone Routing Protocol).

Another examples of hybrid protocols are OSI IS-IS (Intermediate System to Intermediate System), and EIGRP (Enhanced Interior Gateway Routing Protocol) from CISCO. 


### 5.1.4 Routing protocol selection criteria

The type of network to implement must be based in low consumption embedded devices which will limit the hardware, therefore:

- Must find the best routes.
- Set a minimun message transmission.
- Low latency between the source and the destiny.
- Must not flood the network with control messages.
- Resilient, self healing.

In all routing protocols used for wireless networks and WNS it's important to set the routes. If a protocol chooses good routes, it will result in a minor latency between the source and the destiny, therefore it will perform less transmission messages consuming less energy from the device.


## 5.2 Locha Mesh routing protocol

<br>
<img src="pics/protocolo_seleccion.svg"  height="650" width="450" align="left"/>


In this diagram we summarize the criteria for choosing a protocol suitable for Locha Mesh. We can enphasize the following:

- Free from ```loops```.
- The computing cost must be low, since its executed inside an embedded system with limited resources.
- The protocol must be reactive to avoid flooding the network with messages even when the routes are not being used, and save battery while the user its not requiring routes.

It is observed that the highest score in the chart belongs to the AODVv2 protocol and TORA. We have decided to work with the AODVv2, since it has available documentation updated online, although it's plausible a revision for TORA.

Another point to take into account is the network topology that will be used for the deployment, according to the chart regarding the criteria on the client side and reducing the points of failure, the star topology is discarded and in its place the mesh topology will be implemented.

