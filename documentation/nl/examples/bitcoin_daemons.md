# Bitcoin-daemons in Locha Mesh gebruiken

Het belangrijkste doel van deze demo is het uitvoeren van een stresstest. Twee [Bitcoin][]-nodes praten en delen blokken met elkaar, zo proberen ze hun blockchains te updaten

De volgende afbeelding vertegenwoordigt de gebruikte environment:

![Bitcoin-daemons environment](../../pics/demo_bitcoin_daemons.svg)

| node | adres       | verbindingen          | Omschrijving                                             |
|:----:| ----------- | --------------------- | -------------------------------------------------------- |
|  n1  | fc00:db8::1 | Locha Mesh            | pc met bitcoind zonder gesynchroniseerde blockchain      |
|  n?  | fc00:?::1   | Locha Mesh            | 2 Turpial-nodes die indien nodig datapakketten versturen |
|  ne  | fc00:db9::1 | Locha Mesh & internet | pc met bitcoind met een gesynchroniseerde blockchain     |


## Requirements

Er zijn geen speciale setups of requirements om deze test uit te voeren, je hebt alleen een gesynchroniseerde Bitcoin-daemon en een functioneel Locha Mesh-netwerk nodig.


## Procedure

1. De **ne**-node moet worden gedraaid en gesynchroniseerd, verbonden via Turpial of _een compatibele interface_ op de Locha Mesh en het internet tegelijkertijd.

2. Start `bitcoind` op **n1**, door het Locha Mesh-adres **ne** als parameter te gebruiken.

3. Binnen een paar seconden kunnen we in de terminal beide daemons een handshake zien uitvoeren, daarna zullen de daemons blokdata en de laatste transacties gaan delen.





[Bitcoin]: https://bitcoin.org
