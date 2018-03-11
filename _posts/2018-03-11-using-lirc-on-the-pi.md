---
layout: post
title: "Using LIRC on the Raspberry Pi 3 (Raspbian Stretch)"
date: 2018-03-11
nocomments: false
area: "blog"
description: Updated guide on how to use LIRC on the Pi to read and write IR signals
tags: Embedded Electronics
---

# Using LIRC on the Raspberry Pi 3 (Raspbian Stretch)

I found a few guides on how to use LIRC (a mature library to read and write IR signals) and they seem to be a little dated. I am thankful for those guides I found but I just needed to provide the necessary updates in case anyone runs into the same issues I had.

The really cool thing about LIRC is that once the `lirc` module is loaded, the `lircd` daemon runs and provides a way to read decoded infrared signals on a Unix Socket! (`/var/run/lirc/lircd`). In this way you can just read and write that socket in your custom programs as if it were a network stream.

## Setup Guide

You will need to correctly install, configure, enable and run `lirc`. These steps will help you do that.

### 1. Install LIRC
```bash
sudo apt-get install lirc
```

### 2. Load the module at boot time

Pick an input and an output pin for the LIRC module to use. Refer to the Pins here. The Name column has the pin number the module arguments refer to.
In my case I have chosen GPIO.22 as output and GPIO.23 as input; see the file contents below.

Edit the Modules File:
```sudo nano /etc/modules```

It should look something like like this (I chose pins GPIO.23 and GPIO.22)
```
i2c-dev
lirc_dev
lirc_rpi gpio_in_pin=23 gpio_out_pin=22
```

### 3. Add the module's hardware configuration
Create the ```hardware.conf``` file:

```
sudo nano /etc/lirc/lircd.conf.d/hardware.conf
```

Enter the text so the file ends up looking like this:

```
DRIVER="default"
DEVICE="/dev/lirc0"
MODULES="lirc_rpi"
```

### 4. Change the coot configuration slightly
```
sudo nano /boot/config.txt
```

find the line where `dtoverlay=lirc` was commented out and change it so it looks like this:
```
dtoverlay=lirc-rpi,gpio_in_pin=23,gpio_out_pin=22,gpio_in_pull=up
```

## Usage Guide

### Starting, Stopping and Restarting the Service

Once you ar all set, you can start or stop the daemon that exposes the scoket very easily:

```bash
sudo /etc/init.d/lircd stop
sudo /etc/init.d/lircd start
sudo /etc/init.d/lircd restart
```

### Testing IR Input

You can test your receiver by first stopping the `lircd` daemon and then running lirc's `mode2` utility which is simply used to dump lirc's driver kernel messages to the console.

```bash
sudo /etc/init.d/lircd stop
sudo mode2 -d /dev/lirc0
```

### Testing IR Output

You can test your IR LED is correctly sending IR signals using the ```irsend``` utility. Check out the guide on how to do this in the following link. This requires you to record and assign certain IR sequences and save them to your configuration file.

http://www.instructables.com/id/How-To-Useemulate-remotes-with-Arduino-and-Raspber/

## Using `lirc` from your own programs

As stated before the IR signals are written and decoded to and from a socket. So all you need to do is connect to that Unix socket and normally read and write signals.

A `c#` example of how to read signals is available here:
https://github.com/shawty/raspberrypi-csharp-lirc
Basically, all you need to do is parse the incoming socket data as ACII as follows:
```
<code> <repeat count> <button name> <remote control name>
```

Example:
```
0000000000f40bf0 00 KEY_UP ANIMAX
```

In any case, you can always get to the documentation of the lircd socket here:
http://www.lirc.org/html/lircd.html

