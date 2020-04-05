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

### set ###
``` cpp
void set(Led::State state)
```
*Sets the status of the led.*  
`state` is the new state for the LED. It can be `State_Off`, `State_On` or `State_Blink`.


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