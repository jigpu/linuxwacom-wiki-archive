---
title: ExpressKey Remote
permalink: wiki/ExpressKey_Remote/
layout: wiki
---

Resetting the ExpressKey Remote on Linux
========================================

You may occasionally find that you need to reset your ExpressKey Remote
(EKR) to get it to work properly. These steps will reset the EKR and
hopefully fix your problem.

Find the sysfs wacom\_remote directory:

`$ cd /sys`  
`$ find . -iname *wacom*`

There will be a line like the following:
./devices/pci0000:00/0000:00:14.0/usb3/3-4/3-4:1.0/0003:056A:0331.0001/wacom\_remote

List that line:  
`$ ls ./devices/pci0000:00/0000:00:14.0/usb3/3-4/3-4:1.0/0003:056A:0331.0001/wacom_remote`

The output will look like this:  
  
`18437 unpair_remote`

Where 18437 is the serial number of your device.

Reset your remote by changing to your wacom\_remote directory and
issuing the following commands (example):

`$ cd ./devices/pci0000:00/0000:00:14.0/usb3/3-4/3-4:1.0/0003:056A:0331.0001/wacom_remote`  
`$ sudo su`  
`$ echo '*' > unpair_remote`  

After that you'll have to repair your remote (see below).

Pairing the ExpressKey Remote
=============================

"How do I pair the ExpressKey Remote? \[1\]

The ExpressKey Remote comes pre-paired from the factory with an RF
receiver. You can find it inside the left hand side compartment on the
back. There is normally no need to remove the receiver although it can
be used on any USB port. If your ExpressKey Remote loses its pairing you
can re-pair by holding the ExpressKey Remote close to the left hand side
of Cintiq 27QHD while holding “ON” the ExpressKey Remote power slider
until the blue light at the bottom of the ExpressKey Remote begins to
flash. Each Cintiq 27QHD receiver can have 5 ExpressKey Remotes paired
at a time. Each ExpressKey Remote can only be paired with one
Cintiq/receiver however. Using an ExpressKey Remote on a different
Cintiq/receiver will require pairing."

\[1\]<http://www.wacom.com/en-kr/support/faqs/cintiq%2027qhd%20and%20cintiq%2027qhd%20touch/how%20do%20i%20pair%20the%20expresskey%20remote>
