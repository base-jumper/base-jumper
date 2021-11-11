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

### High State Configuration
The output can be operated either as push-pull or open-collector. Both modes drive the output to a low voltage level when the state is set to `false`. But they behave differently when the output is set to `true`. In push-pull the output is driven to a high voltage, whereas open-collector puts the output in a high-impedance state. 

Open-collector is useful when sending a signal to the input of another electronic device which might be operating at a different voltage level. In this configuration the receiving device should contain its own pull-up resistor to bias the input to a suitable voltage level. 

Use push-pull only when you are certain that the device receiving the signal can handle the high voltage level (typically 5V). This mode can also be useful for driving optocouplers, LEDs and solid-state relays.

`set_high_mode` is used to select between modes. `Driven` selects push-pull and `Floating` selects open-collector.

## API
### Configuration
```cpp
void set_high_mode(HighMode mode)
```
*Configures the output level when state is set to `true`.*  
`mode` is an enum with the following possible values;
* `DigitalOutput::HighMode::Driven` to actively drive the output to a high level
* `DigitalOutput::highMode::Floating` to leave the output in a high-impedance state

```cpp
DigitalOutput::HighMode get_high_mode()
```
*Gets the configured behavior for when the output is set to `true`.*  
See `set_high_mode` for details of the returned enum.

```cpp
void set_state(bool value)
```
*Sets the state of the digital output.*  
`true` drives the output to a high level (`HighMode::Driven`) or high-impedance (`HighMode::Floating`) depending on the selected `HighMode` (see `set_high_mode`).   
`false` drives the output low  

```cpp
bool get_state()
```
*Gets the current state of the digital output.*  
See `set_state` for details of the states.

```cpp
void toggle_state()
```
*Toggles the state of the output.*  
If the state is `State::HighZ` (high-impedance) it remains unchanged. Otherwise, `State::High` is flipped to `State::Low` and visa-versa.

```cpp
DigitalOutput::Status get_status()
```
*Gets the status of the output.*  
Returns a `Status` enum with the following possible values;
* `DigitalOutput::Status::Ok` for normal operation
* `DigitalOutput::Status::OverloadShutdown` if output has been disabled (changed to `State::HighZ`) due to an overload condition.

```cpp
void reset_status()
```
*Resets status.*  
If the status was previously `Status::OverloadShutdown`, it will be reset to `Status::Ok` and the output will be re-enabled. Operation may resume as normal if the condition that triggered the overload condition has since been cleared. Otherwise the overload protection will again be triggered and the status will return to `Status::OverloadShutdown`.