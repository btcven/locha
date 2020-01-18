<p align="center">
  <a href="https://locha.io/">
  <img height="200px" src="images/LogotipoTurpial-Color.20-09-19.svg">
  </a>
</p>

<p align="center">
  <a href="https://locha.io/">Project Website</a> |
  <a href="https://locha.io/donate">Donate</a> |
  <a href="https://github.com/sponsors/rdymac">Sponsor</a> |
  <a href="https://locha.io/buy">Buy</a>
</p>

<h1 align="center">Locha Mesh</h1>

The Locha Mesh is a radio network for off-grid messaging and cryptocurrency
transactions without access to the internet. We are working not only in a
protocol, but also the firmware for affordable devices like the *Turpial*,
*Harpia*, or their DIY equivalents. We adhere to open source ethos at every
step and aim to make this tool as decentralized as possible so users can
transmit with freedom.

## What's in the code?

* The main application that communicates with the Turpial devices (ESP32 &
SimpleLink radio MCUs) can be found
[here](https://github.com/btcven/locha-mesh-chat).

* Our firmware for the Turpial boards (ESP32) can be found
[here](https://github.com/btcven/turpial-firmware).

* Our firmware for the radio modules (SimpleLink radio MCUs) can be found
[here](https://github.com/btcven/radio-firmware). In practice the firmware can
be installed on any platform capable of running
[Contiki-NG](https://github.com/contiki-ng/contiki-ng/wiki), but currently our
focus is on support for the
[SimpleLink platform](https://github.com/contiki-ng/contiki-ng/wiki/Platform-simplelink).

* Routing protocol and code for the mesh network be found in
[radio-firmware](https://github.com/btcven/radio-firmware) and in our
[Contiki-NG fork](https://github.com/btcven/contiki-ng) repositories.

* Find Locha Mesh simulator, datasheets, and more on the
[Bitcoin Venezuela organization](https://github.com/btcven).

* Documentation can be found
[here](https://github.com/btcven/locha/tree/master/documents).

## Supported devices

### Turpial
A Turpial is a small and portable device
[ESP32](https://www.espressif.com/en/products/hardware/esp-wroom-32/overview)
based system.

Radio module, battery type, and more details about the board are currently
described
[here](https://github.com/btcven/locha/blob/master/documents/turpial-description.md).

<p align="center">
  <img height="533px" width="400px" src="images/turpial-finished-board.jpg">
  <br/>
  <i>Actual board photo</i>
</p>

<p align="center">
  <img height="338px" width="600px" src="images/turpial-size-compared.jpg">
  <br/>
  <i>Size comparison of a Turpial board</i>
</p>

## Contribution guidelines

Please read our [contributing guide.](CONTRIBUTING.md)

----
#### :warning: Warning :warning:

Please take into account that some things in this repo are in a very early
stage. Thank you for reading through the code and for sharing your ideas on
Twitter and the [Issues](https://github.com/btcven/locha/issues) section here
at GitHub or under each specific repo.

----
## Stay connected

- Twitter [@Locha_io](https://twitter.com/Locha_io).
- Website [locha.io](https://locha.io).
- Telegram [Locha](t.me/Locha_io)

## License

Copyright (c) 2019 Bitcoin Venezuela and Locha Mesh developers.

Licensed under the **Apache License, Version 2.0**

---
**A text quote is shown below**

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

Read the full text:
[Locha Mesh Apache License 2.0](https://github.com/btcven/locha/blob/master/LICENSE)

----
Leeme en: [Español](README.es.md)
