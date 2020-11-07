---
layout: page
title: Encoder
parent: Circuits
---

# Encoder
The encoder circuit interfaces to a [rotary encoder](https://en.wikipedia.org/wiki/Rotary_encoder) for measuring either position or speed. It supports incremental encoders with quadrature outputs and an optional index pulse (the most common type of encoder). Typical uses include measuring the speed/position of a motor shaft, or for interfacing to a rotary knob for user input. The `Encoder` circuit uses dedicated hardware counters for tracking pulses, and is capable of high speed operation with minimal processor loading.

### Circuit Requirements
The `Encoder` circuit uses the same physical pins and hardware as [`DigitalInputs`]({{ "docs/circuits/DigitalInput.html" | relative_url}}). Using the encoder consumes three DigitalInput circuits: one for each of the 'A' and 'B' quadrature signals and one for the index pulse 'Z'. Even if the index input is not used, the associated DigitalInput circuit must be reserved for the encoder and cannot be used for other purposes.

### Supported Electrical Interfaces
Rotary encoders are available with 3 different output types: NPN open collector, push-pull (totem) or line driver (differential). Open collector outputs are supported. Push-pull is supported, providing the output voltage is within the operating range of the `DigitalInput` circuit. Line driver output is not supported.

### Pin Configurations
The expected input waveforms for clockwise rotation are shown in the diagram below.  
![Expected encoder output]({{assets/images/encoder_signal.jpg | relative_url}})  
Some encoders may have outputs with the polarity of one or more of the signals inverted. For encoders with open collector outputs, it is often the case that the index signal is inverted. The `Encoder` API has a `set_pin_config` function for configuring the polarity of the signals. If any of the A, B or Z (index) signals are inverted compared to what is shown in the diagram above, then inversion should be set to `true` for that signal in `set_pin_config`.

### Operating Modes
The encoder circuit supports three different operating modes for measuring position, and one operating mode for measuring speed (4 modes total).

**`Mode::Relative`** is used to measure relative position. No index pulse is used (even if it is present, it will be ignored). Use this mode if you only want to measure relative movement. Alternatively, this mode can also be used if you want to measure absolute position, but *don't* want the index pulse to set your zero position. In this case, the `set_position` function would be called to set the zero position.  

**`Mode::SingleTurn`** measures rotation over a single turn. It uses an index pulse, and the count is reset every time the index pulse occurs.

**`Mode::MultiTurn`** measures rotation over multiple turns. It uses the first index pulse to reset the count. All subsequent index pulses are ignored.

**`Mode::Speed`** measures speed. Phase pulses are counted over a specified interval (set using `set_speed_interval`). At the end of each interval, the number of recorded pulses is scaled to get a speed measurement in pulses per second. The count is reset and the process repeats over the next interval. 

When using one of the position measurement modes, the position is obtained using `get_position`. In speed measurement mode, speed is obtained using `get_speed`. Calling `get_speed` when in position mode (or visa-versa) will return an invalid value that should not be used.

### Index Pulse
`Mode::SingleTurn` and `Mode::MultiTurn` both use an index pulse. Until the first index pulse is seen, the reported position is invalid (it will start counting from zero and reset on the first index pulse). The `seen_index` function can be polled to check if an index pulse has occurred. Once `seen_index` returns `true`, the value from `get_position` is then a valid. 

### Counts
`get_position`/`get_speed` report the position/speed in units of counts and counts/second, respectively. Results are returned as a signed integer. Negative values represent rotation in the anti-clockwise direction.

### Pulse Multiplier
A multiplier can be applied to the number of counts that are reported to effectively increase the resolution of the encoder. The multiplier options are described below.

**`Multiplier::X1`**: Counts match the number of pulses-per-revolution (ppr) on the encoder label. For example, a 1000ppr encoder will generate 1000 counts per revolution.

**`Multiplier::X2`**: Counts are doubled. For example a 1000ppr encoder will generate 2000 counts per revolution. This is equivalent to counting both rising and falling edges of a single phase signal. 

**`Multiplier::X4`**: Counts are quadrupled. For example a 1000ppr encoder will generate 4000 counts per revolution. This is equivalent to counting both rising and falling edges of both phase signals. 

There is no performance penalty in terms of software overheads, interrupt loading, etc associated with selecting the highest multiplier. 


### Configuration Sequence
Changing configurations and operating modes on-the-fly is possible, but not recommended. Each time the mode is changed, the count will be reset to zero. The recommended start-up sequence is as follows;

1) Create the encoder resource  
2) Set the pin configurations  
3) Set the operating mode  
4) Set the multiplier  
5) Set the speed interval if operating in speed mode.  
6) Run a zeroing sequence while polling `seen_index`. How the encoder is zeroed is application specific. You must decide how to best do this for your application. This is only applicable for modes that use the index pulse and can be skipped for relative position measurements and speed measurements.  
7) If measuring position and an offset is required (i.e. zero count is somewhere other than the index location), call `set_position`.  
8) Start taking measurements from `get_position` or `get_speed`  

