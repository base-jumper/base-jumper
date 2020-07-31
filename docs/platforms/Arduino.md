---
layout: page
title: Arduino
parent: Platforms
---

# Arduino
Arduino hardly needs an introduction. Arduinos have been used by hobbyists worldwide to build all manner of gadgets from [real-life iron man suits](https://www.youtube.com/watch?v=dNtXt5NUALQ) to [rubik cube solving robots](https://www.youtube.com/watch?v=NRRSYEWIQ_w). Arduino started out as a platform for learning, education and general hackery. But don't be too quick to judge. More recent additions to the Arduino family make it a serious prospect for building commercial systems on a budget. The MKR footprint was introduced with a range of different wireless communication technlogies aimed at IoT applications. Recently Arduino announced the release of the powerful new [Portenta H7](https://store.arduino.cc/usa/portenta-h7). With a dual Cortex M7 (480MHz) + M4 processor (240MHz), WiFi, Bluetooth 5.1, ethernet, and the ability to run computer vision applications in TensorFlow Lite - this is a far cry from the humble Arduino Uno. It provides plenty of grunt to meet the requirements of many demanding industrial applications.

## Support
At present, Arduino boards with the Arduino Uno and MKR form factors are supported.

## Getting Started
* Install the BaseJumper library for Arduino.
* Add `#include "BaseJumper.h` at the top of your sketch. Note that this should be inserted automatically when you select the library.
* All BaseJumper code is inside the namespace `BaseJumper`. You might wish to bring this namespace into scope with a using directive: `using BaseJumper;`

## Example
This simple example turns on a LED when a switch is pressed. With the BaseJumper Arduino library installed you can also find it in the Arduino IDE under `File > Examples > BaseJumper > button_led`.

``` cpp
/* Button LED example 
 * 
 * To run this example you will need to wire a switch to Digital Input 1 on your baseboard.
 * Connect one terminal of the switch to the digital input, and the other to 5V. The baseboard 
 * will provide a 5V reference that you can use. Refer to the documentation (link below) for 
 * the base-board pin-outs. When the switch is closed, the LED should turn on. The LED is located 
 * on the baseboard itself. If the baseboard is inside an enclosure, you might need to remove it 
 * from the enclosure to see the LED.
 */  

#include <BaseJumper.h>

using namespace BaseJumper;

// create circut handles
static DigitalInput::Handle button(0);
static Led::Handle led(0);

void setup() 
{  
  basejumper_init();

  led.create();
  button.create();
}

void loop() 
{
    /* Turn led on when button is pressed and off when button is released */
    if (button.get_state())
    {
      led.set_state(Led::State_On);
    }
    else
    {
      led.set_state(Led::State_Off);
    }
    delay(50);
}
```

## Pin Mappings

| Uno Pin* | MKR Pin* | Label | Circuit | Description | 
| ---   | ---             | ---             | ---     | ---         |
|  D13 | D9 | CTL_SCLK | System | SPI control of base board - clock) |
| D12 | D10 | CTL_MISO | System | SPI control of base board - miso |
| D11 | D8 | CTL_MOSI | System | SPI control of base board - mosi |
| D10 | D7 | CTL_CS# | System | SPI control of base board - chip select |
| D9 | D6 | CTL_BUSY# | System | SPI control of base board - busy signal |
| D3 | D5 | CTL_WAKE  | System | Used by baseboard to wake platform |
| D2 | D21 | COM_SER_TXEN | SerialPort | Serial direct control - transmit enable for rs485 |
| D1 | D14 | COM_SER_TX | SerialPort | Serial direct control - transmit | 
| D0 | D13 | COM_SER_RX | SerialPort | Serial direct control - receive | 

**There may be additional connections made for powering & resetting the platform that are not included in this table.*  
**Arduino pin numbers refer to the digital pin number.*