---
layout: page
title: Navagio
parent: Boards
---

# Navagio

## Specifications
This section provides specifications for the circuits available on the base board. These specifications are for the default population, and may not apply if any customizations have been made. For general information on how to use a particular circuit, refer to the *circuit docs* linked beneath the table.

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

*See also* [[circuit docs]](({{"docs/circuits/CanBus.html#hardware-customization" | relative_url}}) [[bom]](#bom) [[assembly drawing]](#assembly-drawing)

---

## Pin Outs

**Connector A**  

| Pin # | Function 1 | Function 2 |
| --- | --- | --- |
| x | AnalogInput 0 | AnalogOutput 0 |
| y | AnalogInput 1 | AnalogOutput 1 |
| z | Supply 0 | - |

## PCB Assembly Drawing


## BOM