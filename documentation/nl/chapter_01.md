## 1. Motivatie

Hoewel de telecomtechnologie een grote sprong voorwaarts heeft gemaakt in termen van ontwikkeling, wordt het duidelijk dat het zelf gevoelig is. We hebben het niet alleen over technologie, maar ook over mensen. dat zijn degenen die kwetsbaar zijn voor de neveneffecten van isolatie van communicatie als gevolg van centralisatie en wereldwijde regulering van communicatienormen.

De vooruitgang die is geboekt sinds het verschijnen van draadloze netwerken in 1970, de groei en de vraag in de afgelopen tien jaar zijn exponentieel geweest. Er zijn in dit verband twee perspectieven bij de onderliggende architectuur van het netwerk: draadloze netwerkinfrastructuur en draadloze netwerken.

As an example for wireless networking infrastructure we can take a look to cellular networks, in these networks the user can connect wireless to the main network as long as it's inside the coverage range of the network, this is how it can serve a large number of users inside an extense geographic area by using a limited frequency spectrum. The service provided is high quality and is often compared to the wired telephone systems. In this network, the routers or base stations, are in fixed-locations and wired, the mobile units connect to it and communicate through it. If this mobile unit goes further than the range of its base a handoff to another base its produced.

The popularity of the wireless networks started back in the 1980s with the deployment of analog 1G phones limited to one country due to the different standards established by geographic situation. These conditions encourage the development of the 2G for the next decade, adding fax, data service and SMS, but it wasn't adopted as a worldwide standard. This generation was recently extended to 2.5G improving the support of low and high speed data transmition. The transition to 3G determined the adoption of a globalized standard adding roaming, and upgrading the size of the bandwith considerably. In spite of these improvements, 3G presented some difficulties in its deployment and also in the delivering of its services due to limitations on the architecture of the network, which resulted in the creation of the 4G network based on VoIP, relaying all of the services through IP.

Another relevant example of wireless networking infrastructure are the WLAN (Wireless Local Area Network) and Bluetooth which are vastly used in millions of daily used product that connect between devices or serve as bridge to internet.

To serve to the Locha Mesh main goal, the wireless network infrastructure are non-viable and phisically vulnerable to sudden changes that can affect their infrastructure, as well as other limitations like network coverage, or regulations measured by restrictive policies which limits the access of online content to the public, also being used for censor the users and the requested content, therefore our investigation lead us to the latter perspective of wireless infrastructureless networks.

Ad-hoc wireless network does not possess fixed routers, so the participants of the networks could connect voluntarily with each other. These networks appear spontaneously, they are dynamic and can also self-configure. This kind of network allows to interconnect independent nodes, limited on power transmission and processing power to provide a major network coverage and better performance.

The participants of these networks, known as nodes, work as routers that find and keep the routes of other nodes in the network without a previous agreement about the part each node must take over. On the other hand, each node can make decisions independently based on the network actual state, e.g. two personal computers equipped with wireless network adapters can form an independent network as long as each one is in the range of the other.

The applications of this type of network could also include the rescue efforts, conferences or conventions where attendees wish to share information immediately, or accessing data in inhospitable places. Ad-hoc wireless networks are very interesting topic for research and business due to the growing demand for connectivity _anywhere and everywhere_, that the actual standards can't satisfy as a result of their centralized nature.

We live in an interconnected world, new inventions in this field allow us to create bridges to a future where communications can maintain a symbiotic relationship with the current infrastructure, and where the decentralized services are the standard that guarantee the access to any person to the network and the contents in it, a resilient resource that even in the most unexpected extreme situations can still be capable of enabling communications, cooperation and commerce between individuals.

Figure 1.1 shows the simplified architecture of a cellular network versus an ad-hoc network. The Figure 1.1 (a) represent three base stations communicating with their mobile nodes. In contrast, Figure 1.1 (b) exhibits an ad-hoc wireless network in which the nodes are not only mobile devices but also can be part of other elements like an access point.

<figure>
  <img src="../pics/network-topology.png"/>
  <figcaption>Fig.1.1</figcaption>
</figure>

## 1.2 Doelen

Het hoofddoel van Locha Mesh is de implementatie van het AODVv2-routing-protocol om een veerkrachtig communicatiesysteem mogelijk te maken, bestaande uit het volgende:

1. Het meest geschikte routing-protocol voor de packets transmission tussen nodes.
2. Selecteer de juiste hardware voor het draaien van de software en het opslaan van de benodigde data.
3. Implementatie van het AODVv2-protocol in een embedded systeem, in dit geval [RIOT-OS](https://www.riot-os.org/).
4. Verifieer de prestaties van het AODVv2-protocol en vergelijk dit met wat eerder was gepland.
5. Ontwikkeling van de driver die nodig is voor de integratie met het besturingssysteem en de hardware.
6. Bereken een reeks constante waarden voor de netwerkwerking.

Onze secundaire doelen zijn:

- Theoretisch onderzoek naar ad-hoc wireless netwerken en routing-protocollen.
- Vertrouwd raken met simulatietools.
- Bepaal parameters voor het genereren van data.
- Implementatie en uitvoering van een testbed.
- Datacollectie.
- Analyse van resultaten en conclusies.
- Opstellen van de documentatie.
- Kiezen van een besturingssysteem op basis van de laatstgenoemde criteria.

AODVv2-protocol biedt een aantal uitdagingen om optimaal te werken in de toepassingsgebieden voor dit soort netwerk:


* #### Dynamic topology
* #### Scarce resourses (hardware)
* #### Heterogeneity between nodes (hardware verschillen)


## 1.3 Planning

Voor de ontwikkeling van dit project, raden we je ten zeerste aan een logische volgorde voor de ontwikkeling vast te stellen en te volgen:

- Voorbereiding en planning: tijdens de voorbereidingsfase hebben we onderzoek gedaan naar `ad-ho `-netwerk en de vele `routering-protocollen` om onze documentatie over dit onderwerp af te maken.
- Het hardware design:
  - Stroomvoorziening.
  - Batterijcircuit.
  - Radiomodule compatibel met IEEE 802.15.4.
  - Firmware die verantwoordelijk is voor het ontvangen van de informatie van de mobiele app en deze naar de radiomodule stuurt.
  - Printplaat nodig om alle componenten te monteren.
  - Functionaliteitstest van de hardware.
- Mobiele app, die de GUI zal zijn.
- Driver voor de integratie van de hardware met het besturingssysteem.

