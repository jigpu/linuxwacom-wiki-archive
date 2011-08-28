---
title: USB Protocol
permalink: wiki/USB_Protocol/
layout: wiki
tags:
 - DeveloperPages
---

This information was derived from linux wacom USB driver source code and
monitoring data from Bamboo Pen&Touch device. You can get a good deal of
information for Generic HID devices by looking at files in
/sys/kernel/debug/hid but this does not help for most Wacom USB devices.
Another way is to look at the raw USB data of Wacom device. First, find
USB Bus Wacom is plugged into:

lsusb \| grep Wacom

"Bus 002" means its own bus 2. Now, you can monitor packets using
following sysfs path. Replace the 2 in "2u" with bus \# found
previously.

cat /sys/kernel/debug/usb/usbmon/2u

Command/Request Format
----------------------

When Wacom devices transitioned to USB interfaces, there was still the
high level need to send configuration commands and to request
configuration and physical attribute information. Wacom presents itself
over USB as a USB Human Interface Device (HID) which supports this need
a standards based way. This also means it can work to a certain existent
with out any special drivers as long as the OS has a generic HID driver
with digitizer support.

USB HID Overview - <http://en.wikipedia.org/wiki/USB_HID> HID Page
documents - <http://www.usb.org/developers/hidpage/>

HID Usage Tables 1.12 defines usage tables with enough information to
support most common features of a Tablet (Digitizer class). A Pen and
Puck/Cursor can be declared and those tools can support X, Y, Pressure,
X Tilt, Y Tilt, Touching tablet versus In Range/Proximity of Tablet,
Twisting, Tip versus Eraser, at least 1 Stylus/Barrow Button, and
various buttons on the tablet itself.

This is enough information to fully represent a typical non-touch
Protocol 4 Tablet and so those could work with a generic HID Digitizer
driver. Protocol 5 tablets support tools beyond Pen and Puck's and also
support a serial number per tool and so that functionality would be lost
if used with a generic HID digitizer driver.

The HID Usage Tables also define a more specific Pen device class to be
used with devices that have an integrated display (Tablet PC fall into
this class). Since Wacom devices with integrated displays never support
Protocol 5 features, they could also be well handled by using a generic
HID Digitizer driver.

Wacom does make some tablets and screens that support reporting single
touches. These could be well represented by HID Usage definitions for
Touchpad and Touchscreens respectively and would also work with a
generic HID driver.

Wacom also makes some tablets and screens that support reporting
multiple touches. The HID Usage Tables as of 1.12 do not define a good
way to support these multitouch reports though. Microsoft, in 2009,
submitted a proposal for new multitouch reports and now both Windows and
Linux have a generic HID Multitouch driver. Since Wacom was shipping
devices before 2009, they solved the issue in a way that is not
compatible with these HID Multitouch drivers.

Because Protocol 5 devices and Multitouch devices shipping before 2009,
Wacom has chosen to support a special mode on their devices where it
disables reporting digitizer data using typical HID messaging and
instead sends packets that look similar to their previous serial and
ISDV4 devices.

When I mentioned earlier that wacom devices COULD be handled by a
generic HID digitizer driver, I have no idea if the devices do function
acceptable with a generic HID driver. That is because all drivers I have
worked with immediately set Wacom devices into the mode that disables
standard HID reports.

Summary of Known Command/Requests
---------------------------------

TODO: Command to switch packet format mode.

Format of Packets
-----------------

There are 4 versions of Stylus Event Packets and 3 versions of Touch
packets.

### Bamboo Stylus Event Packets

The Bamboo Stylus Event Packet has been used by all versions of USB
Bamboo tablets. The packet's length is 9.

