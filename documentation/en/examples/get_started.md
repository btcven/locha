# Get started

[1. Setup build environment]()

[2. Configuring]()

[3. Compiling & Flashing]()


---

## 1. Setup build environment

We recommend to follow this step by step before starting the development process or build the firmware for your **Locha Mesh** device.

If you have a Turpial or a DIY version of any flavour, this process can be a bit different but in principle we can divide it in two main sections:

### Environment for the radio system (CC1312R)

This setup process is applicable for the DIY version or Turpial and **only** for the radio system.

Currently only Linux and MacOS systems are supported, for MacOS a package manager is required, please install your preferred package manager before installing the next requirements:

 * git
 * wget
 * gcc compiler
 * make
 * python3 (optional)

On Debian based systems, the `build-essential` packages bundles the necessary tools to have a build environment.

#### Download and install the arm-embedded toolchain

To compile the firmware we'll need the ARM Toolchain GCC compiler, we need the latest since the packaged version by most distributions are quite outdated. [Here][arm-toolchain] we can download the compiler, just copy the link of the most recent version for your OS.

[arm-toolchain]: https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads

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

#### Install Uniflash

Install TI's Uniflash tool for your operating system. This tool allows you to upload the built firmware to the _CC1312R_ radio module or compatible. Please read carefully the instructions in the [Uniflash site](https://www.ti.com/tool/UNIFLASH) before to continuing the follow steps

## The Locha Mesh _radio-firmware_

The **Locha Mesh** radio firmware is the main software for any hardware compatible with the network.

With the aim to convert the _CC1312R_ radio system into a network interface, we need to connect an empty LaunchPad or Turpial device through the USB port with our computer.

### Clone it, initialize it

```sh
$ git clone https://github.com/btcven/radio-firmware.git

$ cd radio-firmware

$ git submodule update --init --recursive
```

### Build it, flash it, enjoy it

Depending on the hardware you have, follow the steps in the section **(a)** Turpial, or **(b)** for the DIY compatible systems

#### a) Turpial

Build it with:

```sh
$ make
```

Flash it with:

```sh
$ make flash
```


#### b) CC1312R-LaunchPad or compatible system

```sh
$ make BOARD=cc1312r-launchpad
```

Flash it with:

```sh
$ make make BOARD=cc1312r-launchpad flash
```

PD: If you are using other compatible LaunchPad; CC1352R, CC1352P, etc. change the `BOARD` variable to `cc1352p-launchpad`, `cc1352r-launchpad`, etc. Checkout the [RIOT-OS documentation](#1) for a list of supported MCUs.

### environment for the WiFi interface (esp32)
