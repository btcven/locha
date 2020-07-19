<br/>

# Get started

[Setup build environment](#setup-build-environment)

[Building and flashing the Locha Mesh _radio-firmware_](#)

[Configuring the network interface](#)


---

<br/>
<br/>

# Setup build environment

We recommend to follow this step by step before starting the development process or build the firmware for your **Locha Mesh** device.

If you have a Turpial or a DIY version of any flavour, this process can be a bit different but in principle we can divide it in two main sections:

## Environment for the radio system (CC1312R)

This setup process is applicable for the DIY version or Turpial and **only** for the radio system.

Currently only Linux and MacOS systems are supported, for MacOS a package manager is required, please install your preferred package manager before installing the next requirements:

 * git
 * wget
 * gcc compiler
 * make
 * python3 (optional)

On Debian based systems, the `build-essential` packages bundles the necessary tools to have a build environment.

### Download and install the arm-embedded toolchain

To compile the firmware we'll need the ARM Toolchain GCC compiler.
Download and install the latest [ARM toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) available for your OS.

- First we download the toolchain (Linux & MacOS X):

```sh
$ wget <copied-url> -O arm-toolchain.tar.bz2
```

- Unzip files (Linux & MacOS):

```sh
$ tar -xjf arm-toolchain.tar.bz2
```

Please note that you will have to add the directory with executables to your `PATH`. On a typical shell like bash or zsh this can be done using:

```sh
export PATH="${PATH}:/path/to/arm-none-eabi-gcc"
```

You can do it permanent adding the previous command to your `.bashrc` or `.zshrc` file

### Install flash tool

In order to flash the built firmware for the CC1312R we need to use OpenOCD or Uniflash, select one of the tools and read the instructions before to install

- [Texas Instrument's Uniflash](https://www.ti.com/tool/UNIFLASH)

- [OpenOCD](https://git.ti.com/git/sdo-emu/openocd)

**Note:** If you choose to install Uniflash, make sure you set the `UNIFLASH_PATH` environment variable, so that the build system knows where to find it.

<br/>
<br/>

# Building and flashing the Locha Mesh _radio-firmware_

The **Locha Mesh** radio firmware is the main software for any hardware compatible with the network, it acts as a router and it lets us access the Mesh network.

To use the _CC1312R_ or _Turpial_ as a network interface, we need to flash the radio firmware and connect it to the USB port of a computer.

## Clone it, initialize it

```sh
$ git clone https://github.com/btcven/radio-firmware.git

$ cd radio-firmware

$ git submodule update --init --recursive
```

## Build it, flash it, enjoy it

Depending on the hardware you have you need to pass specific parameters to compile the source code and flash it.

## Firmware configuration

The `BOARD` variable controls the hardware we're using, here you can find a list of supported boards:

- Board support provided by Locha (our custom hardware): https://github.com/btcven/radio-firmware/tree/master/boards/
- Boards that RIOT-OS supports: https://github.com/RIOT-OS/RIOT/tree/master/boards

The `USE_SLIPTTY` variable controls wether we want to use SLIP as our serial connection, or a raw standard I/O serial connection. To use the board as a network interface set `USE_SLIPPTY=1` in the make command line.

**Note**: *You can pass these variables on the command line or export them as environment variables*

So, for example, to build for the `turpial` board we can do:

```sh
$ make USE_SLIPTTY=1 BOARD=turpial
```

And then we can flash it with:

```sh
$ make USE_SLIPTTY=1 BOARD=turpial flash
```

**Note:** *Using any other board is the same process, for example, using `cc1312-launchpad` as our board should work.*/

<br/>
<br/>

# Configuring the network interface

Now that we have the firmware on our device it's time to create and configure our network interface:

```sh
$ make USE_SLIPTTY=1 BOARD=turpial term
```

It will ask for sudo (root) password as we need to create a tunnel network interface. This terminal, where we created the network interface can not be closed as it kill the serial connection to our hardware.

The next step is to configure this network interface, we have an script under `radio-firmware/dist/tools/vaina` directory which automates this process, the only we need to care about is our IP address.

We must edit the file and give it IPv6 address:

```sh
# Randomly generate a Unique Local Address (begins with fc00::).
$ hexdump -n 16 -e '2/2 ":%04x"' /dev/urandom | sed "s/^:[a-zA-Z0-9_.-]*:/fc00:/g"
```

The address generated should be copied into the `autoconfigure.sh` in `radio-firmware/dist/tools/vaina`, now we run it on a separate terminal:

```sh
$ ./radio-firmware/dist/tools/vaina/autoconfigure.sh
```

It will ask again for root rights to configure the interface, and now if we type `ip address` on our console we should see a network interface named `sl0` (`tun0` on MacOS) with the address we just generated.

# Enjoy it

With this, other peers can send us any data without any more configuration to our IPv6 address.

You can use **Locha Mesh** for any service that can run on IPV6 such as HTTP / HTTPS, SSH, FTP, RAW Sockets, etc, even Bitcoin or Monero daemons. 
As example, we propose some environments to test Locha Mesh:

- [Monero GUI through Locha Mesh]()
- [Bitcoin daemons talking in the mesh network]()
- [Sharing files through the _Torrent_ protocol]()


Help us to build the people's mesh, test your prefered applications over **Locha Mesh** and share your setup for help others.