| Byte | Bits | Description                                                                                                                                                        |
|------|------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 0    | 7-0  | Wacom Report ID - Always 0x02                                                                                                                                      |
| 1    | 7    | Reserved - Always 1                                                                                                                                                |
|      | 6-4  | Proximity - Kernel looks only at 5-4 bits. I see all set to 1 while in proximity though. Most likely part represents Tool ID like other Wacom Stylus packets have. |
|      | 3    | Tool Type - 0x0 = Pen 0x1 = Eraser                                                                                                                                 |
|      | 2    | Stylus Rocker Button 2                                                                                                                                             |
|      | 1    | Stylus Rocker Button 1                                                                                                                                             |
|      | 0    | Pen Tip                                                                                                                                                            |
| 2    | 7-0  | X (bits 7-0 of X coordinate)                                                                                                                                       |
| 3    | 7-0  | X (bits 13-8 of X coordinate)                                                                                                                                      |
| 4    | 7-0  | Y (bits 7-0 of Y coordinate)                                                                                                                                       |
| 5    | 7-0  | Y (bits 13-8 of Y coordinate)                                                                                                                                      |
| 6    | 7-0  | Pressure (bits 7-0 of Pressure)                                                                                                                                    |
| 7    | 7-0  | Pressure (bits 13-8 of Pressure)                                                                                                                                   |
|      |      |                                                                                                                                                                    |

### Bamboo Touch v1 Event Packets

The Bamboo Touch Event Packets are used by Bamboo devices that support
touch and has a packet length of 22.

The format documented below is used by Bamboo Touches with product ID's
between 0xD0 and 0xD4.

The firmware reports a touch in the lowest available slot upon initial
contact. The firmware performs finger tracking and continues to report
the touch in the same slot; even when a lower \# slot become available.

This is important to understand for gesture logic that accounts for
order of finger touching. Slot \#1 does not always mean the oldest
touch.

The "Phantom" touch described below is always reported when 1 or more
touches is reported. I've not looked closely but I assume its a midpoint
calculation during 2 touches or maybe a corner of a bounding box.

| Byte | Bits | Description                                         |
|------|------|-----------------------------------------------------|
| 0    | 7-0  | Wacom Report ID - Always 0x2                        |
| 1    | 7-4  | Reserved - Always 0                                 |
|      | 3    | Tablet Button 4                                     |
|      | 2    | Tablet Button 3                                     |
|      | 1    | Tablet Button 2                                     |
|      | 1    | Tablet Button 1                                     |
| 2    | 7-0  | Touch Slot 1 Pressure                               |
| 3    | 7    | Touch Slot 1 Detected                               |
|      | 6-3  | Reserved - Always 0                                 |
|      | 2-0  | Touch Slot 1 X (bits 10-8 of X coordinate)          |
| 4    | 7-0  | Touch Slot 1 X (bits 7-0 of X coordinate)           |
| 5    | 7-3  | Reserved - Always 0                                 |
|      | 2-0  | Touch Slot 1 Y (bits 10-8 of Y coordinate)          |
| 6    | 7-0  | Touch Slot 1 Y (bits 7-0 of Y coordinate)           |
| 7    | 7    | Touch Phantom Detected                              |
|      | 6-3  | Reserved - Always 0                                 |
|      | 2-0  | Touch Phantom X (bits 10-8 of X coordinate)         |
| 8    | 7-0  | Touch Phantom X (bits 7-0 of X coordinate)          |
| 9    | 7-3  | Reserved - Always 0                                 |
|      | 2-0  | Touch Phantom (bits 10-8 of Y coordinate)           |
| 10   | 7-0  | Touch Phantom (bits 7-0 of Y coordinate)            |
| 11   | 7-0  | Touch Slot 2 Pressure                               |
| 12   | 7    | Touch Slot 2 Detected                               |
|      | 6-3  | Reserved - Always 0                                 |
|      | 2-0  | Touch Slot 2 X (bits 10-8 of X coordinate)          |
| 13   | 7-0  | Touch Slot 2 X (bits 7-0 of X coordinate)           |
| 14   | 7-3  | Reserved - Always 0                                 |
|      | 2-0  | Touch Slot 2 Y (bits 10-8 of Y coordinate)          |
| 15   | 7-0  | Touch Slot 2 Y (bits 7-0 of Y coordinate)           |
| 16   | 7    | Unknown - 1 when one or more touch. 0 when no touch |
|      | 6    | Reserved - Always 0                                 |
|      | 5    | 1 when next packet will have 3 touches for 1st time |
|      | 4    | 1 when next packet will have 2 touches for 1st time |
|      | 3-2  | 0x3 when 3 fingers touching                         |
|      | 1    | 1 when next packet will have 2 touches for 1st time |
|      | 0    | Unknown - Alternates value each packet.             |
| 17   | 7-6  | Reserved - Always 0                                 |
|      | 5-4  | Touch Count (0, 1, 2, or 3)                         |
|      | 3-0  | Reserved - Always 0x01 when 1 or more touches       |
| 18   | 7-0  | Reserved - Always 0                                 |
| 19   | 7-0  | Reserved - Always 0                                 |
|      |      |                                                     |

