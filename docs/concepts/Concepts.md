---
layout: page
title: Concepts
nav_order: 2
---

# Concepts
Before going any further, let's discuss some of the key concepts around BaseJumper base boards and decide on some terminology that can be used when discussing the associated components.


## Platform
*Arduino, Raspberry Pi, Beagle Bone, Raspberry Pi compute module, Jetson.* Hopefully you've already heard of one or more of these and have some idea of what they are. But what would you call them, collectively? Single board computers? No, not all of them. System on module? Sure, some are. But there's not really a name precisely captures them all. So, for better or worse, we've decided to call them **Platforms**. 


## Base Board
*Shield, hat, cape, wing.* Familiar with any of these terms? They are all "add-on" boards of sorts, that plug in to a platform to extend its functionality. They provide additional circuits, such as sensor & actuator interfaces, transceivers, onboard MEMs sensors, etc. A BaseJumper **base board** is similiar to one of these add-on boards, but with a couple of important differences. 

The first difference between base boards and other shields/hats/capes that you may be familiar with is that base boards aren't stackable. You can't physically plug multiple base boards into the same platform. Now, that doesn't mean that you can't use multiple base boards together. You absolutely CAN (pardon the pun). To link multiple base boards together we use [CAN bus](https://en.wikipedia.org/wiki/CAN_bus). This allows over 250 base boards to be connected to a single platform. The base boards don't all have to be in the same location either. They can be distributed, so that they are situated close to the sensors and actuators that they interface to. 
* Base boards aim to provide all the needed functionality in a single board.
* Enclosure 

The second difference between a BaseJumper base board and shields/hats/capes is that base boards are **cross-platform**. A hat designed for a Raspberry Pi cannot be used with an Arduino. There are a couple of reasons why. First, the Arduino and the Raspberry Pi have different physical interfaces (headers are in different locations, with a different number of pins). Second, there is no guarantee that the Arduino will have the required perihperals to operate the shield. For instance the shield might need more PWM outputs than the Arduino can provide, or a I2S bus that the Arduino doesn't have. Then there is the issue of porting the libaries that run under linux on the Pi to work on Arduino. BaseJumper base boards, on the other hand, are designed to be compatible with a range of different platforms. A jumper board is used to adapt between the physical interface of a particular platform to a common physical interface that is used across all base boads (more on this shortly). Communication between the platform and the base board requires only a SPI bus and a couple of GPIO pins - resources are universal across all platforms.

If you've used shields, capes or hats a lot in the past, then you might be used to stacking multiple boards together to get the functionality you require. Typical add-on boards are quite specialized and only do one thing. For example, they might just add some extra analog inputs, or drive a motor, or add a CAN interface. But not all of these things at once. BaseJumper base boards are quite different in this regard. BaseJumper boards aim to cram *everything you need* into a single board. They offer a mix of circuits and interfaces, so you can build up a complete system from one board. They also take care of providing a clean, regulated power supply to power your platform. In short, BaseJumper base boards provide a lot more functionality than your typical shield.


## Jumper Board
All BaseJumper base boards have the same standard physical interface. The interface is made up of a footprint with 63 surface mount pads arranged in two rows. So how does, say, a Raspberry Pi with its dual row 40-pin header connect to the surface mount pads on the base board? The answer is **jumper boards**. Jumper boards are PCBs that adapt between the 63-pin footprint on the base board, and the physical interface of the platform (whether it be dual row headers, castellated edges, a SODIMM module or something else). Jumper boards have castellated edges that are soldered to the pads on the base-board, providing a compact and low-profile interconnect. In the case of the Raspberry Pi, the jumper board has a dual-row 40 pin socket to mate with the header on the Pi, plus some mounting posts for securing the Pi in place. There is a separate jumper board for every supported Platform, with the appropriate sockets/connectors/interconnects for the specific platform. 

Jumper boards may also have electronic components mounted to them. For example, the Raspberry Pi doesn't have its own CAN controller, so we provide the option of mounting a CAN controller IC to the jumper board. Another example: Arduino Uno is still rocking old-school 5V GPIO pins, so there are level-shifter ICs on its jumper board to translate between 5V and 3.3V.


## Full Footprint and Half Footprint
We just talked about the standard 63-pin footprint which is used to connect base boards to jumper boards. This is called the **full footprint** and it's physical size is about __ x __. There is also another variant called the **half footprint**. It has 31 pins, and is (you guessed it) roughly half the size. Half-footprint jumper boards are available for platforms that have a small physical form-factor, such as the [Arduino MKR series](https://store.arduino.cc/usa/arduino/arduino-mkr-family). Half-footprint jumper boards can be used with any base board, regardless of whether it has a full or half footprint interface. When using a half footprint jumper on a base board with a full footprint, the jumper is soldered to the lower half of the footprint (the first 31 pins) and the remaining pins (32-63) on the base board are left unconnected. Half footprint jumpers can be used with half footprint base boards to provide a very compact solution for smaller systems that do not require a lot of IO.