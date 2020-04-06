---
layout: page
title: System
parent: Circuits
---

# System #
The `System` circuit allows you to configure and manage your baseboard. It's not really a circuit as such, as it doesn't control any physical components on the PCB. However, we treat `System` like a type of meta-circuit in that you interface with it in the same way as a circuit. `System` contains a collection of useful instructions for things like setting the board id number, managing faults, reporting the circuits supported by the board, etc.

`System` commands can be issued from code, or from the serial terminal. `System` commands are especially useful when working at the terminal. Some `System` commands (like `circuits`) are only there to print information to the terminal. Although they can be called from code it wouldn't make much sense to do so. Other commands (like `set_board_id`) are equally useful both in code or at the terminal.

At the serial terminal, `System` commands get special treatment. If the circuit is not specified when issuing a command, it is automatically assumed that it is a system command. For example, instead of `System(0).circuits` you just type `circuits`. Read more about the Serial Terminal [here](Serial.html).

## Fault Reporting
The BaseJumper source code contains a number of `assert` statements, which check that the code is operating as expected. A typical example of where an assertion would be used is to ensure that a receive buffer never overflows. It is very rare for an assertion to fail, and it would only happen in highly unusual opeating conditions. A fault reporting system is in place to ensure that in the unlikely event of an assertion failing, that it does not go unnoticed. The source of the assertion that failed is recorded in non-volatile memory. Recorded details include the software module ('class id'), instance (instance id) and the particular assertion within the module ('assertion id'). The status of faults can be checked using the `faults` command at the terminal. `faults` will report if an assertion failed the last time it was checked ('failed now'), or if it has ever failed since the faults were last cleared ('failed ever'). Faults can be cleared with the command `clear_faults`. It is often the case that software issues can be very quickly and easily remedied once the source of the issue is known. Incorrect use is the most common cause of an assertion failing.

![faults]({{"assets/images/terminal/terminal_faults.png" | relative_url}})

---

## API

### circuits
``` cpp
void circuits()
```
*Prints information about supported circuits (circuit types, API version and number of instanes) to the serial terminal*  

### fault reporting
``` cpp
void faults()
```
*Prints fault information to the serial terminal.*

``` cpp
void clear_faults()
```
*Clears the `failed_ever` status of all faults*