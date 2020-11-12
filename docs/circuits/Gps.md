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

Whenever location and speed infomation is requested, additional info is also included in the result to indicate the accuracy of the provided measurements. 

### Time
The API reports time in two different formats. Both formats include date as well as time. This first format is [UTC time](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), broken down into year, month, day, hour, minute and seconds. This is returned as a [`struct tm`](http://www.cplusplus.com/reference/ctime/tm/) type from the standard c library. It can be used directly with the library functions available in [`time.h`](http://www.cplusplus.com/reference/ctime/). When printed to the terminal, [ISO 18601](https://en.wikipedia.org/wiki/ISO_8601) format is used to display the time.

The alternative format is [`unix time`](https://en.wikipedia.org/wiki/Unix_time). This is essentially the number of seconds that have elasped since 00:00:00 UTC on 1 January 1970. This may be useful for setting the system time on a linux system. It is also a more compact format than the broken-down UTC time, so if your application needs to request time regularly it is more efficient to use this one.


### Availability
GPS data will only be reliable when the receiever has had a chance to establish a fix. This can take several minutes after booting up cold. The fix can also be lost if the antenna is obstructed (for example, driving through a tunnel) and will take some time to re-establish a fix when the obstruction is removed. This is handled in the API through the use of the `Maybe` type. Functions returning position, speed and time information use `Maybe` as their return type to indicate that they might return the requested data or they might return `nothing` (if there is no fix, or information hasn't yet been obtained). The following example shows how to deal with with functions returning `Maybe`.

``` c++
Maybe<Gps::LatLonData> maybe_lat_lon = gps.get_lat_lon();
if (maybe_lat_lon)
{
    Gps::LatLonData lat_lon = maybe_lat_lon.get();
    // lat_lon is available and ready to use.
}
else
{
    // The data was unavailable - wait until the GPS 
    // has had time to establish a fix and try again.
}
```

### Atomic Updates
Many of the functions in the GPS API return a `struct` containing several related measurements. It is guaranteed that all information within a returned `struct` came from a single GPS update at a particular instant in time. For example, when requesting latitude and longitude, you can be sure that you will not receive longitude from the latest GPS update, and latitude from the previous update (that could be a second old). Both latitude and longitude will be from the same update, taken at the same point in time.

---

## Hardware

### Back-up battery
Base boards fitted with a GPS receiver have a coin cell holder that takes a CR2032 cell. This is the GPS backup battery. It allows the GPS to get a fix faster when powering on the board. It also allows the GPS to keep track of time even when the base board is powered down, so that time information is immediately available on powering up. Base boards are shipped without the the backup battery fitted. For optimal performance, it is recommended to install a battery. The battery will last about 1.8 years before it needs to be replaced. 

---

## API

### Status

``` cpp
Gps::Fix get_fix()
```
*Returns current fix status*  
`Gps::Fix::NoFix`, `Gps::Fix::DeadReckoning`, `Gps::Fix::Fix2D`, `Gps::Fix::Fix3D`, `Gps::Fix::GnssPlusDeadReckoning` or `Gps::Fix::TimeOnly`.  
If everything is working OK you should get `Fix3D`, but it can take a few minutes after power up to reach this state.

### Position

``` cpp
Maybe<Gps::LatLonData> get_lat_lon();
```
*Returns latitude, longitude and accuracy (if available).*   
The data will only be available if the GPS has received sufficient information to provide reliable and accurate latitude and longitude measurements.
When available, th data it is packaged into a struct with the following fields;  

| Type | Name | Description |
| --- | --- | --- |
| `double` | `lat` | latitude in fractional degrees.  
| `double` | `lon` | longitude in fractional degrees.  
| `uint32_t` | `accuracy` | estimate of horizontal accuracy in mm.  

If the data is not available `Maybe::nothing` is returned.

``` cpp
Maybe<Gps::LatLonAltData> get_lat_lon_alt();
```
*Returns latitude, longitude, altitude and accuracy of the measurements (if available).*   
The data will only be available if the GPS has received sufficient information to provide reliable and accurate latitude and longitude measurements.
When available, th data it is packaged into a struct with the following fields;  

| Type | Name | Description |
| --- | --- | --- |
| `double` | `lat` | latitude in fractional degrees. |
| `double` | `lon` | longitude in fractional degrees. |
| `uint32_t` | `accuracy` | estimate of horizontal accuracy in mm. |
| `int32_t` | `alt` | altitude above ellipsoid in mm. |
| `int32_t` | `alt_msl` | altitude above mean sea level in mm. |
| `uint32_t` | `alt_accuracy` | estimate of vertical accuracy in mm. |

If the data is not available `Maybe::nothing` is returned.

### Time

``` cpp
Maybe<Gps::UtcTimeData> get_utc_time()
```
*Returns broken-down UTC time (if available).*
The data will only be available if the GPS has received sufficient information to provide reliable and accurate time measurements.

When available, the data it is packaged into a `UtcTimeData` struct. `UtcTimeData` is just an alias for `struct tm` from the standard c library.  See [here](http://www.cplusplus.com/reference/ctime/tm/) for further details.

If the data is not available `Maybe::nothing` is returned.

``` cpp
Maybe<uint32_t> get_unix_time()
```
*Returns unix time, plus a validity flag.*  
Unix time is number of seconds that have elapsed since 00:00:00 UTC on 1 January 1970.

The data will only be available if the GPS has received sufficient information to provide reliable and accurate time measurements.

If the data is not available `Maybe::nothing` is returned.

### Velocity

``` cpp
Maybe<Gps::VelNedData> get_vel_ned()
```
*Returns North-East-Down velocity vectors, plus information on measurement validity and accuracy.*  

The data will only be available if the GPS has received sufficient information to provide reliable velocity measurements. When available, the data is packaged into a struct with the following fields;  

| Type | Name | Description |
| --- | --- | --- |
| `int32_t` | `vel_n` | north velocity in mm/s |
| `int32_t` | `vel_e` | east velocity in mm/s |
| `int32_t` | `vel_d` | down velocity in mm/s |
| `uint32_t` | `accuracy` | estimate of speed accuracy in mm/s |

If the data is not available `Maybe::nothing` is returned.

``` cpp
Maybe<Gps::VelNedData> get_ground_speed()
```
*Returns ground speed, plus information on measurement validity and accuracy.*  

The data will only be available if the GPS has received sufficient information to provide reliable speed measurements. When available, the data is packaged into a struct with the following fields;  

| Type | Name | Description |
| --- | --- | --- |
| `int32_t` | `speed` | ground speed in mm/s |
| `uint32_t` | `accuracy` | estimate of speed accuracy in mm/s |

If the data is not available `Maybe::nothing` is returned.