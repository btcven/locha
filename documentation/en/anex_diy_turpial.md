# DIY Hardware for Locha Mesh

We will try to describe the simplest and necessary piece of hardware to connect to Locha Mesh, using Turpial as base.

Turpial consist of a MCU with a integrated radio transceptor [_CC1312R_](https://www.ti.com/product/CC1312R), a WiFi module _ESP32_ and some complementary circuits such as _USB to Serial_ converter, power amplifier (PA) for the radio transceptor, power source and battery charger and some LEDs for a visual interpretation of the system status 

The basis of the entire system is the _CC1312R_, a small MCU with radio transceptor for Sub-1-GHz frequencies and a PA for TX output power up to **+14dBm**
 
Not many manufacturers are integrating the MCU described above, only the italian [Radiocontrolli](https://www.radiocontrolli.com/) develop modules and kits for evaluation.
<p align="center">
    <img src="../pics/radiocontrolli-cc1312r.jpg" height="300px" />
</p>

Texas Instruments (TI) develops a ready-to-use development kit named [LAUNCXL-CC1312R1](https://www.ti.com/tool/LAUNCHXL-CC1312R1) with debugger, MCU, and power supply. this was our development platform for months previously to the first Turpial board.

<p align="center">
    <img src="../pics/launchxl-cc1312r1_cc1312r1-top-prof1.jpg" height="300px" />
</p>

Other compatible modules are _CC1352R_, _CC1352P_, both of TI and (not yet tested) _AT86RF2xx_ family from _Microchip_

## Hands to work

Depending of if we want a simple-client or multi-client system we need:

### For Single-Client 

A dev-kit as above described connected via USB port of our PC or credit card sized computer like Raspberry PI or similar.

The USB port provide connectivity and power source for the board so this is the simplest setup, only flash the firmware following the instructions in [btcven/radio-firmware](https://github.com/btcven/radio-firmware)

### For multi-client

We need:

- LAUNCHXL-CC1312R1 or compatible dev-kit with the latest firmware available in our [radio-firmware](https://github.com/btcven/radio-firmware) repository.

- ESP32 dev board such as [WROVER-KIT-VB](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/hw-reference/esp32/get-started-wrover-kit.html) with the latest firmware available in the [turpial-firmware](https://github.com/btcven/turpial-firmware) repository

We recommend to follow the instructions carefully for flashing the firmware and initial setup in each repository.

A simplified blocks schema of our DIY Turpial

<p align="center">
    <img src="../pics/basic_turpial.svg" height="400px">
</p>

In our case the USB to Serial converter and the _CC1312R_ MCU are on the Launchpad, and between this and the _WROVER dev-kit_ we need to connect only two wires for the UART interface and GND for a common ground reference.

| LAUNCHPAD-CC1312R1 | WROVER DEV-KIT |
|--------------------|----------------|
| DIO11              | GPIO22         |
| DIO12              | GPIO21         |
| GND                | GND            |


<p align="center">
    <img src="../pics/diy.svg" height="500px">
</p>


At first any _ESP32 board_ can work, only follow the next recommendation: 

1. Use only the UART1 in the _ESP32_ to connect to _CC1312R_ dev-board or module.
2. The UART0 is reserved to connect _USB to Serial converter_ in most of dev-boards for debugging purposes and bootloading.
3. Actually ESP32-S2 is not supported.

The list of ESP32 boards available in the market is huge, for example:

| Board Name                     | UART1 TX/RX | Manufacturer                            | Observations                       |
|--------------------------------|-------------|-----------------------------------------|------------------------------------|
| HUZZAH32 - ESP32 Feather       | 17/16       | [Adafruit](https://www.adafruit.com)    | LiPo Battery charger               |
| WiFi KIT 32                    | ---         | [Heltec](https://heltec.org)            | LiPo Battery charger, OLED display |
| TTGO T7 V1.3 MINI 32           | 1/3         | [Lilygo](http://www.lilygo.cn)          | Battery charger, small factor      |
| Thing Plus - ESP32 WROOM       | 17/16       | [SparkFun](https://www.sparkfun.com/)   | Battery charger                    | 
| ESP32 NodeMCU (various models) | N/A         | Several                                 | Cheap                              |
| Wemos (various models)         | N/A         | Several                                 | Cheap                              |               


We can use the USB connection in each board as power source or in some models a power management system is available, including battery charger,but keep in mind not all _ESP32_ based boards can be used as power source for the _CC1312R_, check the output current that each board can offer.