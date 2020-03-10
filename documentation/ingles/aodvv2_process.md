<p align="center">
  <a href="https://locha.io/">
  <img height="200px" src="pictures/LogotipoTurpial-Color.20-09-19.svg">
  </a>
  <br/>
  <a href="https://travis-ci.com/btcven/radio-firmware">
    <img src="https://travis-ci.com/btcven/radio-firmware.svg?branch=master" title="Build Status">
  </a>
</p>

<p align="center">
  <a href="https://locha.io/">Project Website</a> |
  <a href="https://locha.io/donate">Donate</a> |
  <a href="https://github.com/sponsors/rdymac">Sponsor</a> |
  <a href="https://locha.io/buy">Buy</a>
</p>

<h1 align="center">Routing Protocol AODVv2</h1>

# Table of Contents
- [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Application of MANETs](#application-of-manets)
  - [**MANET** Routing Protocol Challenges](#manet-routing-protocol-challenges)
    - [Dynamic topology](#dynamic-topology)
    - [Resource constraints](#resource-constraints)
    - [Heterogeneity](#heterogeneity)
  - [AODVv2 Reactive Routing protocol](#aodvv2-reactive-routing-protocol)
  - [Loop Freedom](#loop-freedom)
  - [Route Tables](#route-tables)
    - [Updating AODVv2 routing table](#updating-aodvv2-routing-table)
 


## Introduction
Ad hoc networking has gained popularity and is applied in a wide range of applications, such as public safety and emergency response networks. Mobile Ad-hoc Networks (MANETs) are self-conﬁguring networks that support broadband communication without relying on wired infrastructure. Routing protocols of ad-hoc networks are main factors determining performance and reliability of these networks. 
They specify the way of communication among diﬀerent nodes by ﬁnding appropriate paths on which data packets must be sent.

In this work, we focus on the evolution of the Ad-hoc On-demand Distance Vector(AODV) called AODVv2 routing protocol.
**AODV** is one of the four protocols standardised by the **IETF MANET** working group.  The protocol ﬁnds alternative routes on
demand whenever needed, meaning that it is intended to ﬁrst establish a route between a source node and a destination **(route discovery)**, and then maintain a route between the two nodes during topology changes **(route maintenance)**.

## Application of MANETs

The origins of MANETs are to be found in the US military and one envisioneduse is military. For example, in the battleﬁeld, different units could be able to communicate even if an existing infrastructure has been destroyed or is untrusted. Second, MANETs could be used in rescue and disaster relief efforts, for instancein remote areas with little or insufﬁcient communication possibilities.A third application area is in sensor networks. A network of autonomously co-operating sensors can perform tasks not previouslyp ossible in traditional networks. Typically, nodes are relatively small units placed in an environment to monitor some kind of phenomenon. An example could be vehicle-to-vehicle communica-tion. A sensor placed on a vehicle could detect road conditions and propagate this information to other vehicles on the road. A fourth area is temporary networks, for example deployed at conferences, meeting rooms, and airports. Wireless Internet connection at airports can be ex-pensive, and a group of people could share a connection with the use of a MANET. Finally a ﬁfth application area could be a wireless personal area network with watches, laptops, PDAs, cell phones, and wearable computing devices sharing and exchanging information and delivering added convenience for the owner. Some of the motivation of the different application areas can be summarized aseither total lack of an infrastructure, unwillingness to use any existing infrastruc-ture, or the desire to extend coverage of an existing infrastructure.


## **MANET** Routing Protocol Challenges
MANET routing protocols face some challenges to be able to work in the envisioned application areas as described [above](#Application-of-MANETs)

###  Dynamic topology
 Nodes can appear and disappear at random. Nodes can move continuously or be powered off when entering sleep mode. This means that,for example, the routing protocols cannot assume that once information is gained about the topology, it remains ﬁxed and must consider the cost of constantly updating routing information.

### Resource constraints
 Nodes can have limited resources with respect to computational power, e.g., RAM and CPU power available, power supply, and cost of communication. In addition, there are constraints with regard to bandwidth on the wireless link, the effects of multiple access, fading, noise, and interference conditions may severely limit the transmission rate or even prevent link establishment.

### Heterogeneity
 Nodes can have varying characteristics with respect to the resource constraints speciﬁed above and be more or less willing to participate in routing.



## AODVv2 Reactive Routing protocol

This section provides a brief overview of AODV v2 protocol, in this one each node maintains a routing table (RT) containing information about the routes to be followed when sending messages to the others nodes of the network. The collective information in the nodes **"Routig table"** is at best a partial representation of network connectivity as it was at some times in the past.

The basic routine of AODVv2 is similar to the one of AODV:  

if a node **S** is required to send one or more data packets to a target node **T**, but does not have a route stored in  its routing table, it buffers the data and initiates a route discovery process by broadcasting or flooding a route request (RREQ) message. 

The RREQ is typically forwarded by intermediate nodes that  are not the target. An intermediate node that receives the RREQ message updates its routing table by creating entries for a route to the originator of the message **(node S)** and to all intermediate nodes that have forwarded the RREQ message (path accumulation). After that, the node typically re-broadcasts the request to its neighbours. 

This is repeated until the RREQ reaches the target node **T** that replies by unicasting a corresponding route reply (RREP) message back to the source **S** along the previous established path. 

To shorten route-discovery times, intermediate nodes are allowed to and should reply on behalf of the target if they know a route to **T**. An intermediate node that has this information updates its routing table as usual; after that it unicasts a RREP message back to the originator of the RREQ message **(nodeS)**, but it also unicasts a RREP message to the target **node T**. By this,routes between **S** and **T**, and between **T** and **S** are established. 

The RREP messages are forwarded by  the  intermediate nodes along previously established routes. When forwarding RREP messages, nodes create routing table entries for all nodes that have already forwarded that route, to the originator of the RREP message and, in case the originator is different to the target of the original RREQ, also to the target **node T**. 

After the RREP message has reached **node S**, a route from **S** to **T** is established and data packets can start to flow.

In  case  of  link  breaks,  AODVv2  uses  route  error  (RERR)messages  to  notify  affected  nodes,  i.e.,  nodes  that  could potentially use this link: 
if a link break is detected by a node,it invalidates all routes stored in the node’s own routing table that actually use the broken link. Then it broadcasts a RERR message containing the unreachable destinations to all (direct) neighbours using this route. 

When updating routing table entries,  nodes use sequence numbers to determine the freshness of an entry. The larger the sequence number, the fresher the information a sequence number with value 0 indicates that the number is not known. 

To maintain the information about sequence numbers each node  stores  its  own  sequence  number.  It  is a common belief that sequence numbers are sufficient to guarantee loop freedom if they are monotonically increased over time. Whenever a node initiates a route request or a route reply, it increments its own sequence number and transmits the incremented value as part of the message. Whenever a RREQ or a RREP message is forwarded, an intermediate node adds its sequence number together with its IP-address to the content of the message.

## Loop Freedom

The “naive” notion of loop freedom is a term that informally means that “a packet never goes round in cycles with-out (at some point) being delivered”. This dynamic definition is too restrictive a requirement for AODV. There are situations where packets are sent in cycles, but which should not be considered “harmful”. This can happen when the network topology keeps changing. The sense of loop freedom is much better captured by a static invariant, saying that at any given time the collective routing tables of the nodes do not admit a loop. Such a requirement does not rule out the dynamic loop alluded to above. However, in situations where the topology remains stable sufficiently long it does guarantee that packets will not keep going around in cycles.

## Route Tables

Every received route message contains a route and consequently is evaluated to check for any improvement.

```block
Note that an rreq message contains a route to its source while an rrep message contains a route to its destination. 

Therefore, as the routes are identiﬁed by their destinations, in the former case, the destination of the route 

is the originator of the message and in the latter, it is the destination of the message
``` 
Note that we say a router is better then others if it has either a greater sequence number than others or an equal sequence number. 
The routing table must be updated if one of the following conditions is realized.

### Updating AODVv2 routing table 
The routing tables can be updated if:
- No route to the destination exists in the routing table: the route is added to the routing table.
- the incoming route is better than the existing one. Two cases can be distinguished:
  - 1. there is only one matching route with the same destination:
    - the route state of the existing route is invalid: the incoming route must replace the existing one;


- All the existing routes to the destination are unconﬁrmed, i.e., their next hops are unconﬁrmed: the route is added to the routing table.
- If AdvRte is more recent than all matching LocalRoutes. 
- If the sequence numbers are equal, Check that AdvRte is safe against routing loops
- compared to all matching LocalRoutes, 
  
```
If LoopFree(AdvRte, LocalRoute) 
    returns TRUE,
```
compare route costs:
- If AdvRte is better than all matching LocalRoutes, it MUST be used to update the Local Route Set because it oﬀers improvement.
- If AdvRte is not better (i.e. it is worse or equal) but LocalRoute is Invalid, AdvRte **SHOULD** be used to update the Local Route Set because it can safely repair the existing Invalid LocalRoute.




