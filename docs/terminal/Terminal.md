---
layout: page
title: Terminal
---

# Terminal

## Intro
* test functionality
* configuring the system
* image

## Getting Connected
* windows?
* linux?
* links?

## Terminal Syntax
The terminal supports a sub-set of the c++ syntax that you would normally use to write a complete program or sketch. At the terminal you can create handles to circuits and issue commands to circuits. 

* syntax variations.

![Terminal example with full syntax]({{"assets/images/terminal/terminal_example_full_syntax.png" | relative_url}})

The terminal will not evaluate arbitrary c or c++ expressions. Save the `if` statements, `for` loops, arithmetic, etc for your program, as they will not work at the terminal.


``` cpp
my_led.set_blink_rate(1+1) // 1+1 is an expression. The terminal only accepts literals.
```


### Terminal Short-cuts
Compiled code must always strictly follow the syntax of the c++ programming language. If not, the compiler will grumble at us and refuse to generate the stream of 1s & 0s that make up our application. However, there are a few short-cuts we can take when working at the terminal. If you can remember these it will save you some key-stokes.

#### Faster handles
When creating a handle, typing `::Handle` after the circuit type is optional. 

*Complete Syntax*
``` cpp
Led::Handle my_led(0);
```
*Equivalent Shortcut*
``` cpp
Led my_led(0);
```

#### Direct commands
The step of creating a handle can be skipped entirely. If you only want to issue one or two commands to a circuit, this can save some time. Just specify the circuit type, board number (optional) and circuit number in place of the handle when issuing the command.

*Complete*
``` cpp
Led::Handle my_led(0);
my_led.set(blink);  
```
*Shortcut*
``` cpp
Led(0).set(blink);
```

#### Functions without arguments
When calling a function without arguments, you can drop the empty parentheses at the end of the function name.

*Complete*
``` cppp
Led(0).created()
```
*Shortcut*
``` cppp
Led(0).created
```

#### Implicit system commands
A handle to a `System` circuit is created for you automatically. The handle is named (unsurprisingly) `system`. You can use this handle to issue system commands;

*Complete*
``` cppp
system.print_faults()
```
If you issue a command without specifying a handle, it is assumed by default that the command is a `System` command. So using the `system` handle becomes optional. in this particular example we can also omit the parethesis since the instruction does not take any arguments.

*Shortcut*
``` cppp
print_faults
```

## Terminal Use Cases
* Configuration
* Exploring
* Checking faults