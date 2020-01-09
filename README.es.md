<p align="center">
  <a href="https://locha.io/">
  <img height="200px" src="images/LogotipoTurpial-Color.20-09-19.svg">
  </a>
</p>

<p align="center">
  <a href="https://locha.io/">Sitio del Proyecto</a> |
  <a href="https://locha.io/donate">Donar</a> |
  <a href="https://github.com/sponsors/rdymac">Patrocinar</a> |
  <a href="https://locha.io/buy">Comprar</a>
</p>

<h1 align="center">Locha Mesh</h1>

Locha Mesh es una red de radio para comunicaciones y transacciones de
criptomonedas sin internet e incluso sin electricidad. Estamos trabajando no
solo en un protocolo, también en un *firmware* para dispositivos asequibles
como el *Turpial*, *Harpia*, o sus equivalentes DIY (Hágalo usted mismo, por
sus siglas en inglés). Somos un proyecto *open source* en cada paso que damos
y tenemos como propósito hacer esta herramienta lo más decentralizada posible
para que los usuarios puedan transmitir con libertad.

## ¿Qué hay en el código?

* La aplicación móvil que se comunica con los dispositivos Turpial (el ESP32 y
la radio de SimpleLink) puede ser encontrada
[aquí](https://github.com/btcven/LochaMesh-Chat).

* Nuestro *firmware* para las tarjetas Turpial (ESP32) puede ser encontrado
[aquí](https://github.com/btcven/turpial-firmware).

* Nuestro *firmware* para el módulo de radio (MCUs SimpleLink) puede ser
encontrado [aquí](https://github.com/btcven/radio-firmware). En práctica el
firmware puede ser instalado en cualquier plataforma que pueda correr
[Contiki-NG](https://github.com/contiki-ng/contiki-ng/wiki), pero nuestro foco
actualmente se soportar la plataforma
[SimpleLink](https://github.com/contiki-ng/contiki-ng/wiki/Platform-simplelink).

* El protocolo de enrutamiento y el código para la red *mesh* puede ser
encontrado en nuestros repositorio
[radio-firmware](https://github.com/btcven/radio-firmware) y en nuestro
[*fork* de Contiki-NG](https://github.com/btcven/contiki-ng).

* Encuentra el símulador de Locha Mesh, *datasheets*, y más en la organización
de [Bitcoin Venezuela](https://github.com/btcven).

* La documentación puede ser encontrada
[aquí](https://github.com/btcven/locha/tree/master/documents).

## Dispositivos soportados

### Turpial

Es un dispositivo pequeño y portable basado en el MCU
[ESP32](https://www.espressif.com/en/products/hardware/esp-wroom-32/overview).

El módulo de radio, tipo de batería, y más detalles sobre la tarjeta se
describen 
[aquí](https://docs.google.com/document/d/12sjBhGs7FgMGoDsuASq4MyQFnGfmT4qZNib8H_P6eSw/edit).

<p align="center">
  <img height="533px" width="400px" src="images/turpial-finished-board.jpg">
  <br/>
  <i>Foto actual de la tarjeta</i>
</p>

<p align="center">
  <img height="338px" width="600px" src="images/turpial-size-compared.jpg">
  <br/>
  <i>Comparación del tamaño de la tarjeta Turpial</i>
</p>

## Guía de contribución

Por favor lee nuestra [guía de contribución.](CONTRIBUTING.md)

----
#### :warning: Advertencia :warning:

Por favor ten en cuenta que algunas cosas en este repositorio están en una
etapa muy temprana. Gracias por leer el código y por compartir tus ideas en
Twitter y en la sección de [*Issues*](https://github.com/btcven/locha/issues)
en este repositorio o en cada repositorio en específico.

----
## Mantente en contacto

- Twitter [@Locha_io](https://twitter.com/Locha_io).
- Sitio web [locha.io](https://locha.io).

## Licencia

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
Read me in: [English](README.md)
