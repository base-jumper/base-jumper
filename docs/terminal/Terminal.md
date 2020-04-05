---
layout: page
title: Terminal
nav_order: 3
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

![Terminal example with full syntax]({{"assets/images/terminal/terminal_example_full_syntax.png" | relative_url}})

The screenshot above demonstrates how to interact with a Led circuit through the terminal. The commands entered at the terminal similar to the equivalent c++ code, but there are a few differences which are outlined below.

####  No Semicolons
At the terminal we don't need to terminate each line with `;`. The terminal accepts a single command on each line. As soon as you hit enter, the command is executed.

#### Shortened arguments
The second thing to note from the above example is argument passed to `set_state()`. In c++ code we would have used `Led::State_Blink`. Here we used just `blink`. The `Led::` scoping is dropped completely, and the `State_Blink` has been shortened to just `blink`.

So if the arguments used at the terminal are different, how do you know what to use? The simplest way to find out is to use the `instuctions` command. When you call the `instructions` command on a circuit, it will print to the terminal all of the supported instructions. Along with each function it also shows the expected format of the arguments. For numeric arguments it will give an expected range.

![Discover a circuits instructions]({{"assets/images/terminal/terminal_instructions.png | relative_url}})

#### Limited functionality
The terminal will not evaluate arbitrary c or c++ expressions. Save the `if` statements, `for` loops and arithmetic for your program or sketch.


## Terminal Short-cuts
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