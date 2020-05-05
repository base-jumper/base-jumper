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

The API function for reading an `AnalogInput` reports the voltage at the output of the sensor, as measured at the connector pin of the base board, before the  

### ADC Gain and Offset

---

## Hardware
* Configure gain
* Configure filtering

---

## API

### read voltage
``` cpp
int16_t read_volts()
```
*Returns the voltage as measured at the connector pin of the analog input in millivolts.*

### read ratiometric
``` cpp
int16_t read_ratio()
```
*Returns the ratio of the measured voltage at the analog input connector pin, to the 5V external supply. Units are 0.01%. Range is 1-10000.*