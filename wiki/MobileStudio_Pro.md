---
title: MobileStudio Pro
permalink: wiki/MobileStudio_Pro/
layout: wiki
---

The **MobileStudio Pro** is a series of tablet PCs created by Wacom.
This series is the successor to their older **Cintiq Companion** line.

Models
------

| Model Number  | Model Name                 | CPU                       | RAM        | GPU                     | VRAM                      | Disk       | Other features |
|---------------|----------------------------|---------------------------|------------|-------------------------|---------------------------|------------|----------------|
| 2016          |                            |                           |            |                         |                           |            |                |
| DTH-W1320     | Wacom MobileStudio Pro 13" | Intel Core i5             | 4 GB DDR3  | Intel Iris Graphics 550 |                           | 64 GB SSD  |                |
| 8 GB DDR3     | 128 GB SSD                 |                           |            |                         |                           |            |                |
| Intel Core i7 | 256 GB SSD                 |                           |            |                         |                           |            |                |
| 16 GB DDR3    | 512 GB SSD                 | Intel RealSense 3D Camera |            |                         |                           |            |                |
| DTH-W1320     | Wacom MobileStudio Pro 16" | Intel Core i5             | 8 GB DDR3  | NVIDIA Quadro M600M     | 2 GB GDDR5                | 256 GB SSD |                |
| Intel Core i7 | 16 GB DDR3                 | NVIDIA Quadro M1000M      | 4 GB GDDR5 | 512 GB SSD              | Intel RealSense 3D Camera |            |                |
|               |                            |                           |            |                         |                           |            |                |

Known Linux Issues
------------------

-   Random reboots have been experienced by multiple users, but the
    cause is not yet known. It appears to be related in some way to
    power management and CPU/GPU load. [Possible bug
    1](https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1535048),
    [possible bug 2](https://bugs.archlinux.org/task/53227).
-   Bluetooth connections may be interrupted after connecting to a wifi
    network ([Twitter
    suggestion](https://twitter.com/TysonTanX/status/933150431856832513)).
-   The front-facing camera and rear-facing RealSense cameras are both
    USB connected and seem to work under Linux, but the rear-facing
    camera on models without RealSense are not detected by the kernel
    ([Twitter
    thread](https://twitter.com/zemarmot/status/934560771924791296)).
-   The volume buttons do not work ([Kernel.org Bug
    \#197991](https://bugzilla.kernel.org/show_bug.cgi?id=197991)).

Links
-----

-   <https://www.prnewswire.com/news-releases/wacom-mobilestudio-pro--freedom-to-create-anywhere-300339547.html>