### Bamboo Touch v2 Event Packets

The Bamboo Touch v2 Event Packets are used by later Bamboo devices that
support touch and has a packet length of 22.

The format documented below is used by Bamboo Touches with product ID's
greater than 0xD4.

The format is still not well understood and no Linux kernel driver
exists. For example, its not know how to tell how many touches exist and
which slot the touches are reported in. Its somewhat useable with v2
driver when we get lucky and 1 finger touch lands in Slot 1. Since the
packet shifted Slot 2 down a byte (took away pressure), two finger
touches will not work with v1 driver.

| Byte | Bits | Description                                                    |
|------|------|----------------------------------------------------------------|
| 0    | 7-0  | Wacom Report ID - Always 0x2                                   |
| 1    | 7    | Always 1 to indicate v2.                                       |
| 1    | 6-4  | Reserved - Always 1 to indicate v2.                            |
|      | 3    | Tablet Button 4                                                |
|      | 2    | Tablet Button 3                                                |
|      | 1    | Tablet Button 2                                                |
|      | 1    | Tablet Button 1                                                |
| 2    | 7-0  | Unknown - 0x40 seems to alternate. 0x18 seems fixed.           |
| 3    | 7    | Touch Slot 1 Detected                                          |
|      | 6-3  | Reserved - Always 0                                            |
|      | 2-0  | Touch Slot 1 X (bits 10-8 of X coordinate)                     |
| 4    | 7-0  | Touch Slot 1 X (bits 7-0 of X coordinate)                      |
| 5    | 7-3  | Reserved - Always 0                                            |
|      | 2-0  | Touch Slot 1 Y (bits 10-8 of Y coordinate)                     |
| 6    | 7-0  | Touch Slot 1 Y (bits 7-0 of Y coordinate)                      |
| 7    | 7    | Touch Phantom Detected                                         |
|      | 6-3  | Reserved - Always 0                                            |
|      | 2-0  | Touch Phantom X (bits 10-8 of X coordinate)                    |
| 8    | 7-0  | Touch Phantom X (bits 7-0 of X coordinate)                     |
| 9    | 7-3  | Reserved - Always 0                                            |
|      | 2-0  | Touch Phantom (bits 10-8 of Y coordinate)                      |
| 10   | 7-0  | Touch Phantom (bits 7-0 of Y coordinate)                       |
| 11   | 7    | Touch Slot 2 Detected                                          |
|      | 6-3  | Reserved - Always 0                                            |
|      | 2-0  | Touch Slot 2 X (bits 10-8 of X coordinate)                     |
| 12   | 7-0  | Touch Slot 2 X (bits 7-0 of X coordinate)                      |
| 13   | 7-3  | Reserved - Always 0                                            |
|      | 2-0  | Touch Slot 2 Y (bits 10-8 of Y coordinate)                     |
| 14   | 7-0  | Touch Slot 2 Y (bits 7-0 of Y coordinate)                      |
| 15   | 7-0  | Unknown - 0x01 seems fixed. 0x30 seems related to touch count. |
| 16   | 7-0  | Unknown                                                        |
| 17   | 7-0  | Unknown                                                        |
| 18   | 7-0  | Unknown                                                        |
| 19   | 7-0  | Unknown                                                        |
|      |      |                                                                |
