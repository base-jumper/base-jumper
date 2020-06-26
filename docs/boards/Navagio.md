---
layout: page
title: Navagio
parent: Boards
---

# Navagio

## Specifcations
This section provides specifications for the circuits available on the base board. These specications are for the default population, and may not apply if any customizations have been made. For general infomation on how to use a particular circuit, refer to the *circuit docs* linked beneath the table.

### CAN Bus Specs

| Instance | 0 | 1 |
| :--- | --- | --- |
| **Isolation** | yes | no |
| **Termination** | population option | population option |
| **CANFD support** | yes | yes |

*See also*  [[circuit docs]]({{"docs/Circuits/CanBus.html" | relative_url}}) [[configurations]](#CAN-Bus-Config)

---

## Configurations
This sections provides details of component designators and the default population. This information, together with the PCB assembly drawing, can be used to make customization to the base board hardware to suit a given application.

### CAN Bus Config

| Instance | 0 | 1 |
| :--- | --- | --- |
| Rterm1 | Rx | Ry |
| Rterm2 | Ra | Rb |

*See also* [[circuit docs]](({{"docs/Circuits/CanBus.html#Hardware-Customization" | relative_url}}) [[bom]]() [[assembly drawing]]()

---

## PCB Assembly Drawing


## BOM