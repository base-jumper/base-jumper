---
layout: page
title: AnalogOutput
parent: Circuits
---

# AnalogOuptut

Analog outputs are useful for sending control signals to other devices, such as motor controllers, or simulating sensor outputs.

## Protection
The circuit is protected against overloads. Overloads can occur due to incorrect wiring, a wiring failure or equipment failure which results in the output being shorted to power or ground. When an overload condition is detected, the output is disabled (ie. put in a high-impedance state) and the status is set to `Status_OverloadShutdown`. There is a function for checking the status (`get_status`) and to attempt to recover from an overload the status can be reset (`reset_status`).

---
## API
### enabling the output
``` cpp
void enable(bool val)
```
*Enables or disables the output.*   
`true` to enable  
`false` to disable

``` cpp
bool enabled()
```
*Returns current status of output.*  
`true` if enabled  
`false` if disabled

### output voltage
``` cpp
void set_voltage(float x)
```
*Sets the output voltage.*  
Units are volts.

``` cpp
float get_voltage()
```
*Returns the target output voltage setting.*  
Units are volts. This function returns the value set by `set_voltage`. To read the actual voltage at the pin, use `get_feedback`.

``` cpp
float get_feedback()
```
*Reads the output voltage.*  
Units are volts. The voltage is measured at the output using an ADC. Low-pass filtering is applied, consequently there may be some lag in the feedback.

### status
``` cpp
Status get_status()
```
*Returns the current status.*  
`AnalogOutput::Status::Ok` indicates output is operating normally.   
`AnalogOutput::Status::OverloadShutdown` indicates an overload was detected and the output has been shutdown.

``` cpp
void reset_status()
```
*Resets the status.*  
If the output was disabled due to an overload, the status will be cleared. If `enabled()==true` the output will be re-enabled.