---
layout: page
title: Navagio
parent: Boards
---

# Navagio

## Specifications
This section provides specifications for the circuits available on the base board. These specifications are for the default population, and may not apply if any customizations have been made. For general information on how to use a particular circuit, refer to the *circuit docs* linked beneath the table.

### Connector Specs


### Analog Input Specs

| Instance | 0 | 1 |
| :--- | --- | --- |
| Input Voltage Range* | 0-10V | 0-10V |
| ADC Resolution | 12-bit | 12-bit |

* For higher input voltages, configure resistor-divider network

### CAN Bus Specs

| Instance | 0 | 1 |
| :--- | --- | --- |
| **Isolation** | yes | no |
| **Termination** | population option | population option |
| **CANFD support** | yes | yes |

*See also*  [[circuit docs]]({{"docs/circuits/CanBus.html" | relative_url}}) [[configurations]](#can-bus-config)

---

## Configurations
This sections provides details of component designators and the default population. This information, together with the PCB assembly drawing, can be used to make customizations to the base board hardware to suit a given application.

### CAN Bus Config

| Instance | 0 | 1 |
| :--- | --- | --- |
| Rterm1 | Rx | Ry |
| Rterm2 | Ra | Rb |

*See also* [[circuit docs]]({{"docs/circuits/CanBus.html#hardware-customization" | relative_url}}) [[bom]](#bom) [[assembly drawing]](#assembly-drawing)

---

## Pin Outs

**Connector X**  

| Pin # | Function | Pin # | Function |
| --- | --- | --- | --- |
| 1 | Ground | 2 | LoadDriver(0) |
| 3 | Ground | 4 | LoadDriver(1) |
| 5 | Ground | 6 | LoadDriver(2) |
| 7 | Ground | 8 | LoadDriver(3) |
| 9 | Ground | 10 | Ground |
| 11 | Power | 12 | Power |
| 13 | Expansion 3* | 14 | Expansion 0* |
| 15 | Expansion 2*| 16 | Expansion 1* |  

\* Pin used by expansion board (if fitted)

**Connector Y**

| Pin # | Function | Pin # | Function |
| --- | --- | --- | --- |
| 1 | SerialPort(0) RS485B | 2 | SerialPort(0) RS485A |
| 3 | Ground | 4 | Ground |
| 5 | CanBus(0) CANL | 6 | CanBus(0) CANH |
| 7 | Ground | 8 | Ground |
| 9 | CanBus(1) CANL | 10 | CanBus(1) CANH |
| 11 | Supply(1) 5V | 12 | Ground |
| 13 | Ground | 14 | AnalogInput(0) or AnalogOutput(0) |
| 15 | Supply(1) 5V | 16 | Ground |
| 17 | Ground | 18 | AnalogInput(1) or AnalogOutput(1) |
| 19 | Supply(1) 5V | 20 | Ground |
| 21 | Ground | 22 | AnalogInput(2) or AnalogOutput(2) |
| 23 | Supply(1) 5V | 24 | Ground |
| 25 | Ground | 26 | AnalogInput(3) or AnalogOutput(3) |
| 27 | Ground | 28 | Ignition |

**Connector Z**
| Pin # | Function | Pin # | Function |
| --- | --- | --- | --- |
| 1 | Ground | 2 | DigitalOutput(0) |
| 3 | Supply(0) 15V | 4 | Supply(1) 5V |
| 5 | Ground | 6 | DigitalOutput(2) |
| 7 | Supply(0) 15V | 8 | Supply(1) 5V |
| 9 | Ground | 10 | DigitalOutput(3) |
| 11 | Supply(0) 15V | 12 | Supply(1) 5V |
| 13 | Ground | 14 | DigitalOutput(1) |
| 15 | Supply(0) 15V | 16 | Supply(1) 5V |
| 17 | Ground | 18 | DigitalInput(0) |
| 19 | Supply(0) 15V | 20 | Supply(1) 5V |
| 21 | Ground | 22 | DigitalInput(1) |
| 23 | Supply(0) 15V | 24 | Supply(1) 5V |
| 25 | Ground | 26 | DigitalInput(2) |
| 27 | Ground | 28 | DigitalInput(3) |

## PCB Assembly Drawing


## BOM