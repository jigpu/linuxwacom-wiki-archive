---
title: Kernel drivers
permalink: wiki/Kernel_drivers/
layout: wiki
---

Kernel Drivers
--------------

There exist a number of different kernel drivers that work with Wacom
hardware.

| Interface           | Driver            | Module Name | Location                                                        |
|---------------------|-------------------|-------------|-----------------------------------------------------------------|
| USB                 | wacom.ko          | wacom       | Upstream                                                        |
| Bluetooth           | wacom-hid.ko      | ???         | Upstream                                                        |
| Serial (ISDv4)      | wacom\_w8001.ko   | w8001       | Upstream                                                        |
| Serial (Protocol 4) | wacom\_serial.ko  | ???         | <http://cipht.net/2011/07/02/wacom_serial-initial-release.html> |
| Serial (Protocol 5) | wacom\_serial5.ko | ???         | <https://github.com/RoaldFre/wacom_serial5>                     |
| I2C                 | wacom\_i2c.ko     | wacom\_i2c  | Posted to input-wacom mailinglist                               |

### wacom.ko

The **wacom.ko** kernel driver provides hardware support for Wacom
tablets which communicate over USB, including the "Wireless Accessory
Kit" dongle.

### wacom-hid.ko

The **wacom-hid.ko** kernel driver provides support for Wacom tablets
that communicate over Bluetooth. At the moment, this is limited to the
Graphire4 Wireless and the Intuos4 Wireless. Despite the appearance, the
"Wireless Accessory Kit" available for use with some Bamboo-series
tablets is **not** a Bluetooth adapter and is handled by the
[wacom.ko](#wacom.ko "wikilink") driver.

### wacom\_w8001.ko

The **wacom\_w8001.ko** kernel driver provides support for Wacom
digitizers which communicate over a serial bus and use the [ISDv4
Protocol](/wiki/ISDv4_Protocol "wikilink"). The ISDv4 protocol is used
exclusively by digitizers integrated into 3rd-party products (e.g.
Tablet PCs). While most ISDv4 digitizers use the serial bus for
communication, there are a few which use USB and are handled by
[wacom.ko](#wacom.ko "wikilink").

### wacom\_serial.ko

The **wacom\_serial.ko** kernel driver provides support for Wacom
tablets that communicate over a serial bus and use [Protocol
IV](/wiki/Serial_Protocol_IV "wikilink"). This protocol was used by the
Digitizer II (UD series), PenPartner, Graphire, and Cintiq series of
Wacom serial tablets.

### wacom\_serial5.ko

The **wacom\_serial5.ko** kernel driver provides support for Wacom
tablets that communicate over a serial bus and use [Protocol
V](/wiki/Serial_Protocol_IV "wikilink"). This protocol was used by the Intuos
and Intuos2 serial tablets.

### wacom\_i2c.ko

The **wacom\_i2c.ko** kernel driver provides support for Wacom
digitizers that communicate over an I2C bus. At present, there is little
known hardware that uses this bus. The Samsung Galaxy Note does, but
uses a custom kernel driver whose code differs from the patches posted
to the input-wacom mailinglist.
