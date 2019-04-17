# Locha
<Achieving hyperbitcoinization in Venezuela>



## Locha Mesh
The Locha Mesh is a radio network for communications and Bitcoin transactions without internet or even electricity.
We are working not only in a protocol, but also the firmware for affordable devices like the _Turpial_ or _Harpia_.



## What's in the code
* The main software running on Turpial devices (ESP32 LoRa V2) can be found [here](https://github.com/btcven/locha-mesh-app)
* Messages routing protocol code can be found [here](https://github.com/btcven/locha-mesh-app/blob/master/Turpial/routing_incoming.cpp)
* The current Cordova mobile app can be found [here](https://gitlab.com/btcven/locha/mobile-app) , it runs on Android >5.0

Documentation can be found [here](https://github.com/btcven/locha/tree/master/documents)



### Can Locha Mesh software be installed on other devices?

Sure! We have tested the code in some routers running the OpenWRT OS, also some ARM MCUs can be a good choice. The firmware has a low RAM footprint and we are trying to keep it "vendor agnostic".


---


#### :warning: Warning
Please take into account that some things in this repo are in a very early stage. Thank you for reading through the code and for sharing your ideas on Twitter and the Issues section here at GitHub.


---


## Supported devices

#### Turpial
Is a small and portable device [ESP32](https://www.espressif.com/en/products/hardware/esp-wroom-32/overview) based system.

**Overview**
- Dual core 32 bits running at 240 MHz.
- 8MB flash storage.
- WiFi b/g/n dual mode (AP/Station).
- Bluetooth (for admin. access)
- Radio module (for the long range mesh network)
- 1000 mAh Battery
- micro USB for charging and software updates.
- 0.96" OLED screen.
 
In each Turpial can be connected up to 3 clients via WiFi for to send/receive text messages or Bitcoin transactions.

#### Harpia _(soon)_

**Overview**
- Quad core 64 bits at 1.4 Ghz.
- Storage up to 128Gb.
- WiFi dual mode, dual band 2.4Ghz/5Ghz.
- Bluetooth 4.2
- Radio module (for the long range mesh network).
- Ethernet port.
- 4 USB ports.

----

## Stay connected

- Twitter [@Locha_io](https://twitter.com/Locha_io)
- Web [locha.io](https://locha.io)

----

## License
Copyright (c) 2019 locha.io developers.

This software is under a [MIT](LICENSE) license and you can read the full text in the LICENSE file in the root folder.

Readme in: [Español](README_ES.md), [Portuguese](README_PT.md)
