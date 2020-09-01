# Monero GUI via Locha Mesh

Met behulp van de [Monero][] GUI en andere node met de Monero-daemon kunnen we transacties versturen en ontvangen over Locha Mesh. De volgende afbeelding geeft de opstelling voor deze specifieke toepassing weer:

![demo_monero](../../pics/demo_monero.svg)

| Adres       | Verbindingen          | Omschrijving                                             |
| ----------- | --------------------- | -------------------------------------------------------- |
| fc00:db8::1 | Locha Mesh            | Laptop als client die de GUI draait                      |
| fc00:?::1   | Locha Mesh            | 2 Turpial-nodes die indien nodig datapakketten versturen |
| fc00:db9::1 | Locha Mesh & internet | Server die Monero-daemon draait                          |

## Vereisten

Er zijn geen speciale setups of vereisten om deze test uit te voeren, je hebt alleen een gesynchroniseerde Monero-daemon en een functioneel Locha Mesh netwerk nodig.

## Procedure

1. Synchroniseer de wallet

   ![Monero GUI](../../pics/monero_gui.png)

   **Let op:** Er is een kleine bug gedetecteerd toen er een IPV6 in de adres/poort velden werd gebruikt onder `Instellingen > node`, het werd [gemeld][] en een [PR][] was open.

2. Ontvangen en versturen van een transactie

   Er is een video beschikbaar van het hele process inclusief een snelle installatie en de node-synchronisatie.

   [![Monero demovideo](https://img.youtube.com/vi/pe9Buhp9OD8/0.jpg)](https://www.youtube.com/watch?v=pe9Buhp9OD8)



[Monero]: https://web.getmonero.org/
[gemeld]: https://github.com/monero-project/monero-gui/issues/2971
[PR]: https://github.com/monero-project/monero-gui/pull/2973
