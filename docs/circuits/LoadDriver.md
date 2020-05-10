---
layout: page
title: LoadDriver
parent: Circuits
---

# Load Driver
`LoadDriver`s are used to switch power to a load. Think of them like a relay, but using solid-state devices rather than an electromechanical device. Unlike relays, they don't have contacts that wear out over time so you get virtually unlimited cycles. `LoadDriver`s also come with built in protection against overloads, and diagnostics. They provide a highly reliable means of operating a wide range of loads, such as lamps, indicators, solenoids and valves.

### Topology
`LoadDriver`s come in two varieties: high-side and low-side. A high-side load driver switches power to the positive terminal of the load and the negative terminal is typically hard-wired to ground. A low-side driver switches power to the negative terminal and the positive terminal is hard-wired to power. Check the [documentation page]({{"/docs/boards/Boards.html" | relative_url}}) for your particular base board to find out which topology is used. 

### Protection
`LoadDriver`s are protected against overload. If the current draw through the load driver is too high, or the temperature gets too hot, it will shutdown and latch off. Common causes of an overload are wiring errors, short circuits in the wiring, driving a load that draws too much current for the driver or a load failure. Functions are available for checking the status of the load driver, which will report if the driver is in an overload shutdown condition. To attempt to recover from a fault, a function can be invoked to reset the load driver's status. If the cause of the fault has been cleared, then the driver will turn back on and function as normal. But if the fault that caused the overload remains, the overload shutdown will once again trigger and the driver will turn back off.   

### Pulse Width Modulation
`LoadDrivers` support PWM. By default the duty cycle is set to 100% so that the load driver turns fully on when it is enabled. Use the `set_duty` function to adjust the duty cycle. A [freewheeling diode](https://www.electronicshub.org/flyback-diode-or-freewheeling-diode/) is fitted to handle flyback from inductive loads. 

## API

## enabling the output
``` cpp
void enable(bool val)
```
*Sets the state of the output.*  
`true` to engerize the load. `false` to de-energize the load.

``` cpp
bool enabled()
```
*Returns the current status of the output.*
`true` if the output is enabled, `false` if it is disabled.  
If the output was enabled using `enable(true)` but was then shut-down due to an overload, `enabled()` will still return `true` even though the load has been de-energized. To check for overload shutdown, use `get_status()`.

### PWM

``` cpp
void set_duty(uint16_t duty)
```
*Sets the duty cycle of the PWM output.*  
Units are 0.01%. `0` for 0% and `10000` for 100%.

``` cpp
uint16_t get_duty()
```
*Returns the current PWM duty cycle.*  
Units are 0.01%. `0` for 0% and `10000` for 100%.

### status
``` cpp
LoadDriver::Status get_status()
```
*Returns the current status.*  
`LoadDriver::Status_Ok` if load driver is functioning normally.
`LoadDriver::Status_OverloadShutdown` indicates an overload was detected and the load driver has been shutdown to protect it.

``` cpp
bool reset_status()
```
*Resets the status, to try to recover from a fault condition.*  
When the load driver status is `LoadDriver::Status_OverloadShutdown` calling this function will reset the status back to `LoadDriver::Status_Ok`. Assuming the output is still enabled (ie. `enabled() == true`), then the driver will attempt to energize the load. 