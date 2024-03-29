---
layout: page
title: DigitalInput
parent: Circuits
---

# Digital Input

`DigitalInput` circuits are useful for monitoring switches and sensors with digital outputs such as proximity sensors.

## Reading the Input
There are two ways to read the input. The simplest is the `get_state()` function. This returns the instantaneous state of the input. `true` represents a high signal and `false` represents low.  

The second option is `get_edges()`. This function returns not only the current state of the input but also the number of edges (or state transitions) that have occurred. By monitoring the returned edge count, the user application can ensure that it never misses any events - even if they are very fast. By contrast, if the `get_state()` function was used to capture fast events then the application would need to call this function at very fast interval which could put considerable load on the system. Therefore, `get_edges()` is the preferred option in cases where the input signal can change quickly and the application must respond to *every* change.

The returned edge count includes both rising (low to high) and falling (high to low) edges. By considering the current state it is easy to calculate how many of each edge type (rising and falling) have occurred. Calling `get_edges(true)` (ie. argument set to `true`) will *reset* the edge count after it is returned. Consecutive calls to `get_edges(true)` will then give the number of edges between calls. Otherwise the edge count will accumulate, and potentially rollover back to zero if it reaches 2^32.

Changing the circuit configuration (such as changing the biasing) may result in the edge count incrementing - even if the input signal itself hasn't changed. Best practice is to configure the `DigitalInput` as desired (including biasing), wait a short amount of time for the voltages to settle, clear the edge count, then start reading the edge count. To clear the edge count call `get_edges(true)`. The returned data can simply be discarded. 

## Debouncing
When a mechanical switch closes, the contacts will typically bounce multiple times before settling in the closed position. This phenomenon is known as [switch bounce](https://www.allaboutcircuits.com/technical-articles/switch-bounce-how-to-deal-with-it/). Signal conditioning is required to filter out the spurious transitions that can result from switch bounce. 

`DigitalInput` circuits have a hardware low-pass filter on-board. However, since the same input may be used for other high-speed inputs such as frequency measurements or an encoder interface, by default the filter cut-off is set quite high. `DigitalInput` drivers provide additional switch debouncing in software, which should be enabled when interfacing with mechanical switches. For interfacing with sensors that do not have mechanical contacts (hall sensors, inductive sensors, etc.) debouncing can be disabled to give a faster response.

The debouncing algorithm waits for a transition to occur on the input. When a transition is detected the algorithm waits for a specified *settling time* for the voltage to stabilize. The settling time should be set to the longest expected switch bouncing duration. Once the settling time is up, the input is sampled 3 times successively at 10ms intervals to determine the new switch state. If the measurements are inconsistent, then the process repeats until a stable measurement is obtained.

## Programmable Biasing 
Biasing determines the state of the input when there is no signal driving it (i.e. the input is left floating). If biasing is set to `High` a pull-up resistor will be enabled to give a default state of `true`. Similarly if biasing is set to `Low` a pull-down resistor is enabled to give a default state of `false`. With biasing disabled the input will be left floating and will not have a well defined state until it is driven by a signal. 

Biasing should be set to `High` if the signal is coming from a sensors with open-collector output, open-drain output or a switch that connects to ground. Set biasing to `Low` if the signal is coming from an switch that connects to power.

## Hardware Customization
This section describes the customizations that can be made to the base board's hardware. Only general information is provided here. Component designators and board assembly drawings can be found on the [boards]({{"docs/boards/index.html" | relative_url}}) page for a specific base board.

## Switching thresholds
Resistor values can be changed to adjust the switching thresholds and  hysteresis. The table below provides resistor values for a few different configurations. The `General` configuration is a good all-round configuration suitable for high speed inputs (it is normally the default configuration that the base board ships with). `3.3V CMOS` and `TTL` are alternative configurations to closer match the respective standards.

| Component Name | Description | General | 3.3V CMOS | TTL |
| --- | --- | --- | --- | --- | --- |
| Rhyst | Sets hysteresis | 51K | 56k | 75k | 
| Rthres | Sets threshold | 78.7K | 15k | 10k |

## Filtering
The circuit contains a low pass RC filter. The filter cut-off frequency can be adjusted by changing a capacitor value. The `General` configuration (typically the default) below is suitable for high speed signals such as encoder inputs. For lower speed signals, such as mechanical switches, where increased noise immunity is desired, the filtering can be increased. `General` provides a filter cut-off frequency of approximately 70KHz, `Low-speed` is 7KHz.

| Component Name | Description | General | Low-speed |
| --- | --- | --- | --- | 
| Cfilt | Sets filtering | 100pF | 1nF |



---

## API

``` cpp
void set_bias(DigitalInput::Bias bias)
```
*Sets the biasing.*  
`bias` is an enum with the following options;  
* `DigitalInput::Bias::High` to enable pull-up resistor giving a default state of `true`
* `DigitalInput::Bias::Low` to enable the pull-down resistor giving a default state of `false`
* `DigitalInput::Bias::None` to leave the input floating giving an undefined default state

```cpp
DigitalInput::Bias get_bias()
```
*Gets the current bias setting.*  
See `set_bias` for the possible options.

``` cpp
bool get_state()
```
*Returns the state of the digital input.*

``` cpp
EdgeData get_edges(bool reset)
```
*Returns the current state of the input, plus the number of edge transitions that have been counted.*   
The data is packed into a struct with the following fields.
| Type | Name | Description |
| --- | --- | --- |
| `uint32_t` | `edge_count` | Number of edges counted
| `bool` | `current_state` | The current state of the input
`edge_count` includes both rising and falling edges.

The argument `reset` controls whether edge_counts are cleared after they are returned. Set to `true` to clear, or `false` to retain and accumulate.


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