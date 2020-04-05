---
layout: page
title: Serial
parent: Circuits
---

# Serial #
Basejumper base boards currently come with support for RS485. RS485 is popular serial hardware layer used widely in industrial control. It allows signals to be transmitted over long distances using a differential pair. Typical applications might include interfacing with other controllers, motor drivers or sensors with built-in signal processing (eg. load cell).

Putting aside differences in the physical layer, the format of the data packets transmitted over serial is the same regardless of whether it's RS485, RS232 or some other serial protocol. There is a start bit, 5-8 data bits, a parity bit (for error detection) and 1-2 stop bits.

Sparkfun has a [pretty decent tutorial](https://learn.sparkfun.com/tutorials/serial-communication/all) on serial communication that's worth a read if it's new to you or you need a referesher.

### Isolation ###
Isolation is sometimes needed if the device you are communicating with has a different ground reference to the base board. This situation arises if the remote device is not powered from the same power supply as the base board, or if there are long cable lengths between devices. In these situations you should select a base board with galvanically isolated serial circuit. In addition to the data lines, isolated circuits will have an additional pin for a ground reference. Connect this to the ground of the remote device you are communicating with. Once this is connected up, the communications will work even if ground potential of the remote device is different to the base board.

Usually the requirement for isolation can be avoided by connecting together the grounds of all of the components in the system. Note that there is no harm in using an isolated serial circuit even if it's not strictly required. An isolated circuit will work just fine in all scenarios, provided you rememmber to wire up the ground reference.


### Direct Control ###
The platform you are using probably comes with its own serial port in the form of a UART. However, it might not have the transceiver you need (eg. RS485) or isolation. In this case, you have a couple of options. 

1. Use the UART and transceiver on the base board. The UART on the platform is unused. Data is transferred from the platform to the base board using calls to the basejumper API. The base board then send the data out using its serial port.
2. Use the UART on the platform with the transciver on the base board. Data is transferred directly from the platform without being touched by the base board. 

Option 1 is the default mode. This will work with any platform regardless of whether it has its own UART or not.

Option 2 is referred to as 'Direct Control', as the platform is given direct control over the transceiver. This is typically what you would do when using a simple shield/cape/wing/whatever with just a basic transceiver. There are good reasons why you might want to select this mode over option 1. For instance, if you are using a protocol such as modbus there are probably libraries available for your platform to implement this. Those libraries are going to direct all the data through your platform's built-in UART, so they will only work if direct control is enabled.  

Platforms that have a UART will already have the UART pins connected to the transceivers on the base board. This is done via traces on the jumper PCB. You don't need to connect up any wires yourself. Just enable direct control mode and you're good to go. If your platform has more than one UART, refer to the documentation for that specific platform to find out which one is used.

---

## API 
### start
``` cpp
void start(uint16_t buad, Serial::Config config)
```
*Starts the serial port.*  
`baud` specifies the baud rate. 9600 is a safe default. Other common baud rates are  1200, 2400, 4800, 19200, 38400, 57600, and 115200.  
`config` specifies the number of data bits (5-8), parity bit (N for none, E for even, O for odd) and the number of stop bits (1-2). `Serial::Config_8N1` would give 8 data, no parity and 1 stop bit.
``` cpp
/* Example */
my_serial.start(9600, Serial::Config_8N1); // 9600 bits/s, 8data bits, no parity bit, 1 stop bit
```

### available
``` cpp
uint16_t available()
```
*Returns the number of bytes that are currently available to read in the receive buffer.*

### read single byte
``` cpp
uint8_t read();
```
*Reads a single byte from the recieve buffer. If buffer is empty, zero is returned. You should always check that the buffer is not empty prior to reading using the `available` function*

### write single byte
``` cpp 
void write(uint8_t data)
```
*Writes a single byte of data to the serial port*  
`data` is the byte to send.

### read multiple bytes
``` cpp
void read(OUT uint8_t* p_dest, size_t len)
```
*Reads the specified number of bytes from the recieve buffer. You should always check that the buffer has at least the requested number of bytes using `available` prior to calling `read`.*  
`p_dest` is a pointer to the location where you want the data to be written.  
`len` specifies the number of bytes to read.
``` cpp
/* Example */
constexpr uint16_t max_len = 10;
uint8_t data[max_len];
uint16_t available = my_serial.available();
uint16_t read_len;
if (available < max_len)
    read_len = available; // do not read more than what is available
else
    read_len = max_len;
my_serial.read(data, read_len);
// 'read_len' bytes of data are now available in 'data' for processing
```

### write multiple bytes
``` cpp
void write(uint8_t* p_source, size_t len)
```
*Writes mutliple bytes of data to the serial port*  
`p_source` is the location of the data to write.  
`len` is the number of bytes


### clear
``` cpp
void clear()
```
*Discards all data that is currently in the receive buffer. `available()` is reset to zero.*

## Example
