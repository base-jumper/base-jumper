---
layout: page
title: Raspberry Pi
parent: Platforms
---

# Raspberry Pi
Raspberry Pi and BaseJumper baseboards go together like hot pie and whipped cream. The Pi packs a lot of grunt in terms of computing power, it runs linux and provides networking interfaces. Perfect for running high-level applications requiring internet or network connectivity! BaseJumper baseboards bolster the Pi's capabilities by adding real-time processing capability and rugged interfaces for connecting to real-world sensor and actuators.

## Supported Boards
* RPi 4B

## Getting Started

### Library Installation
Start by installing the BaseJumper library on your Raspberry pi. The library is available on [github](https://github.com/base-jumper/Platform_RPi). Follow the steps from the README to compile and install the library. A shared library, static library and header files will be installed on the system.

### Template Project
If you're starting a new project, you might like to start with the BaseJumper application template for linux. It is available on [github](https://github.com/base-jumper/linux-template-cxx). It includes source for a sample application, a simple makefile for building the project, and some configuration files for Visual Studio Code. See the README for further details on how to use it.  

If you prefer to configure your own project and build system, continue reading the Code and Build sections below for tips. If you are using the template you can skip over these as it's already taken care of.

### Code
To bring the library into scope in your C++ source file add `#include <BaseJumper.h>` to the top of the file. All BaseJumper code is inside the namespace `BaseJumper`. You might wish to bring this namespace into scope with a using directive: `using namespace BaseJumper;`. Be sure to call `basejumper_init()` before using any circuits.
``` cpp
#include <BaseJumper.h>

using namespace BaseJumper;

int main() 
{  
  basejumper_init();
  // etc  
}
```

### Build
You will need to instruct the compiler/linker to use the BaseJumper library by adding some flags. `-lbasejumper` tells the linker to use the .so shared library.
`-I/usr/local/include/basejumper` points the compiler to the headers.  
#### Example
``` console
$ g++ ./src/App.cpp -lbasejumper -I/usr/local/include/basejumper -o build/App
```




## Pin Mappings

| RPi4B* | Label    | Circuit | Description | 
| ---    | ---      | ---     | ---         |
| GPIO11 | CTL_SCLK | System  | SPI control of base board - clock |
| GPIO9  | CTL_MISO | System  | SPI control of base board - miso |
| GPIO10 | CTL_MOSI | System  | SPI control of base board - mosi |
| GPIO5  | CTL_CS#  | System  | SPI control of base board - chip select |
| GPIO22 | CTL_BUSY# | System | SPI control of base board - busy signal |
| GPIO4  | COM_SER_TXEN | SerialPort | Serial direct control - transmit enable for rs485 |
| GPIO14 | COM_SER_TX | SerialPort | Serial direct control - transmit | 
| GPIO15 | COM_SER_RX | SerialPort | Serial direct control - receive | 
| GPIO16 | RPI_CS | CanBus | SPI control of CAN controller IC on jumper - chip select |
| GPIO20 | RPI_MOSI | CanBus | SPI control of CAN controller on jumper - mosi |
| GPIO19 | RPI_MISO | CanBus | SPI control of CAN controller on jumper - miso |
| GPIO21 | RPI_SCLK | CanBus | SPI control of CAN controller on jumper - clock | 

**There may be additional connections made for powering & resetting the platform that are not included in this table.*  
