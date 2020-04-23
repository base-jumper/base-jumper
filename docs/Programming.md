---
layout: page
title: Programming
nav_order: 4
has_children: false
---

# C++ Programming

## Intro
If your application is running on a Platform, the first step is to download the BaseJumper library for that platform. To find out how to get the library, refer to the documentation page for the [platform]({{"docs/platforms/Platforms.html"| relative_url}}). If the application is running directly on the base board, then please see page [Platforms/BaseBoard]({{"docs/Platforms/BaseBoard.html" | relative_url}}) for info on how to get setup. Once you're set up with the appropriate library, you will have access to a c++ API for controlling the base board's circuits. This page covers the general concepts of how to use the API. Once you understand this, you can start looking at the [Circuits]({{"docs/circuits/Circuits.html" | relative_url}}) pages to see the functions available for each circuit.

## Example

Below is a complete example of a simple program that uses `DigitalInput` and `Led` circuits. This example is for an Arduino, but other platforms will be similar. Take a quick look over it, and then we will dissect it further to understand what each line is doing.

``` cpp
/* Button LED example 
 * 
 * To run this example you will need to wire a switch to Digital Input 1 on your baseboard.
 * Connect one terminal of the switch to the digital input, and the other to 5V. The baseboard 
 * will provide a 5V reference that you can use. Refer to the documentation (link below) for 
 * the base-board pin-outs. When the switch is closed, the LED should turn on. The LED is located 
 * on the baseboard itself. If the baseboard is inside an enclosure, you might need to remove it 
 * from the enclosure to see the LED.
 */  

#include <BaseJumper.h>

using namespace BaseJumper;

// create circut handles
DigitalInput::Handle button(0);
Led::Handle led(0);

void setup() 
{  
  basejumper_init();

  led.create();
  button.create();
}

void loop() 
{
    /* Turn led on when button is pressed and off when button is released */
    if (button.get_state())
    {
      led.set_state(Led::State_On);
    }
    else
    {
      led.set_state(Led::State_Off);
    }
    delay(50);
}
```

## Bringing the API into Scope

Notice the `#include` directive at the top?
``` cpp
#include <BaseJumper.h>
```
This brings the BaseJumper API into scope. `BaseJumper.h` is the only header you need to include to get access to all the Circuit handle classes.

All of the BaseJumper functions and classes live inside a namespace. The namespace is called `BaseJumper`, and you can bring everything in the namespace into scope with a `using` statement;
``` cpp
using namespace BaseJumper;
```
Alternatively, you can choose to use fully qualified names for all library members, such as `BaseJumper::Led::Handle` instead of just `Led::Handle`. This is sometimes necessary (albeit rare) if there are name collisions between library code and other code in your application. If you're unsure, just start with the `using` statement as above.

## Initialisation

Your application will no doubt have some code that is called once at start-up to initialize resources. In Arduino this code lives inside a function called `setup`. In a less structured applications the code might be at the beginning of a `main` function. You will need to find where the application initialisation code is and insert this line;
``` cpp
basejumper_init();
```
This will ensure that the BaseJumper library has an opportunity to configure the resources it needs (eg. SPI bus and GPIO pins) before you start trying to issue commands to circuits.

## Circuit Handle Construction

To control a circuit, the first step is to create a handle to the circuit. The example program above creates two circuit handles.
``` cpp
// create circut handles
DigitalInput::Handle button(0);
Led::Handle led(0);
```
The first circuit handle is for `DigitalInput` circuit and is called `button`. The second is for a `Led` circuit and is called `led`.
The first thing to note is that all circuit handle types end in `::Handle`. Say you needed to control an `AnalogInput`, can you guess the name of the handle class? Yep, it would be `AnalogInput::Handle`.  

Next, look at the constructor argument. In this example both constructors are passed a single argument of `0`. This identifies the *instance* of the circuit you want to control. Circuit numbering starts at zero, so here we are saying we want to use the first instance of the `DigitalInput` and `Led` circuits. It is not uncommon for base boards to have multiple instances of a particular circuit. If the base board has four `DigitalInputs` and we want to control the last one, then the handle constructor argument would be `3`. 

## Circuit Handle to a Remote Base Board

