---
layout: page
title: DigitalInput
parent: Circuits
---

# Digital Input

`DigitalInput` circuits are useful for monitoring switches and sensors with digital outputs such as proximity sensors.

### Debouncing
When a mechanical switch closes, the contacts will typically bounce multiple times before settling in the closed position. This phenomenon is known as [switch bounce](https://www.allaboutcircuits.com/technical-articles/switch-bounce-how-to-deal-with-it/). Signal conditioning is required to filter out the spurious transitions that can result from switch bounce. 

`DigitalInput` circuits have a hardware low-pass filter on-board. However, since the same input may be used for other high-speed inputs such as frequency measurements or an encoder interface, by default the filter cut-off is set quite high. `DigitalInput` drivers provide additional switch debouncing in firmware, which should be enabled when interfacing with mechanical switches.

The debouncing algorithm waits for a transition to occur on the input. When a transition is detected the algorithm waits for a specified *settling time* for the voltage to stabilize. The settling time should be set to the longest expected switch bouncing duration. Once the settling time is up, the input is sampled 3 times successively at 10ms intervals to determine the new switch state. If the measurements are inconsistent, then the process repeats until a stable measurement is obtained.

## API

### read state
``` cpp
bool get_level()
```
*Returns the state of the digital input.*

``` cpp
void set_debounce(bool)
```
*Enables or disables switch debounce functionality. This setting is retained between power cycles.*

``` cpp
bool get_debounce()
```
*Returns `true` is debounce is enabled, `false` if it is not.*

``` cpp
void set_settling_time(uint8_t time)
```
*Sets the switch settling time for debouncing. This should be set to the maximum expected switch bounce duration. The default values are conservative.*
Range: 10-100ms

``` cpp
void get_settling_time()
```
*Returns the switch settling time for debouncing.* 
Range: 10-100ms