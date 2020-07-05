# 6. Hardware de Locha Mesh

En esta sección se presentan los parámetros que se usaron para la elección de los dispositivos de transmisión y control actualmente utilizandos en Locha Mesh.


## 6.1 Módulo de radio frecuencia

<img src="../pics/RF-selection.svg"  height="650" width="450" align="left" alt="Para elegir el módulo de radio que contiene el microcontrolador (MCU), la RF y modulación necesaria para transmitir en la frecuencia 915 MHz, se establecieron algunos parámetros:" />

La tabla muestra tres familias de MCU con interface de RF de acuerdo con los criterios basados en la necesidad actual, elegiendo el `CC1312R1` como el módulo más económico, robusto, amplio rango de cobertura y de bajo consumo para desempeñar la tarea que el `Turpial` requiere como dispositivo transceptor.

- Soporte del estándar IEEE 802.15.4.
- Al menos 2 puertos USART.
- De bajo costo.
- Fácil de utilizar.
- Soporte para la banda del Subgigahercio a 915 MHz.

La familia de MCUs `CC135x` cumplen con la mayoría de requerimientos, sin embargo provee más funciones de las que se necesitan encareciendo su valor y en consecuencia, descartado.

The MCUs family CC135X fulfills most of the requirements, nevertheless, provides more functions than required, increasing its value and consequently discarded.

<br/>
<br/>

## 6.2 Fuente de voltaje

Para que el Turpial de Locha Mesh funcione de forma óptima, se debe seleccionar cuidadosamente el dispositivo que regulará la corriente proveniente de la batería y alimentará al dispositivo.

<br/>

<img src="../pics/powerSupply-selection.svg"  height="650" alt="Los parámetros a tomar en cuenta para la elección del dispositvo que alimenta al circuito:" />

Los parámetros a tomar en cuenta para la elección del dispositvo que alimenta al circuito:
- Corriente: La corriente del circuito debe ser mayor o igual a 2 Amperios (A).
- Voltaje: El dispositivo debe ser capaz de entregar un voltaje estable de salida de 3.3 Voltios, sin importar que la entrada que lo alimenta este por encima o por debajo del umbral requerido.
- Alta eficiencia: Debe ser eficiente en la transformación de la energía, lo que se traduce en libre de sobrecalentamientos extremos.
- Ruido: Cualquier ruido en la señal de salida de voltaje puede afectar todo el sistema.

When weighting the difference devices, it is observed that the LTC3113 provides a good performance, it is full bridge and delivers currents above 2.5 A, but it's high-priced. We have decided to use the TPS63802 since it provides the benefits required to meet the goals of our project, it has wide market availability and has an affordable price.


## 6.3 Cargador de batería

<img src="../pics/BatteryChargerSelection.svg"  height="650" alt="Para que el Turpial de Locha Mesh funcione de forma óptima, se debe seleccionar cuidadosamente el dispositivo que regulará la corriente proveniente de la batería y alimentará al dispositivo." />
<br>

Para la carga de la batería de litio hemos decidido utilizar el chip de la familia `BQ2407x`, el cual es de fácil adquisicion y se ajusta a los requerimientos de carga lineal y durabilidad en las baterías al tener un sistema de carga robusto, como el que se presenta en el diseño del Turpial.

## 6.4 Control de energía de la batería
<img src="../pics/BatteryManagementSystem-selection.svg"  height="650" alt="Battery Management System" />

The power control is the system that is responsible for reading variables related to the battery such as available power, charging time, current supplied, etc.

El control de energía es el sistema que se encarga de leer variables relacionadas con la batería como energía disponible, tiempo de carga, corriente suministrada, etc.

## 6.5 Microcontrolador (Interface)

A la hora de seleccionar el dispositivo que se encargará de dicha tarea, este debe tener soporte para I2C; en la tabla se pueden apreciar dos diferentes dispositivos de dos fabricantes diferentes los cuales tienen prestaciones similares, pero difieren mucho del costo por unidad, así que se utilizará el BQ27441-G1 como dispositivo para la recolección de datos relacionados con la batería para ser enviada a la unidad principal de control.

Locha Mesh específicamente trabaja con el ESP32 WROVER, el cual consta de:
- Económico.
- Amplia documentación disponible.
- Comunidad de desarrolladores en continuo crecimiento.
- Ejemplos en la plataforma de Arduino que se pueden portar al entorno de espressif sin mucha dificultad.
- Bajo consumo.

Para la seleccion del microcontrolador (MCU), nos hemos centrado en la famila de microcontroladores de `espressif`, ya que esta es una marca que ofrece una gran variaded de dispositivos con increíbles características como los es Bluetooth y WiFi a muy bajo costo, que difícilmente otra marca podría ofrecer además de la amplia documentación que existe para este dispositivo.
- 4 Mb Flash SPI.
- 8 Mb PSRAM.
- Frecuencia de 80 MHz a 240 MHz.
- WiFi.
- BlueTooth.
- USART x3.
- Dual core.

Bondades de la familia ESP32:


## 6.6 Diagrama en bloques del hardware del Turpial

<img src="../pics/Hardware-Block.svg"  width="100%" alt="Hardware Block" />


## 6.7 Diagrama en bloques de la aplicación de control

## 6.8 Visión General del Turpial como dispositivo

<img src="../pics/turpial.svg" height="450"  alt="Turpial" />

La figura muestra el primer prototipo actualmente en desarrollo. 