Suppose the circuit we wish to control is not on the local base board, but is on another base board that is linked by CAN. In this case we will need two constructor arguments to create the Circuit handle. The first argument specifies the *board ID* of the remote base board and the second argument specifies the circuit *instance*. Below is an example.
``` cpp
DigitalInput::Handle proximity_sensor(5, 1); // handle for digital input instance 1 on board with ID 5
```
If the board ID is not specified it defaults to the local board.

## Initialising the Circuit
Take another look at the `setup` function and you'll notice it has the two lines below.
``` cpp
led.create();
button.create();
```
A function called `create` is being invoked on each of the circuit handles. `create` is a special function that all circuit handles have. When you call `create`, the base board allocates the resources it needs to operate that circuit and initializes it so it is ready for use. You should always call `create` before using a circuit, otherwise an exception will be generated.

There may be circuits on the base board that share resources. For example, an `AnalogInput` and an `AnalogOutput` circuit might both share the same connector pin. In this case, you can create either circuit, but not both. An exception will be generated if you try to create two circuits with overlapping resources.

There are a few more *general commands* similar to `create` that are available on all circuit handles. Here's the complete list.

``` cpp
bool created()
``` 
Returns true if the circuit has been successfully created.

``` cpp
void destroy()
```
The opposite of `create`. Releases the resources that are used by a circuit. This should only be called on circuits that have been created, but are no longer required.

``` cpp
void instructions() 
```
Prints a list of all available instructions for that circuit type. This is most often used at the [terminal]({{"docs/terminal/Terminal.html"| relative_url}}).

``` cpp
uint16_t get_baseboard_api_version()
```
Returns the API version of the circuit firmware running on the base board. This needs to match `get_library_api_version()` for everything to work correctly.

``` cpp
uint16_t get_library_api_version()
```
Returns the API version of the library being used to interface with the base board. This needs to match `get_baseboard_api_version` for everything work correctly.

## Issuing Commands to a Circuit

Circuit are controlled by calling methods on their handles. In the example code from above, there are a three lines in the `loop` function where commands are being issued to circuits. These lines are numbered below.

``` cpp
void loop() 
{
    /* Turn led on when button is pressed and off when button is released */
    if (button.get_state())          // circuit instruction #1
    {
      led.set_state(Led::State_On);  // circuit instruction #2
    }
    else
    {
      led.set_state(Led::State_Off); // circuit instruction #3
    }
    delay(50);
}
```
The first is `button.get_state()`. This issues a command to the `DigitalInput` circuit instance that is identified by the handle `button`. The command is `get_state` which returns the current state of the input as a boolean value.

`led.set_state(Led::State_On)` is an instruction for a `Led` circuit which accepts the desired state (i.e. on, off or blinking) as an argument. When custom types such as this state enumeration are used, they are always defined inside the circuit namespace, so the argument will always have something like `Led::` at the beginning (same as the handle class!).

In this example we have only encountered two circuit types and a few instructions. There are many more, and you can read about them in the [Circuits]({{"docs/circuits/Circuits.html" | relative_url}}) section.

## Timing
At the bottom of the `loop` function there is a line we still haven't discussed.
``` cpp
delay(50);
```
This line inserts a delay (in this case 50ms) between executions of the loop. If your control loop is very simple, like this example, then we recommend that you put in a delay. If not, the loop is going to execute very quickly and the base board will be flooded with commands that it needs to execute. When the base board receives a circuit command, it makes it its top priority to act on that command and return a response ASAP. To achieve this, other less-urgent tasks such as reporting, servicing the terminal, etc, are put aside while the task is executed. If you keep throwing circuit instructions at the base board as fast as it can handle it, then the less-urgent tasks that are being put aside will start to pile-up. At this point the base board is forced to reduce the priority at which circuit commands are executed so that other tasks have a chance to complete. When this happens, you may notice an increase in the time it takes the base-board to respond to commands. The base board will effectively 'throttle' the speed at which your control loop runs for you, because the method calls on the circuit handles won't return until the base board has had time to do its own housekeeping. It's best to avoid this situation by inserting delays as needed at the end of the control loop, or between large groups of circuit commands that run in quick succession. 

The `delay` function used in this example is part of the standard `Arduino` library. You will need to find the equivalent function for the platform you are using.

For a more complex control system running computationally-expensive algorithms, the time taken to perform computations may slow down the control loop enough that explicit delays are not required. 