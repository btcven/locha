
<img height="200px" src="./images/locha_logo.png">

# Locha Mesh

The Locha Mesh is a radio network for communications and Bitcoin transactions without internet or even electricity.
We are working not only in a protocol, but also the firmware for affordable devices like the *Turpial* or *Harpia*.

## What's in the code

* The main software running on Turpial devices (ESP32) can be found [here](https://github.com/btcven/locha-mesh-app)

* Messages routing protocol code can be found [here](https://github.com/btcven/locha-mesh-app/blob/master/Turpial/routing_incoming.cpp)

* The current Cordova mobile app can be found [here](https://gitlab.com/btcven/locha/mobile-app) , it runs on Android versions equal or greater  than 5.0

* We are working in the new mobile application, will work on iOS or Android and we hope to publish it in the corresponding application store. Check out [here](https://github.com/btcven/LochaMesh-Chat) the repository.

* Documentation can be found [here](https://github.com/btcven/locha/tree/master/documents)

## Can the Locha Mesh software to be installed on other devices?

Sure! We have tested the code in some routers running the OpenWRT OS, also some ARM MCUs can be a good choice. The firmware has a low RAM footprint and we are trying to keep it "vendor agnostic".

## Supported devices

### Turpial
Is a small and portable device [ESP32](https://www.espressif.com/en/products/hardware/esp-wroom-32/overview) based system.

**Overview**
- Dual core 32 bits running at 240 MHz.
- 8MB flash storage.
- WiFi b/g/n dual mode (AP/Station).
- Bluetooth (for admin. access)
- Radio module (for the long range mesh network)
- 1000 mAh Battery
- micro USB for charging and software updates.
- ~~0.96" OLED screen.~~ (*)

In each Turpial can be connected up to 3 clients via WiFi for to send/receive text messages or Bitcoin transactions.

_(*) The screen has been removed from the reference hardware to improve battery performance._

### Harpia (soon)

**Overview**
- Quad core 64 bits at 1.4 Ghz.
- Storage up to 128Gb.
- WiFi dual mode, dual band 2.4Ghz/5Ghz.
- Bluetooth 4.2
- Radio module (for the long range mesh network).
- Ethernet port.
- 4 USB ports.

----
#### :warning: Warning :warning:
Please take into account that some things in this repo are in a very early stage. Thank you for reading through the code and for sharing your ideas on Twitter and the Issues section here at GitHub.

----
## Stay connected

- Twitter [@Locha_io](https://twitter.com/Locha_io)
- Web [locha.io](https://locha.io)

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
Readme in: [Spanish](README_ES.md), [Portuguese](README_PT.md)
