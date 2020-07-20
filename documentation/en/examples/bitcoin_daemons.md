# Using Bitcoin daemons in Locha Mesh

The main purpose of this demostration is to perform a stress test. Two [Bitcoin] nodes are talking and sharing blocks, trying to update their  blockchains 

The next figure represent the used environment:

![Bitcoin Daemons Environment](../../pics/demo_bitcoin_daemons.svg)

| node | address     | connections           | Description                                             |
|:----:|-------------|-----------------------|---------------------------------------------------------|
| n1   | fc00:db8::1 | Locha Mesh            | pc running bitcoin with no-syncronized blockchain       |
| n?   | fc00:?::1   | Locha Mesh            | 2 Turpial nodes that transmit data packets if necessary |
| ne   | fc00:db9::1 | Locha Mesh & Internet | pc running bitcoind with a synchronized blockchain      |


## Requirements

No special setup or requirements for run this test, only a synchronized Bitcoin daemon and a functional Locha Mesh network.


## Procedure

1. The **ne** node needs to be running and synchronized, connected via _Turpial_ or compatible interface to the Locha Mesh and Internet at the same time.

2. Start `bitcoind` on **n1**, using the Locha Mesh address of **ne** as parameter.

3. In a few seconds, in the terminal we can see both demons doing a handshake, after that, the demons will start sharing blocks.





[Bitcoin]: https://bitcoin.org
