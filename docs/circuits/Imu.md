---
layout: page
title: Imu
parent: Circuits
---

# Imu #
The inertial measurement unit (IMU) contains an accerometer and gyroscope. Both are 3-axis MEMS sensors. Accelerometers are useful for measuring tilt, orientation (via gravity vector) or tracking movement (dead reackoning). The gyroscope provides angular rate measurements. Often measurements from an IMU are combined with measurements from a GPS to provide improved location accuracy.

---

## API

### Measurements
``` cpp
Accel get_accel()
```
*Returns the latest x, y & z acceleration measurements.*  

The data is packaged into a struct with the following fields;  

| Type | Name | Description |
| --- | --- | --- |
| `float` | `x` | acceleration in x-axis in m/s/s |
| `float` | `y` | acceleration in y-axis in m/s/s |
| `float` | `z` | acceleration in z-axis in m/s/s |

``` cpp
AngularRate get_angular_rate()
```
*Returns the latest x, y & z angular rate measurements.*  

The data is packaged into a struct with the following fields;  

| Type | Name | Description |
| --- | --- | --- |
| `float` | `x` | angular rate in x-axis in deg/s |
| `float` | `y` | angular rate in y-axis in deg/s |
| `float` | `z` | angular rate in z-axis in deg/s |

### Self-test
```cpp
bool self_test_accel()
```
*Runs self-test on accelerometer.*  
Returns true if the test passes, false if it fails. It is important to keep the board stationary during the test.  
> This is a blocking function. It may take up to 100ms to complete. It is not intended to be run as part of normal application execution. Use only for diagnostics purposes.

```cpp
bool self_test_angular_rate()
```
*Runs self-test on gyroscope.*  
Returns true if the test passes, false if it fails. It is important to keep the board stationary during the test.  
> This is a blocking function. It may take up to 100ms to complete. It is not intended to be run as part of normal application execution. Use only for diagnostics purposes.