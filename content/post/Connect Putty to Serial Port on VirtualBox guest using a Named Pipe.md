---
title: "Connect Putty to Serial Port on VirtualBox guest using a Named Pipe"
date: 2018-04-28T10:48:38-08:00
tags: ["VirtualBox","PuTTY"]
---

This post describes how to connect to a serial port on a [VirtualBox](https://www.virtualbox.org/) guest VM using [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/).

The serial console has traditionally played a special role in system configuration, administration and recovery. Access through this method may provide special elevated privileges and/or not have the same authentication requirements as access through other means.

<!--more-->

For the VirtualBox VM you wish to connect to, go to the serial port settings.

* Enable the Serial Port and select the port number you want to connect to (ie, COM1)
* Change the Port Mode to **Host Pipe**
* Select **Create Pipe**
* Enter the Port/File Path as **\\.\pipe\{PIPE_NAME}**  
*{PIPE_NAME} can be any name of your choice*  
*ie: \\.\pipe\COM1 or \\.\pipe\MYPIPE*

![](/images/virtualbox-serial-ports.png)

Fire up your VM and launch PuTTY.

* Select Connection Type of Serial
* Enter your Port/File Path for Serial Line  
*ie: \\.\pipe\COM1*  
*Leave the speed at the default 9600*

![](/images/PuTTYConfiguration.png)

Open the connection and PuTTY should now be connected to Serial Port via your named pipe.  
You may have to hit enter one or more times for something to be displayed.

![](/images/pipeCOM1-PuTTY.png)
