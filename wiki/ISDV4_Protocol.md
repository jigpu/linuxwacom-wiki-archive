---
title: ISDV4 Protocol
permalink: wiki/ISDV4_Protocol/
layout: wiki
tags:
 - DeveloperPages
---

This information was derived from reading xf86-input-wacom source code
and linux wacom\_w8001 driver source code.

Command/Request Format
----------------------

Compared to the Serial Protocol 4/5 communication protocol, the ISDV4
protocol supports a much smaller set of commands and request. Also, all
commands and requests are 1 character in length with no termination
character (specifically, no carriage return used). In the case that a
response is expected, the response will have the 2 MSB (bits 6 and 7)
set in the first byte of the response.

### Summary of Known Command/Requests

| Request string | Description                                                                     |
|----------------|---------------------------------------------------------------------------------|
| \*             | ISDV4\_QUERY - Query stylus configuration data (expects a response)             |
| %              | ISDV4\_TOUCH\_QUERY - Query touchscreen configuration data (expects a response) |
| 0              | ISDV4\_STOP - Stop sending coordinates                                          |
| 1              | ISDV4\_START - Start sending coordinates                                        |
| &              | ISDV4\_RESET - Touch panel reset request                                        |
|                |                                                                                 |

Initialization
--------------

The approach taken by Linux wacom\_w8001 driver to initializing a serial
device is as follows. xf86-input-wacom uses same approach but will try
additional baud rates as well as skip touch detection if it knows device
can not support it.

This sequence assumes attachdevice has already been executed to set
baudrate correctly to either 38400 or 19200.

1.  Send STOP command to the tablet (ISDV4\_STOP)
2.  Wait 250 ms
3.  Read and discard any old data on line
4.  Send query command (ISDV4\_QUERY)
5.  Wait for query response
6.  Parse valid response to find features of tablet PC.
7.  Send touch query command (ISDV4\_TOUCH\_QUERY).
8.  If valid touch response, parse to find features of touchscreen.
9.  Send start command (ISDV4\_START) when ready to receive coordinate
    data.

Format of Packets
-----------------

Response and Event packets start with the bit 7 (MSB) of the first byte
set (1) and the MSB of each subsequent byte unset (0).

Response and Event packets are differentiated by the Control bit (bit 6)
in the first byte. It will be set (1) for Responses and will be unset
(0) for Events.

### Format of Stylus Query Response

| Byte | Bits | Description                           |
|------|------|---------------------------------------|
| 0    | 7    | Always 1 -- marks beginning of packet |
|      | 6    | Always 1 - indicates control packet   |
|      | 0-5  | Data ID                               |
| 1    | 7    | Always 0                              |
|      | 0-6  | Max X (bits 7-13 of X coordinate)     |
| 2    | 7    | Always 0                              |
|      | 2-6  | Max X (bits 2-6 of X coordinate)      |
| 3    | 7    | Always 0                              |
|      | 0-6  | Max Y (bits 7-13 of Y coordinate)     |
| 4    | 7    | Always 0                              |
|      | 2-6  | Max Y (bits 2-6 of Y coordinate)      |
| 5    | 7    | Always 0                              |
|      | 0-6  | Max Pressure (bits 0-6 of Pressure).  |
| 6    | 7    | Always 0                              |
|      | 5-6  | Max X (bits 0-1 of X coordinate)      |
|      | 3-4  | Max Y (bits 0-1 of Y coordinate)      |
|      | 0-2  | Max Pressure (bits 7-9 of Pressure)   |
| 7    | 7    | Always 0                              |
|      | 0-6  | Max Y Tilt                            |
| 8    | 7    | Always 0                              |
|      | 0-6  | Max X Tilt                            |
| 9    | 7    | Always 0                              |
|      | 0-6  | Version (bits 14-7)                   |
| 10   | 7    | Always 0                              |
|      | 0-6  | Version (bits 0-6)                    |
|      |      |                                       |

Tilt support is optional. It is supported by device when both Tilt X and
Tilt Y values are non-zero.

### Format of Touch Query Response

| Byte | Bits | Description                           |
|------|------|---------------------------------------|
| 0    | 7    | Always 1 -- marks beginning of packet |
|      | 6    | Always 1 - indicates control packet   |
|      | 0-5  | Data ID                               |
| 1    | 7    | Always 0                              |
|      | 0-6  | Touch panel resolution                |
| 2    | 7    | Always 0                              |
|      | 5-6  | Max X (bits 0-1 of X coordinate)      |
|      | 3-4  | Max Y (bits 0-1 of Y coordinate)      |
|      | 0-2  | Sensor ID                             |
| 3    | 7    | Always 0                              |
|      | 0-6  | Max X (bits 7-13 of X coordinate)     |
| 4    | 7    | Always 0                              |
|      | 2-6  | Max X (bits 2-6 of X coordinate)      |
| 5    | 7    | Always 0                              |
|      | 0-6  | Max Y (bits 7-13 of Y coordinate)     |
| 6    | 7    | Always 0                              |
|      | 2-6  | Max Y (bits 2-6 of Y coordinate)      |
| 7    | 7    | Always 0                              |
|      | 0-6  | Capacitance resolution                |
| 8    | 7    | Always 0                              |
|      | 0-6  | Reserved                              |
| 9    | 7    | Always 0                              |
|      | 0-6  | Version (bits 14-7)                   |
| 9    | 7    | Always 0                              |
|      | 0-6  | Version (bits 0-6)                    |
|      |      |                                       |

