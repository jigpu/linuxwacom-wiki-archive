---
title: Serial Help
permalink: wiki/Serial_Help/
layout: wiki
---

### Woe Not Serial Woes

This page is an effort to help aid you in setting up your serial tablet
by braking down the main problem areas. With some time and patients, you
should be able to apply the appropriate changes in order to make use of
your Wacom serial device.

### Serial Woes Check List

We have a few situations that can arise when trouble shooting a serial
tablet.

1.  The serial port has not been identified and set to the appropriate
    uart and irq values of your serial hardware.
2.  The serial port has been assigned to another program, interfering
    with the tablet signals
3.  The older wacom driver that supports serial tablets is incompatible
    with the X server you are running
4.  You are running X server 1.7 or later with the newer
    xf86-input-wacom driver which does not support serial tablets at the
    moment.

### Serial Port Configuration

Before you can proceed to diagnose other areas of setting up your Wacom
serial tablet, you need to first make sure the serial values are set up
correctly. You do this by comparing your BIOS values with the values set
when the kernel boots up. The values in the BIOS will show up as COM1
through COM3. Write the UART and IRQ values done, then reboot your
system. Check your kernel boot log with the 'dmesg' command from a
console, your looking to find some ttySX entries.

    Host@User# dmesg | grep ttyS
    serial8250: ttyS0 at I/O 0x3f8 (irq = 4) is a 16550A
    serial8250: ttyS3 at I/O 0x2e8 (irq = 3) is a 16550A
    00:06: ttyS0 at I/O 0x3f8 (irq = 4) is a 16550A
    00:07: ttyS3 at I/O 0x2e8 (irq = 3) is a 16550A

Most of the time on a computer with one serial port it will be set to
ttyS0 in the /dev directory. On my system I have a DE-9 serial port and
a DA-15 gameport. Above you can see that the DE-9 is the ttyS0 device,
with the gameport being assigned ttyS3. These same values coincide in
the BIOS which is what I wanted to verify.

    Host@User# setserial -a /dev/ttyS0
    /dev/ttyS0, Line 0, UART: 16550A, Port: 0x03f8, IRQ: 4
            Baud_base: 115200, close_delay: 50, divisor: 0
            closing_wait: 3000
            Flags: spd_normal skip_test

    Host@User# setserial -a /dev/ttyS3
    /dev/ttyS3, Line 3, UART: 16550A, Port: 0x02e8, IRQ: 3
            Baud_base: 115200, close_delay: 50, divisor: 0
            closing_wait: 3000
            Flags: spd_normal skip_test

    Host@User# setserial -g /dev/ttyS*
    /dev/ttyS0, UART: 16550A, Port: 0x03f8, IRQ: 4
    /dev/ttyS1, UART: unknown, Port: 0x02f8, IRQ: 3
    /dev/ttyS2, UART: unknown, Port: 0x03e8, IRQ: 4
    /dev/ttyS3, UART: 16550A, Port: 0x02e8, IRQ: 3

The print out above shows 4 serial devices being created regardless of
whether they actually exist. This is due to a default of 4 serial
devices being set in the kernels source code before it is built. You can
bypass this with the 8250.nr\_uarts kernel option telling the kernel to
create more serial devices. This is needed for when you want to install
a serial expansion card that attaches to the PCI interface, the ports
will not show up at ttyS0 through ttyS3 and will be created at ttyS4
thereafter.

Below is an example entry in the menu.lst grub boot configuration file.

    title           Debian GNU/Linux, kernel 2.6.34
    root            (hd0,0)
    kernel          /boot/vmlinuz-2.6.34 root=/dev/sda1 ro 8250.nr_uarts=6

In debian your setserial setting are saved in the /etc/serial.conf file.
Sometimes your serial card will not show up with the appropriate values
needed, in which case you will have to manually edit this file. The best
way to find out the UART and IRQ values for your cards serial ports is
to use the lspci command with the -vv option which will output extra
verbose infromation.

    02:0b.0 Serial controller: Lava Computer mfg Inc Lava DSerial-PCI Port A (prog-if 02 [16550])
            Subsystem: Lava Computer mfg Inc Lava DSerial-PCI Port A
            Control: I/O+ Mem- BusMaster- SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx-
            Status: Cap- 66MHz- UDF- FastB2B+ ParErr- DEVSEL=slow >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
            Interrupt: pin A routed to IRQ 23
            Region 0: I/O ports at dfa8 [size=8]
            Kernel driver in use: serial
            Kernel modules: 8250_pci

    02:0b.1 Serial controller: Lava Computer mfg Inc Lava DSerial-PCI Port B (prog-if 02 [16550])
            Subsystem: Lava Computer mfg Inc Lava DSerial-PCI Port B
            Control: I/O+ Mem- BusMaster- SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx-
            Status: Cap- 66MHz- UDF- FastB2B+ ParErr- DEVSEL=slow >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
            Interrupt: pin A routed to IRQ 23
            Region 0: I/O ports at dfe0 [size=8]
            Kernel driver in use: serial
            Kernel modules: 8250_pci

The previous text is the print out of the lspci command showing my Lava
DSerial-PCI card. There are two sections, one for port A and one for
port B. This is the correct hardware information I need to pass to the
setserial command at boot time. Below is an example of the parameters
being specified in the serial.conf file. Some of the other values
related to the serial card I found by searching the internet.

    /dev/ttyS0 uart 16550A port 0x03f8 irq 4 spd_normal skip_test closing_wait 3000
    /dev/ttyS3 uart 16550A port 0x02e8 irq 3 spd_normal skip_test closing_wait 3000
    /dev/ttyS4 uart 16550A port 0xdfa8 irq 23 spd_normal skip_test close_delay 256 closing_wait 15360
    /dev/ttyS5 uart 16550A port 0xdfe0 irq 23 spd_normal skip_test close_delay 256 closing_wait 15360

The main idea of the above examples is to give you an idea of how check
and modify the serial port values regardless of which distribution of
linux you are using. If the values are correct, matching the information
given in the BIOS, and the information printed by programs such as
lspci, you can check this section off.
