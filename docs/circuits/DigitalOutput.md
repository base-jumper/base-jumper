---
layout: page
title: DigitalOutput
parent: Circuits
---

# DigitalOutput
The `DigitalOutput` circuit provides a tri-state output which can be driven actively high, low or put into a high-impedance state. The output is fully protected against short circuits and overloads, and has diagnostics to verify correct operation. They can be used to send digital signals to external controllers, switch solid state relays, or even drive small LED indicators.  

Digital outputs are not intended to drive heavy loads, or inductive loads. See [LoadDriver]({{"docs/circuits/LoadDriver.html" | relative_url}}) instead.


### Protection
Digital outputs have both hardware and firmware measures to protect the outputs against overloads, or short circuits to the ground, 5V or 15V power rails. The hardware limits the amount of current that the digital output will source or sink to approximately 10mA. If the overload condition persists, the firmware will intervene and put the output in a high-impedance state. API function `get_status` is available for checking if an overload has occurred and `reset_status` will clear the overload shutdown status and attempt to re-enable the output.

## API

```cpp
void set_state(DigitalOutput::Tristate state)
```
*Sets the state of the digital output.*  
`state` is an enum with the following options;  
* `DigitalOutput::State_HighZ` for high-impedance
* `DigitalOutput::State_High` to drive high
* `DigitalOutput::State_Low` to drive low

```cpp
DigitalOutput::Tristate get_state()
```
*Gets the current state of the digital output.*  
See `set_state` for the possible states.

```cpp
void toggle_state()
```
*Toggles the state of the output.*  
If the state is `State_HighZ` (high-impedance) it remains unchanged. Otherwise, `State_High` is flipped to `State_Low` and visa-versa.

```cpp
DigitalOutput::Status get_status()
```
*Gets the status of the output.*  
Returns a `Status` enum with the following possible values;
* `DigitalOutput::Status_Ok` for normal operation
* `DigitalOutput::Status_OverloadShutdown` if output has been disabled (changed to `State_HighZ`) due to an overload condition.

```cpp
void reset_status()
```
*Resets status.*  
If the status was previously `Status_OverloadShutdown`, it will be reset to `Status_Ok` and the output will be re-enabled. Operation may resume as normal if the condition that triggered the overload condition has since been cleared. Otherwise the overload protection will again be triggered and the status will return to `Status_OverloadShutdown`.