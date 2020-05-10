---
layout: page
title: Supply
parent: Circuits
---

# Supply
Base boards have on-board voltage regulators which can be used to power external sensors. These supplies have protection against common faults such as an overload or wiring short-circuit. The supply voltage requirements of sensors varies, depending on the sensor and its output type. For this reason BaseJumper base boards typically provide 2 sensors supplies: one at 5V, and another at 15V. 5V sensors with a ratiometric output are quite common, particularly in 12V systems (such as cars). But in the industrial space where systems run at 24V, many sensors require a supply voltage higher than 5V. Most industrial sensors will specify a range (something like 12V-24V) and while the range varies from sensor-to-sensor, the majority will operate at 15V. Regardless of the output voltage, the API for controlling a supply is the same. A function is available for reading the nominal output voltage of the supply.

## API

### enabling the supply
``` cpp
void enable(bool val)
```
*Sets the state of the supply. `true` enables the supply. `false` disables the supply.*

``` cpp
bool enabled()
```
*Returns the current state of the supply. `true` if it is enabled. `false` if it is disabled.*

### fault checking
``` cpp
Supply::Status get_status()
```
*Gets the status of the supply.*  
`Supply::Status_Ok` if supply is functioning normally.  
`Supply::Status_OutOfTolerance` indicates that the supply voltage has deviated outside the expected normal range.  
`Supply::Status_OverloadShutdown` indicates an overload fault occured and the supply has shutdown to protect itself.  

``` cpp
void reset_status()
```
*Resets the supply status.*
This can be called to attempt to recover from an abnormal condition.
If the supply was shutdown due to an overload, calling `reset_status` will re-enable the supply. 
If the supply has an out-of-tolerance status, calling `reset_status` will return the status back to `Status_Ok`.

### output voltage monitoring

``` cpp
uint16_t get_nominal_voltage()
```
*Returns the target output voltage of the supply in mV*  
example: `5000` for a 5V supply.

``` cpp
uint16_t get_voltage()
```
*Returns the actual supply output voltage in mV.*  
If the supply is operating correctly this should be very close to the nominal voltage.