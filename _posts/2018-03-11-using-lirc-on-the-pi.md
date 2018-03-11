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