Some older touchscreens reported zero for X/Y and these need to default
to 1024 maximums and a panel resolution of 10. If these same
touchscreens report a panel resolution then the maximum X/Y can be
computed by (1 &lt;&lt; panel resolution).

Stylus Event Packets
--------------------

1 bit is used to indicate both eraser touching tablet as well as side
button 2 pressed. If both tip (opposite from eraser) is set and button
2/eraser bit is set then you can assume its button 2. If eraser is set
but not tip then you can assume its eraser.

This should be decided when first coming into proximity. If decided its
an eraser and the bit ever clears then some cleanup logic needs to be
performed to convert pack to a pen without confusing user land.

| Byte | Bits | Description                           |
|------|------|---------------------------------------|
| 0    | 7    | Always 1 -- marks beginning of packet |
|      | 6    | Always 0 - indicates event packet     |
|      | 5    | Proximity                             |
|      | 3-4  | Reserved                              |
|      | 2    | Side Button 2/Eraser pressed          |
|      | 1    | Side Button 1 pressed                 |
|      | 0    | Tip pressed                           |
| 1    | 7    | Always 0                              |
|      | 0-6  | X (bits 7-13 of X coordinate)         |
| 2    | 7    | Always 0                              |
|      | 2-6  | X (bits 2-6 of X coordinate)          |
| 3    | 7    | Always 0                              |
|      | 0-6  | Y (bits 7-13 of Y coordinate)         |
| 4    | 7    | Always 0                              |
|      | 2-6  | Y (bits 2-6 of Y coordinate)          |
| 5    | 7    | Always 0                              |
|      | 0-6  | Pressure (bits 0-6 of Pressure)       |
| 6    | 7    | Always 0                              |
|      | 5-6  | X (bits 0-1 of X coordinate)          |
|      | 3-4  | Y (bits 0-1 of Y coordinate)          |
|      | 0-2  | Pressure (bits 7-9 of Pressure)       |
| 7    | 7    | Always 0                              |
|      | 0-6  | Y Tilt                                |
| 8    | 7    | Always 0                              |
|      | 0-6  | X Tilt                                |
|      |      |                                       |

Touch Event Packets
-------------------

Length of single-touch packets can be either 5 or 7 bytes depending on
model. Length of multi-touch packets are 13 bytes. The packet format is
the same up to length of packet.

Packets of length 5 means single touch device that does not support
reporting capacitance while a length 7 means single touch device that
does support reporting capacitance. In these cases, bit 1 of byte 0 zero
should be ignored.

Capacitance is not currently used by any drivers. It could be used as an
approximation for pressure in same way to width of touch in other
devices is used as pressure approximation. In my limited experience with
capacitance reports, they have the following issues: They don't scale
linearly with actual pressure, they report higher values during fast
movements, and they report high values during multitouch. In practice,
these limitations aren't so bad. The only real application of pressure
reports with touch that I know of is very low resolution drawings with 1
finger.

| Byte | Bits | Description                                 |
|------|------|---------------------------------------------|
| 0    | 7    | Always 1 -- marks beginning of packet       |
|      | 6    | Always 0 - indicates event packet           |
|      | 0-5  | Reserved                                    |
|      | 1    | 2nd finger touch                            |
|      | 0    | 1st finger touch                            |
| 1    | 7    | Always 0                                    |
|      | 0-6  | X (bits 7-13 of X coordinate)               |
| 2    | 7    | Always 0                                    |
|      | 0-6  | X (bits 0-6 of X coordinate)                |
| 3    | 7    | Always 0                                    |
|      | 0-6  | Y (bits 7-13 of Y coordinate)               |
| 4    | 7    | Always 0                                    |
|      | 0-6  | Y (bits 0-6 of Y coordinate)                |
| 5    | 7    | Always 0                                    |
|      | 0-6  | Capacitance (bits 7-13 of ouch capacitance) |
| 6    | 7    | Always 0                                    |
|      | 0-6  | Capacitance (bits 0-6 of Touch capacitance) |
| 7    | 7    | Always 0                                    |
|      | 0-6  | X (bits 7-13 of X coordinate)               |
| 8    | 7    | Always 0                                    |
|      | 0-6  | X (bits 0-6 of X coordinate)                |
| 9    | 7    | Always 0                                    |
|      | 0-6  | Y (bits 7-13 of Y coordinate)               |
| 10   | 7    | Always 0                                    |
|      | 0-6  | Y (bits 0-6 of Y coordinate)                |
| 11   | 7    | Always 0                                    |
|      | 0-6  | Capacitance (bits 7-13 of ouch capacitance) |
| 12   | 7    | Always 0                                    |
|      | 0-6  | Capacitance (bits 0-6 of Touch capacitance) |
|      |      |                                             |
