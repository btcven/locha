## 1. Motivation

Even though telecom technology has taken a big leap in development terms, it becomes evident it's own susceptibility. We're not just talking about technology by itself, also we are talking about those people that are vulnerable to the side effects of communication isolation resulting from centralization, and regulation of global communication standards.

Since the invention of wireless networks in 1970s, it's growth and demand in the last decade has been exponential. Specifically, there are two paths in this subject that attend to the underlying architecture of the network: wireless networking infrastructure mode and ad-hoc mode networks.

As an example for wireless networking infrastructure mode we can take a look to cellular networks, in these networks the user can connect wireless to the main network as long as it's inside the coverage range of the network, this is how it can serve a large number of users inside an extense geographic area by using a limited frequency spectrum. The service provided is high quality and is often compared to the wired telephone systems. In this network, the routers or base stations, are in fixed-locations and wired, the mobile units connect to it and communicate through it. If this mobile unit goes further than the range of its base a handoff to another base its produced.

The popularity of the wireless networks started back in the 1980s with the deployment of analog 1G phones limited to one country due to the different standards established by geographic situation. These conditions encourage the development of the 2G for the next decade, adding fax, data service and SMS, but it wasn't adopted as a worldwide standard. This generation was recently extended to 2.5G improving the support of low and high speed data transmition. The transition to 3G determined the adoption of a globalized standard adding roaming, and upgrading the size of the bandwith considerably. In spite of these improvements, 3G presented some difficulties in its deployment and also in the delivering of its services due to limitations on the architecture of the network, which resulted in the creation of the 4G network based on VoIP, relaying all of the services through IP.

Another relevant example of wireless networking infrastructure are the WLAN (Wireless Local Area Network) commonly known as Wi-Fi and Bluetooth which are vastly used in millions of daily used product that connect between devices or serve as bridge to internet.

To serve to the Locha Mesh objective, the wireless networking infrastructure mode are non-viable and phisically vulnerable to sudden changes that can affect their infrastructure, as well as other limitations like network coverage, or regulations measured by restrictive policies which limits the access of online content to the public, also being used for censor the users and the requested content, therefore our investigation lead us to the later path of ad-hoc wireless networks.

Ad-hoc wireless network does not possess fixed routers, so the participants of the networks must connect voluntarily with eachother. These networks appear spontaneously, they are dynamic and can also self-configure. This kind of network allows to interconnect independent nodes, limited on power of transmission and processing power to provide a major network coverage and better performance. 

The participants of these networks, known as nodes, work as routers that find and keep the routes of other nodes in the network without a previous agreement on the part each node must take over. On the other hand, each node can make decisions independently based on the network actual state, e.g. two personal computers equipded with wireless network adapters can form an independent network as long as each one is in the range of the other.

The applications of this type of network could also include the rescue efforts, conferences or conventions where attendees wish to share information immidiately, or accessing data in inhospitable places. Ad-hoc wireless networks are very interesing topic for research and business due to the growing demand for connectivity _anywhere and everywhere_, that the actual standards can't satisfy as a result of their centralized nature.

We live in an interconnected world, new inventions in this field allow us to create bridges to a future where communications can maintain a symbiotic relationship with the current infrastructure, and where the decentralized services are the standard that guarantee the access to any person to the network and the contents in it, a resilient resource that even in the most unexpected extreme situations can still be capable of enabling communications, cooperation and commerce between individuals. 

Figure 1.1 shows the simplified architecture of a cellular network versus an ad-hoc network.
The Figure 1.1 (a) represent three base stations communicating with their mobile nodes. In contrast, Figure 1.1 (b) exhibits an ad-hoc wireless network in which the nodes are not only mobile devices but also can be part of other elements like an access point.



<figure>
  <img src="imple_pic/network-topology.png" alt="drawing" height="200" width="450" align="center"/>
  <figcaption>Fig.1.1</figcaption>
</figure>


## 1.2 Objectives

Locha Mesh main objective is the implementation of the routing protocol AODVv2 to enable a resilient communication system, composed of the following:

1. Choosing the most suitable routing protocol for the packets transmision between nodes.
2. Select the proper hardware for the execution of the software and storing the necessary data. 
3. Implementation of the AODVv2 protocol in an embedded system, for this case [RIOT-OS](https://www.riot-os.org/).
4. Verify the performance of the AODVv2 protocol and contrast it with what was previously planned. 
5. Development of the driver needed for the integration with the OS and the hardware. 
6. Compute set of constant values for the network operation.

Our secondary objectives are:

- Theoretical research for ad-hoc wireless networks and routing protocols.
- Familiarization with simulation tools.
- Determine parameters for data generation.
- Implementation and execution of a testbed.
- Data collection.
- Analysis of results and conclusions.
- Drafting the documentation.
- Choosing an OS based on the criteria later exposed.

AODVv2 protocol presents some challenges in order to work optimally in the application areas provided for this kind of network:

<ul class="w3-ul w3-border">
 <li><h4>Dynamic topology.</h4></li>
 <li><h4>Scarce resourses (hardware).</h4></li>
 <li><h4>Heterogeneity between nodes (hardware differences).</h4></li>
</ul>




## 1.3 Planning

We strongly recommend to establish and follow a logical sequence for the development of this project:

- Preparedness and planning: during the preparation phase we researched about ```ad-hoc``` network and the many ```routing protocols``` to complete our documentation on this subject.
- The hardware design:
  - Power supply.
  - Battery circuit.
  - Radio module compatible with IEEE 802.15.4.
  - Firmware in charge of receiving the information from the mobile app and send it to the radio module.
  - Printed circuit board needed to assemble all of the components.
  - Functionality test of the hardware.
- Mobile App, which will be the GUI.
- Driver for the integration of the hardware with the OS.
