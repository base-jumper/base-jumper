---
layout: page
title: ServoOutput
parent: Circuits
---

# ServoOutput

`ServoOutput` outputs a digital pulse sequence which can be used to control servo motors or electric speed controllers (ESC). The output is a square wave pulse with a width of 1-2ms which repeats every 20ms. Adjusting the width of the pulse will adjust the position or speed of a servo motor or ESC, respectively.  
![Servo motor signal]({{"assets/images/servo_motor.jpg" | relative_url}})

### Ratio
For convenience, the API works with percentages rather than pulse durations. 0% corresponds to the minimum pulse duration of 1ms. 100% corresponds to the maximum pulse duration of 2ms. A linear relationship exists for values between 0% and 100%. A scale factor of 100x is applied to the percentage (maximum 10000) to give a resolution of 0.01% when working with 16-bit integers. The term *ratio* is used to describe the scaled percentage.

### Circuit Requirements
`ServoOutputs` use the same physical hardware as `DigitalOutputs`. When using a circuit as a `ServoOutput`, it cannot be used a `DigitalOutput` at the same time.

---

## API


```cpp
void enable(bool val)
```
*Enables or disables the output.*  
The pulse will appear at the output once `enable` is called with an argument of `true`. Prior to doing so, it is advisable to call `set_ratio` so that the output is defined. To disable the output call `enable` with an argument of `false`.

```cpp
bool enabled()
```
*Returns current enable status.*  
`true` if circuit is currently enabled, `false` if it is disabled.

```cpp
void set_ratio(uint16_t val)
```
*Sets the output pulse duration.*   
Accepts a scaled percentage. A value of 0 corresponds to 0% or the minimum pulse duration of 1ms. A value of 10,000 corresponds to 100% or the maximum pulse duration of 2ms. Units are x0.01%.

```cpp
uint16_t get_ratio()
```
*Gets the output pulse duration.*   
Returns the value previously set using `set_ratio`. See `set_ratio` for details.