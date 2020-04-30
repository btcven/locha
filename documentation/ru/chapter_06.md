# 6. Locha Mesh Hardware

This section presents the parameters that were used to choose all the control and transmission devices currently used in Locha Mesh.


## 6.1 Radio frequency module

<img src="pics/RF-selection.svg"  height="650" width="450" align="left" alt="RF Selection" />

To choose the radio module that contains the microcontroller (MCU), the RF and modulation needed to transmit on the 915 MHz frequency, some parameters were established:
- Support of IEEE 802.15.4 standard.
- At least 2 UART ports.
- Low-cost.
- Easy to use.
- Support for the Sub-GHz band to 915 MHz.

The chart shows three MCU's families with RF interface according to the criteria based on the current demand, choosing the CC1312R1 as the most economical, solid, with wide range coverage and low consumption module to perform the task  that the Turpial requires as a transceptor device.

The MCUs family CC135X fulfills most of the requirements, nevertheless, provides more functions than required, increasing its value and consequently discarded.

<br>
<br>


## 6.2 Voltage source

For the Locha Mesh's Turpial to work optimally, the device that will regulate the voltage deriving from the battery and will power the device must be carefully selected.

<br>
<img src="pics/powerSupply-selection.svg"  height="650" width="450" align="left" alt="Power Supply Selection" />

The parameters to take into account for the selection of the device that supplies the circuit are:
- Electric current: the circuit current must be greater or equal to 2 amps (A).
- Voltage: the device must be capable of delivering a stable 3.3 volts output voltage, regardless of whether the input supply is above or below the required threshold.
- High efficiency: it must be efficient converting the energy, which translates into no external overheating.
- Noise: any noise in the voltage output signal can affect the entire system.

When weighting the difference devices, it is observed that the LTC3113 provides a good performance, it is full bridge and delivers currents above 2.5 A, but it's high-priced. We have decided to use the TPS63802 since it provides the benefits required to meet the goals of our project, it has wide market availability and has an affordable price.

<br>
<br>
<br>

## 6.3 Battery charger

<img src="pics/BatteryChargerSelection.svg"  height="450" width="450" align="left" alt="Battery Charger Selection" />
<br>

For charging the lithium battery we have decided to use the BQ2407x family chip, which is easy to acquire and it adapts to the linear load and durability requirements of the batteries by having a robust charging system like the one presented in the Turpial design.
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## 6.4 Battery power control
<img src="pics/BatteryManagementSystem-selection.svg"  height="450" width="550" align="left" alt="Battery Management System" />

The power control is the system that is responsible for reading variables related to the battery such as available power, charging time, current supplied, etc.

When selecting the device that will perform this task, it must have I2C support, in the chart you can see two different devices from two different manufacturers, which have similar presentations but differ greatly in cost per unit, so The BQ27441-G1 will be used as a device for collecting battery related data to be sent to the main control unit.

<br>
<br>

## 6.5 MCU (Interface)

For the selection of the microcontroller, we have focused on the  _Espressif_ controllers, as this is a brand that offers a variety of devices with incredible features such as Bluetooth and Wi-Fi at very low cost, which another brand could hardly offer in addition to the extensive documentation that exist for this device.

Benefits of the ESP32 family:
- Economical.
- Extensive documentation available.
- Examples on the Arduino platform that can be easily carried into the espressif environment.
- Energy- efficient.
- Incorporate BLE, which enables a wide range of possibilities.

Locha Mesh specifically works with ESP32 WROVER, consist of:
- 4 Mb Flash SPI.
- 8 Mb PSRAM.
- 80 MHz a 240 MHz Frequency.
- Wi-Fi.
- Bluetooth.
- USART x3.
- Dual core.

It is perfect for the Turpial, since it has several USART ports, which allows us to communicate with the radio module and the PC through USB, it also has a network adapter that allows us to implement a web server for the control application and its configuration.


## 6.6 Block diagram of the Turpial hardware

<img src="pics/Hardware-Block.svg" height="450" width="900" align="center" alt="Hardware Block" />


## 6.7 Block diagram of the control application.

## 6.8 Overview of the Turpial as a device.

<img src="pics/turpial.svg" height="450" width="400" align="center" alt="Turpial" />

The figure shows the first prototype currently under development. 
