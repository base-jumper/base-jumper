---
layout: page
title: Gps
parent: Circuits
---

# GPS
GPS receivers provide access to location (latitude, longitude and altitude), velocity and timing information. 

The use of the term "GPS" is a slight misnomer. BaseJumper base boards use a Ublox neo-m8n receiver which can use up to 3 different satellite networks concurrently to resolve position. One of those satellite networks is GPS. The others are Galileo, GLONASS and BeiDou. However, we use the term *GPS receiver* because it is more widely understood than the more technically correct term *GNSS receiver*.

### Location & Speed
When requesting position, the API provides options for getting just a 2D position with latitude and longitude or a 3D position with additional altitude information. Speed is available in north-east-down vectors, or as a ground speed. 

Whenever location and speed infomation is requested, additional info is also included in the result to indicate the validity and estimated accuracy of the provided measurements. GPS data will only be reliable when the receiever has had a chance to establish a fix. This can take several minutes after booting up cold. The fix can also be lost if the antenna is obstructed (for example, driving through a tunnel) and will take some time to re-establish a fix when the obstruction is removed. Therefore it is important to check the validity flags to before using the provided measurements, so you can be sure you have reliable data.

### Time
The API reports time in two different formats. Both formats include date as well as time. This first format is [UTC time](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), broken down into year, month, day, hour, minute and seconds. This is returned as a [`struct tm`](http://www.cplusplus.com/reference/ctime/tm/) type from the standard c library. It can be used directly with the library functions available in [`time.h`](http://www.cplusplus.com/reference/ctime/). When printed to the terminal, [ISO 18601](https://en.wikipedia.org/wiki/ISO_8601) format is used to display the time.

The alternative format is [`unix time`](https://en.wikipedia.org/wiki/Unix_time). This is essentially the number of seconds that have elasped since 00:00:00 UTC on 1 January 1970. This may be useful for setting the system time on a linux system. It is also a more compact format than the broken-down UTC time, so if your application needs to request time regularly it is more efficient to use this one.

Time measurement results are returned with an additional flag to indicate their validity. Be sure to check that the time is valid before using it. 

### Atomic Updates
Many of the functions in the GPS API return a `struct` containing several related measurements. It is guaranteed that all information within a returned `struct` came from a single GPS update at a particular instant in time. For example, when requesting latitude and longitude, you can be sure that you will not receive longitude from the latest GPS update, and latitude from the previous update that is now 1 second old. Both will be from the same update, taken at the same point in time.

---

## Hardware

### Back-up battery


---

## API

### Status

``` cpp
Gps::Fix get_fix()
```
*Returns current fix status*  
`Gps::Fix_NoFix`, `Gps::Fix_DeadReckoning`, `Gps::Fix_2D`, `Gps::Fix_3D`, `Gps::Fix_GnssPlusDeadReckoning` or `Gps::Fix_TimeOnly`.  
If everything is working OK you should get `Fix_3D`, but it can take a few minutes after power up to reach this state.

### Position

``` cpp
Gps::LatLonData get_lat_lon();
```
*Returns latitude, longitude and some information about the validity and accuracy of the measurements.*   
The data is packaged into a struct containing the following fields;  
`bool valid`: `true` if the GPS has received sufficient information to provide reliable and accurate latitude and longitude measurements. If `false`, the data in the remaining fields should not be trusted.
`double lat`: latitude in fractional degrees.  
`double lon`: longitude in fractional degrees.  
`uint32_t accuracy`: estimate of horizontal accuracy in mm.  

``` cpp
Gps::LatLonAltData get_lat_lon_alt();
```
*Returns latitude, longitude, altitude and some information about the validity and accuracy of the measurements.*   
The data is packaged into a struct containing the following fields;  
`bool valid`: `true` if the GPS has received sufficient information to provide reliable and accurate latitude, longitude and altitude measurements. If `false`, the data in remaining fields should not be trusted.
`double lat`: latitude in fractional degrees.  
`double lon`: longitude in fractional degrees.  
`uint32_t accuracy`: estimate of horizontal accuracy in mm.  
`int32_t alt`: altitude above ellipsoid in mm.  
`int32_t alt_msl`: altitude above mean sea level in mm.  
`uint32_t alt_accuracy`: estimate of vertical accuracy in mm.

### Time

``` cpp
Gps::UtcTimeData get_utc_time()
```
*Returns broken-down UTC time, plus a validity flag.*
The data is packaged into a struct with the following fields;  
`bool valid`: `true` if the GPS has received sufficient information to provide reliable and accurate time measurements. If `false`, the data in remaining fields should not be trusted.
`struct tm tm`: UTC time formated in a structure from the standard c library. See [here](http://www.cplusplus.com/reference/ctime/tm/) for further details.

``` cpp
Gps::UnixTimeData get_unix_time()
```
*Returns unix time, plus a validity flag.*  
The data is packaged into a struct with the following fields;  
`bool valid`: `true` if the GPS has received sufficient information to provide reliable and accurate time measurements. If `false`, the data in remaining fields should not be trusted.  
`uint32_t time`: seconds elapsed since 00:00:00 UTC on 1 January 1970.   

### Velocity

``` cpp
Gps::VelNedData get_vel_ned()
```
*Returns North-East-Down velocity vectors, plus information on measurement validity and accuracy.*  
The data is packaged into a struct with the following fields;  
`bool valid`: `true` if the GPS has received sufficient information to provide reliable and accurate speed measurements. If `false`, the data in remaining fields should not be trusted.  
`int32_t vel_n`: north velocity in mm/s
`int32_t vel_e`: east velocity in mm/s
`int32_t vel_d`: down velocity in mm/s
`uint32_t accuracy`: estimate of speed accuracy in mm/s

``` cpp
Gps::VelNedData get_ground_speed()
```
*Returns ground speed, plus information on measurement validity and accuracy.*  
The data is packaged into a struct with the following fields;  
`bool valid`: `true` if the GPS has received sufficient information to provide reliable and accurate speed measurements. If `false`, the data in remaining fields should not be trusted.
`int32_t speed`: ground speed in mm/s
`uint32_t accuracy`: estimate of speed accuracy in mm/s