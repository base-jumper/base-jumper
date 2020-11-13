---
layout: page
title: AnalogInput
parent: Circuits
---

# AnalogInput

`AnalogInput` circuits are used for monitoring outputs of analog sensors. 

### Ratiometric Sensors
Many sensors have the property that their output voltage is proportional to their supply voltage. Sensors of this type are said to be *ratiometric*. The simplest example is a potentiometer. The accuracy of a measurement from a ratiometric sensor can be improved by measuring both the sensor output and the supply voltage, and then dividing the output by the supply voltage to obtain a ratio. Any variation in the output signal due to error in the supply voltage will not affect the ratio. 

The `AnalogInput`API provides an option for reading the sensor output as a ratio of the supply voltage. It assumes the sensor is powered from the base boards 5V external supply.

### Circuit Gain
The circuit hardware contains signal conditioning stages which can be configured to apply a gain to the signal. There is a resistor network which can be configured as a resistor-divider with a gain of less than 1. There is also a non-inverting op-amp circuit which can provide a gain of over 1. The `Boards` page of each base board specifies the default gain of its `AnalogInput` circuit. If required, the hardware can be modified to adjust the gain to suit a particular application. 

The API function `get_voltage` is used to read the input voltage. It reports the voltage at the output of the sensor, as measured at the connector pin of the base board. This function accounts for any gain that is applied to the input in the signal condition circuits. If any adjustments are made to the gain, the baseboard should be updated with the new gain setting using the `set_gain` function.

### Filtering
`AnalogInput` circuits provide two layers of filtering. First, there is the signal conditioning circuitry on the board. The default circuit population provides a first-order low-pass RC filter. Second, filtering can be applied in software by the `AnalogInput` firmware running on the base board. To enable software filtering, see the filtering API below.

#### Lowpass Filter
The software low-pass filter uses the IIR filtering algorithm below;
![Simple IIR filter formula]({{"assets/images/eqn-iiravg-implementation.png" | relative_url}})  
Where `y` is the filter output and `x` is the filter input. alpha is a filtering coefficient, which can be adjusted to control the level of filtering. Reducing alpha reduces the cut-off frequency of the filter.  

The filter coefficients are normalized for a time step of 1 second, however the actual time steps are significantly smaller.

---
## Hardware Customization
This section describes the customizations that can be made to the circuit's hardware. Only general information is provided here. Component designators and board assembly drawings can be found on the [boards]({{"docs/boards/index.html" | relative_url}}) page for a specific base board.

### Resistor network


### Amplifier gain

## Hardware
* Configure gain
* Configure filtering

---

## API

### read the input
``` cpp
float get_voltage()
```
*Returns the voltage as measured at the connector pin of the analog input.*  
Units are volts. The gain is used to calculate the voltage from the ADC measurement. Result will be filtered if filtering is enabled.

``` cpp
int16_t get_raw()
```
*Returns the ADC measurement.*  
The result is in units of ADC bits. Result will be filtered if filtering is enabled.

``` cpp
int16_t get_ratio()
```
*Returns the the voltage as measured at the analog input connector pin, as a ratio of the supply voltage on the 5V supply.*  
Units are 0.01%. Range is 0..10000.  
Result will be filtered if filtering is enabled.

### filtering
``` cpp
void set_filt(AnalogInput::FilterType filter_type)
```
*Add or remove a filter.*  
`AnalogInput::FilterType::None` for no filtering.  
`AnalogInput::FilterType::Lowpass` for IIR low-pass filter.

```cpp
FilterType get_filt()
```
*Returns the current filter setting.*  
See `set_filt` for supported filter types.

``` cpp
void set_filt_coeff(uint8_t index, float val)
```
*Configures the filter coefficients of the active filter.*  
`index` specifies which filter coefficient to set. Coefficients are numbered starting from `0`.  
`val` is the new value for the filter coefficient.  

`AnalogInput::FilterType::Lowpass` has a single coefficient (`index` of 0). Reducing this coefficient lowers the cut-off frequency.

``` cpp
float get_filt_coeff(uint8_t index)
```
*Returns a filter coefficient of the active filter.*  
`index` specifies which filter coefficient to return. Coefficients are number starting from `0`.  
When requesting an `index` that exceeds the supported number of coefficients for the active filter, a result of 0.0 will be returned. 

### gain

``` cpp
void set_gain(float val)
```
*Configures the circuit gain.*  
The circuit gain is used for back-calculating the analog input pin voltage (as reported by `get_voltage`) from the ADC measurement. It is only neccessary to configure the gain if adjustments are made to the signal conditioning hardware of the analog input circuit on the PCB. The gain paramter is kept in non-volatile storage, so it is only neccessary to call this function once after reconfiguring the hardware. The updated gain will be used in all subsequent computations (even after a power cycle).

``` cpp
float get_gain()
```
*Returns the current circuit gain setting.*