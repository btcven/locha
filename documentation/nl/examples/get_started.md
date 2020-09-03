<br/>

# Aan de slag

[Setup build environment](#setup-build-environment)

[Building en flashing van de Locha Mesh _radio-firmware_](#)

[Configureer de netwerkinterface](#)


---

<br/>

# Setup build environment

We raden aan om deze stap-voor-stap te volgen voordat je het ontwikkelproces start of de firmware build voor je **Locha Mesh**-apparaat. Als je een Turpial of een DIY-versie van welke flavour dan ook hebt, kan dit proces een tikkeltje anders zijn, maar in principe kunnen we het in twee hoofdsecties indelen:

## 1. Environment voor het radiosysteem (CC1312R)

Dit installatieproces is van toepassing op de DIY-versie of Turpial en **alleen** voor het radiosysteem.

Momenteel worden alleen Linux en macOS-systemen ondersteund, voor macOS is een package manager vereist. Installeer de gewenste package manager voordat je de volgende requirements installeert:

 * git
 * wget
 * gcc compiler
 * make
 * python3 (optioneel)

Op Debian gebaseerde systemen bundelen de `build-essential`-pakketten de nodige tools om een build environment te hebben.

### Download en installeer de arm-embedded toolchain

Om de firmware te compilen hebben we de ARM Toolchain GCC compiler nodig. Download en installeer de nieuwste [ARM toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) die beschikbaar is voor je besturingssysteem.

Als eerste downloaden we de toolchain (Linux & macOS):

```sh
$ wget <copied-url> -O arm-toolchain.tar.bz2
```

Unzip bestanden (Linux & macOS):

```sh
$ tar -xjf arm-toolchain.tar.bz2
```

Houd er rekening mee dat je de map met executables aan je `PATH` moet toevoegen. Op een shell als bash of zsh kan dit worden gedaan met behulp van:

```sh
export PATH="${PATH}:/path/to/arm-none-eabi-gcc"
```

Je kunt het permanent doen door het vorige commando toe te voegen aan je `.bashrc` of `.zshrc ` bestand

### Installeer flash tool

Om de gebouwde firmware voor de CC1312R te flashen, moeten we OpenOCD of Uniflash gebruiken. Selecteer een van de tools en lees de instructies voor de installatie

- [Texas Instrument's Uniflash](https://www.ti.com/tool/UNIFLASH)

- [OpenOCD](https://git.ti.com/git/sdo-emu/openocd)

**Let op:** als je ervoor kiest om Uniflash te installeren, zorg er dan voor dat je de nvironment variable `UNIFLASH_PATH` instelt, zodat het build-systeem weet waar het te vinden is.

<br/>

### Building en flashing van de Locha Mesh _radio-firmware_

De **Locha Mesh** radio-firmware is de belangrijkste software voor hardware die compatibel is met het netwerk, het fungeert als router en het geeft ons toegang tot het Mesh-netwerk.

Om de _CC1312R_ of _Turpial_ als netwerkinterface te gebruiken, moeten we de radio-firmware flashen en deze aansluiten op de USB-poort van een computer.

#### Clone & initialiseer het

```sh
$ git clone https://github.com/btcven/radio-firmware.git

$ cd radio-firmware

$ git submodule update --init --recursive
```

#### Build it, flash it & relax

Afhankelijk van de hardware moet je specifieke parameters doorgeven om de broncode te compilen en te flashen.

#### Firmware configuratie

De variabele `BOARD` controleert de hardware die we gebruiken, hier vind je een lijst van ondersteunde boards:

- Board-ondersteuning geleverd door Locha (onze aangepaste hardware): https://github.com/btcven/radio-firmware/tree/master/boards/
- Boards die RIOT-OS ondersteunt: https://github.com/RIOT-OS/RIOT/tree/master/boards

De ` USE_SLIPTTY ` variabele regelt of we SLIP willen gebruiken als onze serial verbinding, of een raw standaard I/O serial verbinding. Om het board als een netwerkinterface te gebruiken, stel je `USE_SLIPPTY=1` in de make command line.

**Let op:*** Je kan deze variabelen op de command line doorgeven of ze exporteren als environment variables*

Dus, om bijvoorbeeld te builden voor het `turpial`-board kunnen we het volgende doen:

```sh
$ make USE_SLIPTTY=1 BOARD=turpial
```

En dan kunnen we het flashen met:

```sh
$ make USE_SLIPTTY=1 BOARD=turpial flash
```

**Let op:** * het gebruik van een ander board is hetzelfde proces, bijvoorbeeld het gebruik van `cc1312-launchpad` zoals ons board zou moeten werken. * /

<br/>

### Configureer de netwerkinterface

Nu we de firmware op ons apparaat hebben, is het tijd om onze netwerkinterface aan te maken en te configureren:

```sh
$ make USE_SLIPTTY=1 BOARD=turpial term
```

Het zal om een sudo (root) wachtwoord vragen, omdat we een tunnel-netwerkinterface moeten creëren. Deze terminal, waar we de netwerkinterface hebben gemaakt, kan niet worden gesloten omdat het de serial verbinding met onze hardware beëindigt.

De volgende stap is om deze netwerkinterface te configureren, we hebben een script onder de `radio-firmware/dist/tools/vaina` directory die dit proces automatiseert, het enige waar we ons zorgen om moeten maken is ons IP-adres.

We moeten het bestand bewerken en het IPv6-adres geven:

```sh
# Willekeurig een uniek lokaal adres genereren (begint met fc00::).
$ hexdump -n 16 -e '2/2 ":%04x"' /dev/urandom | sed "s/^:[a-zA-Z0-9_.-]*:/fc00:/g"
```

Het gegenereerde adres moet worden gekopieerd en geplakt in `radio-firmware/dist/tools/vaina/autoconfigure.sh`, vervolgens moet het script op een nieuwe terminal worden uitgevoerd:

```sh
$ ./radio-firmware/dist/tools/vaina/autoconfigure.sh
```

Het zal opnieuw om rootrechten vragen om de interface te configureren, en als we nu `ip-adres` of `ifconfig` typen op onze console, zouden we een netwerkinterface moeten zien met de naam `sl0` (`tun0` op macOS) met het adres dat we zojuist hebben gegenereerd.

### Geniet ervan

Hiermee kunnen andere peers ons alle gegevens zonder verdere configuratie naar ons IPv6-adres sturen.

Ja kan de **Locha Mesh** gebruiken voor elke service die op IPv6 kan draaien, zoals HTTP / HTTPS, SSH, FTP, RAW Sockets, enz., zelfs voor Bitcoin- en Monero-daemons. Hieronder staan enkele voorbeeld environments om de Locha Mesh te testen:

__Voltooid__

- [Monero GUI via Locha Mesh](monero_gui.md)
- [Bitcoin-daemons die in het Locha Mesh-netwerk praten](bitcoin_daemons.md)

__In uitvoering__
- [Electrum Server](electrum_server.md)
- [Bestanden delen met behulp van het _Torrent_-protocol](torrent_protocol.md)


Help ons met het bouwen van het veerkrachtige netwerk, test je favoriete applicaties in de **Locha Mesh** en deel je setup om anderen te helpen aan de slag te gaan.
