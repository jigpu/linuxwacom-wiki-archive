---
title: Wacom Protocol Overview
permalink: wiki/Wacom_Protocol_Overview/
layout: wiki
tags:
 - DeveloperPages
---

Note: The original author of this page was late to the tablet party.
I've never worked with a serial tablet or an serial ISDV4 tablet. This
information was obtained by working with xf86-input-wacom code base,
reviewing kernel drivers, and filling in some holes with guesses.

The protocols Wacom devices use can be broken into two pieces.

-   Communication Protocol - This is the request/response protocol that
    applications can use for querying both static and dynamic properties
    of the tablet as well as allow applications to configure those
    dynamic properties. The protocol also defines how to tell the
    difference between a response to a request and a data packet
    containing stylus/button/mouse/etc information.

<!-- -->

-   Event Data Packet Format - This is the stylus/button/mouse/etc data
    that applications are really interested in. It is always in a
    compact binary format.

Since there are many different tablets with different feature set, there
are a wide variety of Event Data Packet formats. The Communication
Protocol though is much less feature centric and is shared among a much
wider range of tablets.

There are 2 communication protocols in active use and 1 historical
communication protocol. The historical protocol will be discussed first
as the other 2 active protocols are somewhat related.

Serial Protocol 4/5
-------------------

Wacom tablet popularity pre-dates the popularity of USB ports and were
connected using serial ports. The communication looks vaguely like the
AT protocol used with modems of same era. Requests are sent to the
tablet using somewhat readable ASCII characters and the response back
from tablet comes back in somewhat readable ASCII characters. The
responses are structured enough so that applications can detect the
difference between responses and event data packets.

The serial tablets all used the same communication protocol although not
all tablets support the full configuration command set. Serial devices
can be broken into two groups called Protocol 4 and Protocol 5 devices
(I have no idea if protocol 1, 2, and 3 existed in the wild). These are
also often referred to as Protocol IV and Protocol V.

The difference between those two "protocols" lies in the feature set of
the tablets and not really in how they communicate to applications.

Protocol 5 devices return a unique serial \# inside the event data
packet for each stylus the user owns. This allows user to assign unique
behaviour in an application to each stylus instead of having to create
button macros to reconfigure the 1 visible stylus that the application
see's. Another lesser feature is some models support more than 1 tool
being in proximity at a time (multi-touch before multi-touch was
popular!).

Protocol 4 devices do not return this serial \# and this shows up as a
different event data packet format.

Because Protocol 4 and Protocol 5 devices share the same communication
protocol and because the serial tablets are no longer being sold, the
terms Protocol 4 and Protocol 5 have come to be used more for tablet
feature set then anything to do with the communication protocol. In
fact, it is still used with USB devices to describe this same difference
in feature set.

But to keep everyone on their toes, the term Protocol 4/5 is also used
to refer to the original serial port communication protocol and its
related command set.

See [Serial Protocol IV](/wiki/Serial_Protocol_IV "wikilink") for more
information on this serial protocol.

Serial ISDV4 Protocol
---------------------

A communication protocol still in use is for wacom devices that
communicate over internal serial ports on Tablet PC's and it is called
ISDV4. I've no idea what ISDV4 stands for other than ISD V4. It may be
because they have more in common to Protocol 4 devices then they do to
Protocol 5 devices (no serial \#s). The communication protocol is a more
compact than the original Protocol 4/5 protocol as binary data is used
in responses. Also, it only supports a subset of the command set as
Protocol 4/5 devices.

Depending on the hardware, this protocol can support 2 different Event
Data Packets. One is used for typical stylus data while the other is
used for touchscreen touch data.

See the [ISDV4 Protocol](/wiki/ISDV4_Protocol "wikilink") page for further
information.

HID-Wacom Protocol
------------------

The final communication protocol in use is what I'll call USB HID-Wacom.
When serial tablets were replace with USB tablets, the designers leaned
on the USB HID standard to declare itself. When communicating, the
tablet can make use of a lot of the OS's HID infrastructure for reading
static configuration values and reading/writing dynamic configuration
values. Once configured, the device does not return data packets in
standard HID format. Instead, it returns data in a format that looks
like the original Protocol 4/5 Event Data Packets.

I've not compared like serial devices to like USB devices but there is a
good chance they share exact same data packet formats.

Recently, several Tablet PC's that used to use serial ISDV4 links are
now using USB links and sometimes have ISD V4 in their name. This can
cause some confusion to end users since this devices do not use the
serial ISDV4 Communication Protocol nor does their Event Data Packet
formats look anything like ISDV4 packets. The ISDV4 name in this case is
only indicating that its a Tablet PC using the HID-Wacom Communication
Protocol along with the Tablet PC Event Packet format.

See the [USB Protocol](/wiki/USB_Protocol "wikilink") page for further
information.
