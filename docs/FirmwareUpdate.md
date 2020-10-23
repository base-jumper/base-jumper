---
layout: page
title: Firmware Update
nav_order: 10
---

# Firmware Update

## Firmware Downloads
Coming soon.

## Procedure

> *If you've already done this once before, you can skip installation of tools by jumping to step 3.*  
> *If you just want to get out of bootloader mode (without updating firmware) see step 11*. 


1. [Download](https://www.microchip.com/DevelopmentTools/ProductDetails/PartNO/SAM-BA%20In-system%20Programmer) the SAM-BA application from Microchip. Be sure to grab v3 which is a command line tool (the earlier v2 is a GUI tool which isn't covered here). Extract the contents of the zip to a folder.
2. Add the SAM-BA folder to your PATH, so you can easily launch `sam-ba.exe` from the command line. Instructions here for [windows](https://docs.alfresco.com/4.2/tasks/fot-addpath.html), [linux](https://docs.oracle.com/cd/E19062-01/sun.mgmt.ctr36/819-5418/gaznb/index.html), [mac](https://www.architectryan.com/2012/10/02/add-to-the-path-on-mac-os-x-mountain-lion/).   
At this point you should be able to run the sam-ba command line tool. You might need to restart the terminal if you changed the PATH.  
![sam-ba at the command line]({{"assets/images/sam-ba-commandline.png" | relative_url }})
3. Download the firmware binary file from the downloads section on this page.
4. Plug your baseboard into your computers USB port.
5. Open the BaseJumper terminal. More details on this [here]({{"docs/terminal/Terminal.html" | relative_url}}).
6. At the BaseJumper terminal issue the instruction `bootloader`.
7. Cycle power to the baseboard. The baseboard should now be in bootloader mode.
8. Find the COM port number (windows) or /dev/ttyS device (linux) that has been assigned to the baseboard in bootloader mode and note it down. It may be different to normal mode. Wherever you see `COM5` in the following steps, replace it with your device number.
9. At the command line of your PC, use SAM-BA to erase the existing baseboard firmware. Remember to replace `COM5` with the appropriate serial port.
``` console
c:\> sam-ba -p serial:COM5 -d samv71 -a internalflash -c erase
```
10. Change directory to the folder that contains the firmware binary file that you downloaded in step 3. In this example we assume the folder is `c:\downloads`.
``` console
c:\> cd downloads

c:\downloads>
```
10. Program the new firmware into the base board. Replace `basejumper-base-1.0.0.bin` with the name of the firmware binary you downloaded.
``` console 
c:\downloads> sam-ba -p serial:COM5 -d samv71 -a internalflash -c write:basejumper-base-1.0.0.bin
```
11. Exit bootloader mode.
``` conole
c:\downloads> sam-ba -p serial:COM5 -d samv71 -a bootconfig -c writecfg:bootmode:flash
```
12. Power cycle the base board. It should now boot in normal mode running the new firmware.
