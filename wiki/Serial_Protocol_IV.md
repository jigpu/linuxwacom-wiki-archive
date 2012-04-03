---
title: Serial Protocol IV
permalink: wiki/Serial_Protocol_IV/
layout: wiki
tags:
 - DeveloperPages
---

This information was derived from reading various versions of the
linuxwacom source and predecessors thereof. In this document:

wcmSerial : refers to the serial implementation in the linuxwacom 0.9.0 source, primarily:wcmSerial.c and wcmSerial.h but possibly other files  
xf86wacom : refers to a much older form of the driver, as can be browsed [here](http://cvsweb.xfree86.org/cvsweb/xc/programs/Xserver/hw/xfree86/common/Attic/xf86Wacom.c).  

Models Covered
--------------

According to the last version of wcmSerial, the following devices use
protocol IV:

| Model        | Identifying Prefix in Model String |
|--------------|------------------------------------|
| Digitizer II | UD                                 |
| Graphire     | ET                                 |
| Penpartner   | CT                                 |
| Cintiq       | PL or DT                           |
|              |                                    |

Request Format
--------------

Requests to the device are sent as carriage return (0x0d)-terminated
ASCII strings (no linefeed). In the case that a response is expected,
the response begins with the first two characters of the request. Note
that the PenPartner seems to be exceptional in that it does not wait for
a carriage return when sent ~\#, and more importantly, it does not
terminate its response with a carriage return.

### Summary of Known Requests

| Request string | Description                                         |
|----------------|-----------------------------------------------------|
| \\r\#          | Reset to protocol IV command set (\\r is CR)        |
| \\r$           | Reset to 9600 bps (sent at 19200 bps; \\r is CR)    |
| ~R             | Request a configuration string (expects a response) |
| ~C             | Request max coordinates (expects a response)        |
| ~\#            | Request model and ROM version (expects a response)  |
| MU1            | Multi-mode input                                    |
| OC1            | Origin in upper left                                |
| SU             | Suppress mode                                       |
| ~M0            | Enable all macro buttons                            |
| ~M1            | Disable macro buttons of group 1                    |
| IT0            | Max transmit rate (unit of 5 ms)                    |
| FM1            | Enable extra protocol for tilt management           |
| IN0            | Disable incremental mode                            |
| SR             | Enable continuous mode                              |
| PH1            | Enable pressure mode                                |
| SP             | Stop sending coordinates                            |
| ST             | Start sending coordinates                           |
|                |                                                     |

Initialization and Resetting
----------------------------

The approach taken by wcmSerial to initializing a serial device is as
follows:

1.  Set the link speed to 38400
2.  Send reset speed to the tablet ("\\r$")
3.  Wait 250 mS
4.  Send reset ("\\r\#") to the tablet
5.  Wait 75 mS
6.  Set the link speed to 19200
7.  Repeat steps 2 through 5
8.  Set the link speed to 9600
9.  Repeat steps 2 and 3
10. Send stop ("SP") to the tablet (not reset)
11. Wait 30 mS
12. Flush tablet (xf86FlushInput)
13. Proceed to model detection and model-specific initialization.
14. Send "ST" so the tablet starts sending us event packets

### Format of model response

The response to the model query ("~\#") is formed as follows:

` ~#`<Tablet model>` V`<Rom version>`  (Digitizer II)`  
` ~#`<Tablet model>`,V`<Rom version>`, (PenPartner)`

The original code states:

  
"look for the first V from the end of the string; this seems to be the
better way to find the version of the ROM."

The ROM versions, as observed presently, are of the form

` `<decimal integer>`.`<decimal integer>  
` `<decimal integer>`.`<decimal integer>`-`<decimal integer>

The original code uses the first two bytes after ~\# in the response to
identify the model.

### Format of coordinates response

The response to the coordinate query ("~C") is formed as follows:

` ~C<x>,<y>`

where x and y are decimal integers indicating the max X and Y
coordinates.

To resolve: in which units?  

### Format of configuration string response

The response to the configuration string query ("~R") is formed as
follows:

` ~R<A?>,<B?>,<C?>,<x>,<y>`

where A? is a hexadecimal integer of unknown purpose, B? and C? are
decimal integers of unknown purpose, and x and y are decimal integers
giving the resolution of the tablet.

To resolve: in which units? What is the purpose of the unknown elements?  

Event Packets
-------------

Event packets start with the MSB of the first byte set (1), and the MSB
of each subsequent byte unset (0). The basic protocol IV packet is as
follows: (bytes and bits will be zero-indexed to make reading the
associated code easier)

| Byte | Bits | Description                                                |
|------|------|------------------------------------------------------------|
| 0    | 7    | Always 1 -- marks beginning of packet                      |
|      | 6    | Pointing device in proximity?                              |
|      | 5    | Is this a stylus (1) or cursor (0)?                        |
|      | 4    | Reserved                                                   |
|      | 3    | Appears to indicate pad button press                       |
|      | 2    | Z (lowest bit of pressure on devices where max &gt; 255)   |
|      | 0-1  | X (top two bits of X coordinate)                           |
| 1    | 7    | Always 0                                                   |
|      | 0-6  | X (bits 7-13 of X coordinate)                              |
| 2    | 7    | Always 0                                                   |
|      | 0-6  | X (bits 0-6 of X coordinate)                               |
| 3    | 7    | Always 0                                                   |
|      | 3-6  | Buttons                                                    |
|      | 2    | Z (lowest or second lowest bit, depending on max pressure) |
|      | 0-1  | Y (top two bits of Y coordinate)                           |
| 4    | 7    | Always 0                                                   |
|      | 0-6  | Y (bits 7-13 of Y coordinate)                              |
| 5    | 7    | Always 0                                                   |
|      | 0-6  | Y (bits 0-6 of Y coordinate)                               |
| 6    | 7    | Always 0                                                   |
|      | 6    | Sign of Z (highest bit of pressure, sign-extend)           |
|      | 0-5  | Z (highest non-sign bits of pressure)                      |
|      |      |                                                            |

According to xf86wacom, enabling tilt mode adds the following two bytes:

| Byte | Bits | Description                      |
|------|------|----------------------------------|
| 7    | 7    | Always 0                         |
|      | 0-6  | X tilt (sign-extend highest bit) |
| 8    | 7    | Always 0                         |
|      | 0-6  | Y tilt (sign-extend highest bit) |

Unresolved Questions
--------------------

What is the maximum linkspeed these devices can run at? : wcmSerial seems to run all protocol IV devices at 9600 bps, but it's not clear that this is necessarily the only valid speed for some devices.  
Which protocol IV devices support tilt? : wcmSerial only handles tilt for protocol V devices, despite containing a comment which describes tilt in protocol IV. xf86wacom appears to handle tilt, and the comment claims it is supported on devices with ROM version 1.4 and above.  
