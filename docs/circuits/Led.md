---
layout: page
title: Led
parent: Circuits
---

# Led #
Base boards typically have two LEDs on board. One is used to indicated the status of the system. The other can be controlled by the user. Because, well, who doesn't love blinking an LED? It's a great place to start if you're new to the platform. And maybe you can use to indicate the status of your system, or for debugging.

LEDs have three supported states: on, off or blinking. The rate at which the led blinks can also be configured.

---

## API

### LED state
``` cpp
void set_state(Led::State state)
```
*Sets the state of the lED.*  
`Led::State_Off` turns the LED off  
`Led::State_On` turns the LED on  
`Led::State_Blink` makes the LED blink  

``` cpp
Led::State get_state()
```
*Returns the current state of the LED.*  
See `set_state` for the possible states.

### Blinking
``` cpp
void set_blink_period(uint16_t period)
```
*Sets the blink period.*  
`period` is in milliseconds.  
Note that the LED will not start blinking until `set_state(Led::State_Blink)` is called.

``` cpp
uint16_t get_blink_period()
```
*Gets the blink period.* 

### Colour

``` cpp
void set_colour(Led::Colour colour)
```
*Sets the colour of the LED.*  
`Led::Colour_Green` for green  
`Led::Colour_Red` for red  
`Led::Colour_Orange` for orange  

``` cpp
Led::Colour get_colour()
```
*Gets the current colour setting of the LED.*  
See `set_colour` for the possible colours.

---

## Example

``` cpp
#include <BaseJumper.h>

// create circut handle
Led::Handle my_led(0);

void setup() {  
  basejumper_init();
  my_led.create();  
  my_led.state_set(Led::State_Blink); 
}

void loop() {
  // We already set the led to blink in setup().
  // Nothing to do here but kick back and relax.
}

```