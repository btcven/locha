# Bitcoin Daemons in Locha Mesh gebruiken

Het belangrijkste doel van deze demostratie is het uitvoeren van een stresstest. Twee [Bitcoin][]-nodes praten en delen blokken en proberen hun blockchains bij te werken

De volgende afbeelding vertegenwoordigt de gebruikte omgeving:

![Bitcoin Daemons Environment](../../pics/demo_bitcoin_daemons.svg)

| node | adres       | verbindingen          | Beschrijving                                             |
|:----:| ----------- | --------------------- | -------------------------------------------------------- |
|  n1  | fc00:db8::1 | Locha Mesh            | pc met bitcoind zonder gesynchroniseerde blockchain      |
|  n?  | fc00:?::1   | Locha Mesh            | 2 Turpial-nodes die indien nodig datapakketten verzenden |
|  ne  | fc00:db9::1 | Locha Mesh & internet | pc met bitcoind met een gesynchroniseerde blockchain     |


## Requirements

There are no special setups or requirements to run this test, you only need a synchronized Bitcoin daemon and a functional Locha Mesh network.


## Procedure

1. The **ne** node needs to be running and synchronized, connected via Turpial or _a compatible interface_ to the Locha Mesh and the Internet at the same time.

2. Start `bitcoind` on **n1**, using the Locha Mesh address of **ne** as parameter.

3. In a few seconds, in the terminal we can see both demons doing a handshake, after that, the demons will start sharing blocks data and latest transactions.





[Bitcoin]: https://bitcoin.org
