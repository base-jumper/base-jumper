---
layout: page
title: Advanced Concepts
nav_order: 3
---

# Advanced Concepts
Want to delve in a little deeper? It's ok if you don't. You can skip past this page and you will still learning everything you need to know to build a rock-solid system using a BaseJumper base board. But if you want to understand a little about what's happening under the hood; read on.

![red pill or blue pill?]({{"assets/images/red_pill_blue_pill.jpg" | relative_url}})

Hah, I knew you wouldn't be able to resist! C'mon, let's geek-out on things like [SPI buses](https://en.wikipedia.org/wiki/Serial_Peripheral_Interface) and [proxies](https://en.wikipedia.org/wiki/Proxy_pattern) for a bit. High five! :raised_hand::nerd_face:

## Circuit Control Over SPI
The base board runs its own ATSAME70 microcontroller with ARM Cortex-M7 processor and a wide range of peripherals. The firmware drivers for operating all the circuits on the base board are running on this processor. The firmware comes pre-installed when you order the base board. 

So, the question is, if the drivers for the base board circuits are all running on a processor on the base board, how are we able to control the circuits from our platform of choice? The answer: SPI. The base board provides a SPI interface which can be used to remotely control all of the circuits on the base board. The base board acts as the SPI slave device, and receives commands from the Platform which acts as the SPI master. Each time the platform needs to create, query or alter the state of a circuit, it issues a command over the SPI bus and receives a response back from the base board.

Although SPI is used as the underlying communication bus between the platform and the base board, you don't need to spend your time writing code to handle the SPI communications. If you're using a supported platform, then that has already been done for you and packaged up into an easy-to-use library. To find out how to download the library for your platform, refer to the relevant page in the [Platforms]({{"docs/platforms/Platforms.html" | relative_url}}) section. For each type of circuit on the base board, the library will provide a *handle* class for that circuit type. Each circuit handle object acts as a proxy for a single circuit of a particular circuit type on the base board. You specify must specify which circuit you want to control when you create the handle, as a base board often provides more than one instance of a given circuit type (eg: 4 digital outputs). Once a circuit handle object is created, methods can be invoked on the object to control the circuit that it represents. The way you interact with the circuits handle is much the same as you would to control a resource that is running locally on the same processor. However, we now know that behind the scenes the handle is relaying the command to the base board over a SPI bus. It waits until it gets a response from the base board and then returns the result.

## Circuit Control Over CAN
We've talked about how a platform mounted to a base board can control circuits on that base board over a SPI interface. SPI is great for this purpose. It's simple, fast and all platforms have it. However, there is an alternative communication interface available and it offers a different advantage. That interface is *CAN bus*, and it's advantage is *distance*. 

Sometimes it isn't practical to physically mount the platform to the base board. Maybe the platform is too big or has some custom features which make it physically incompatible. Maybe you want the platform headers available to mount conventional add-on boards. Or maybe you're debugging and it's more convenient to have the two boards separate. Whatever the reason; it doesn't matter if the distance between the boards is 1m or 100m. SPI won't work, but CAN certainly will.

The procedure for controlling a base board circuit over CAN bus is much the same as for SPI. We create a circuit handle object which acts as a proxy to that circuit, and issue commands to the circuit by invoking methods on the handle object. The only difference is that when the circuit handle is created you must explicitly specify to use CAN as the client (the default behaviour is to use SPI). Commands will then be dispatched to the base board over CAN bus. The details of the syntax to use are discussed in the [Programming]({{"docs/Programming.md" | relative_url}}) section.    

## Relaying between Base Boards
Large systems can be built up by linking multiple base boards together over CAN bus. A single platform that is linked to one of the base boards (either via the SPI or CAN interfaces) is able to control all of the base boards in the system.

Each base board in the system should be allocated a unique board id (refer to the [System]({{"docs/circuits/System.html" | relative_url}}) page for setting the board id). To control a circuit on a remote base board, the id of the base board must be specified when creating the circuit handle.

When a base board receives a circuit command over its SPI interface, it first checks the board id. If the board id in the command does not match its own board id it will forward the command to the appropriate board over CAN bus. Assuming a board with a matching id is on the network, it will receive the command, execute it, and send the result back to the original base board over CAN. The original base board will then pass the response back to the platform over SPI. From the perspective of the application running on the platform, there is no difference between issuing a command to a local base board or another base board that is connected to the CAN network (other than a small delay).