The operating mode, pin configurations and multiplier will be remembered between power cycles. However, there is no harm in setting them each time in the power up sequence.

## Hardware Customization
The recommended hardware customization options for `DigitalInput` circuits used as encoder inputs are below. This setup has been tested with pulse rates up to 15KHz (ie. 60K counts/sec in 4x mode).

| Component Name | Description | Value |
| --- | --- | --- |
| Rhyst | Sets hysteresis | 78.7k |
| Rthres | Sets threshold | 51k |
| Cfilt | Sets filtering | 100pF | 

In most cases, this will be the default configuration that the base board ships with. So providing it is wired up correctly, everything should work just fine out-of-the-box.

If changes are made to the circuit, it is important not to add too much filtering. Filtering can attenuate the phase signals, and increase propagation delays.  

## API

### Configuration

```cpp
void set_pin_config(Encoder::PinConfig config)
```
*Configures the pins to suit the encoder output signals.*  
`config` is a struct with the following fields;  

| Type | Name | Description |
| --- | --- | --- |
| bool | a_inverted | `true` if phase A signal from encoder is inverted |
| bool | b_inverted | `true` if phase B signal from encoder is inverted |
| bool | z_inverted | `true` if index signal from encoder is inverted |

This setting is retained between power cycles.

```cpp
Encoder::PinConfig get_pin_config()
```
*Returns the current pin configuration.*  
Refer to `set_pin_config` for details of the returned struct.

```cpp
void set_mode(Encoder::Mode mode)
```
*Sets the operating mode.*  
Supported operating modes are;  
* `Encoder::Mode::Relative` for relative position measurements  
* `Encoder::Mode::SingleTurn` for absolute position measurements over a single turn  
* `Encoder::Mode::MultiTurn` for absolute position measurements over multiple turns  
* `Encoder::Mode::Speed` for speed measurements  

This setting is retained between power cycles.

```cpp
Encoder::Mode get_mode()
```
*Gets the current operating mode.*  
Refer to `set_mode` for the supported operating modes.

```cpp
void set_multiplier(Encoder::Multiplier mult)
```
*Sets pulse count multiplier.*  
Supported multipliers are;  
* `Encoder::Multiplier::X1` counts per turn matches encoder ppr spec
* `Encoder::Multiplier::X2` counts per turn is double encoder ppr spec
* `Encoder::Multiplier::X4` counts per turn is quadruple encoder ppr spec  

This setting is retained between power cycles.
```cpp
Encoder::Multiplier get_multiplier()
```
*Gets pulse count multiplier.*  
See `set_multiplier` for supported multipliers.

```cpp
void set_speed_interval(uint16_t ms)
```
*Sets the interval used for speed measurements.*  
Speed is measured by accumulating pulses over an interval. Depending on the expected number of pulses per second it may be desirable to adjust the interval to improve performance. Increasing the interval improves measurement resolution and may be necessary when the pulses per second is low. Reducing the interval improves responsiveness and may be necessary when a fast update rate is required and the pulses per second is high.  
Units are milliseconds. Valid range is 1-100ms.  
This setting is retained between power cycles.

```cpp
uint16_t get_speed_interval()
```
*Gets the interval used for speed measurements.*  
Refer to `set_speed_interval` for details.

### Measurements

```cpp
int32_t get_position()
```
*Gets the position.*  
Valid only when using one of the position measurement modes. 
Units are counts, which may be a multiple of the encoder pulses per revolution (ppr).  
Positive numbers indicate positions clockwise from the index or zero location. Negative numbers indicate positions anti-clockwise from the index or zero location.

```cpp
void set_position(int32_t counts)
```
*Sets the position.*  
Valid only when using one of the position measurement modes. Calling this function will apply an internal offset to the current position measurement to produce the desired position specified by `counts`.  
For `Mode::SingleTurn` or `Mode::MultiTurn` it is advisable to wait until `seen_index` return true before configuring the offset. For `Mode::Relative` this function can be called at any time.

```cpp
bool seen_index()
```
*Indicates if an index pulse has occurred.*  
Returns `true` if an index pulse has been seen since the last time `reset_seen_index` was called. In absolute position measurement modes that use the index pulse, the value returned by `get_position` may not be valid until an index pulse has been received. `seen_index` can be polled to determine when this has occurred.

```cpp
void reset_seen_index()
```
*Resets the value returned by `seen_index`.*

```cpp
int32_t get_speed()
```
*Get speed measurement.*  
Valid only when mode is set to `Mode::Speed`. Result is in counts per second, where counts may be a multiple of the encoders pulses per revolution (ppr). Positive values indicate rotation in the clockwise direction. Negative values indicate rotation in the counter-clockwise direction.