## MCU ESP32

In principle, any module that carries an ESP32 with at least 4MB to 8MB flash storage could be used. For example, the ESP32 WROOM models.

### Adafruit Feather HUZZAH32

This would be the minimum system, 4MB of storage and battery management. A disadvantage is the low RAM they carry. They lack of PSRAM could have an impact on the number of users who simultaneously use the access point "AP".
 
It can be purchased on the manufacturer's own website, www.adafruit.com or at any local or global distributor such as mouser:
 https://www.mouser.es/ProductDetail/Adafruit/3405?qs=sGAEpiMZZMuJ3l9lTgMBp1ZUJQFPkBG2kgGNDfvEbt9%252BtBjtG%2Fb8ag%3D%3D

### Heltec WiFi 32 V1 and Heltec WiFi 32 V2
The first has a storage of 4MB, the second of 8MB (more than enough). It includes a charging and battery management system, it also has a small OLED screen of 128x64 points that could be used to monitor some parameters without having to connect it to a PC.

V1
https://www.amazon.es/MakerHawk-desarrollo-Bluetooth-pantalla-pulgadas/dp/B076P8GRWV/ref=sr_1_1?__mk_es_ES=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=1I6U573FU3RRH&keywords=heltec%2Blora&qid=1567694750&s=gateway&sprefix=helte%2Caps%2C154&sr=8-1&th=1

V2
https://www.amazon.es/MakerHawk-desarrollo-Bluetooth-pantalla-pulgadas/dp/B076T28KWG/ref=sr_1_1?__mk_es_ES=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=1I6U573FU3RRH&keywords=heltec%2Blora&qid=1567694750&s=gateway&sprefix=helte%2Caps%2C154&sr=8-1&th=1

### WROVER modules 
Offer provide 4MB of flash storage and 8MB of PSRAM, in addition to the RAM whose quantity is common to WROOM models, this is the one chosen for version 2 of the Turpial.

Any distributor like Digikey, Mouser, Sparkfun will have them in their catalog.

https://www.espressif.com/en/products/hardware/esp-wrover-kit/overview



## Radio system

In this new reference design the radio interface becomes governed by the Texas Instrument CC1312R chip. The manufacturer offers the possibility of acquiring a development kit directly or through the distributors of the brand:

### LaunchPad CC1312R Development Kit

If it is not possible to acquire for any reason the previous kit there are other compatible ones, such as;

### LaunchPad CC1310R Development Kit
It differs from the previous one in the amount of RAM and flash storage available, the radio performance is the same.

### LaunchPad CC1350 Development Kit and LaunchPad CC1352R1 Development Kit
Both make use of the double band, sub-GHz and 2.4GHz, we will use only the first, the difference between the two is the greater amount of RAM and storage of the second.

### LaunchPad CC1352P Development Kit
In performance is exactly the same as the CC1352R but this also includes a PA (Power Amplifier) that will offer us an output of up to 20dBm.


## Power

If the development board for the MCU ESP32 integrates a battery charge manager, we can power the entire system via USB cable or battery. The maximum capacity that many manufacturers advise is around 1000 mAh of the LiPo type , as is the case with Adafruit or Heltec. TTGO has some plates that support cylindrical batteries of type 18650 that are usually easier to find in any electronics store, often used in power banks or laptops.
