# Using Bitcoin daemons in Locha Mesh

El propósito principal de esta demostración era realizar un test de estrés. Dos nodos [Bitcoin][] hablando entre ellos y compartiendo bloques, intentando actualizar sus cadenas de bloques

La siguiente figura representa el entorno utilizado:

![Bitcoin Daemons Environment](../../pics/demo_bitcoin_daemons.svg)

| node | address     | connections           | Description                                                                         |
|:----:| ----------- | --------------------- | ----------------------------------------------------------------------------------- |
|  n1  | fc00:db8::1 | Locha Mesh            | pc ejecutando bitcoind con una blockchain no sincronizada                           |
|  n?  | fc00:?::1   | Locha Mesh            | 2 nodos Turpial con la capacidad de transmitir paquetes de datos si fuera necesario |
|  ne  | fc00:db9::1 | Locha Mesh & Internet | pc ejecutando bitcoind con la blockchain sincronizada                               |


## Requirements

No hay ninguna configuración especial para ejecutar este test, tan solo un demonio bitcoin sincronizado y una red Locha Mesh funcional.


## Procedure

1. The **ne** node needs to be running and synchronized, connected via Turpial or _a compatible interface_ to the Locha Mesh and the Internet at the same time.

2. Start `bitcoind` on **n1**, using the Locha Mesh address of **ne** as parameter.

3. In a few seconds, in the terminal we can see both demons doing a handshake, after that, the demons will start sharing blocks data and latest transactions.





[Bitcoin]: https://bitcoin.org
