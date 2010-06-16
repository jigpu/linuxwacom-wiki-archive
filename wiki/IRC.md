---
title: IRC
permalink: wiki/IRC/
layout: wiki
---

The official linux wacom irc is \#linuxwacom on irc.freenode.net. Feel
free to ask problems on whatever issues you may have, but be patient as
there is not always someone watching the channel. It is recommended that
you ask any questions on the mailing list as well as in irc, as you may
get extra help in the mailing list even if it takes a while longer.

IRC channel management
----------------------

To add someone to the access list so they can promote themselves to
admin (op):

`/msg chanserv flags #linuxwacom +o nickname`

NOTE: It is the standard on freenode and generally a good idea to ONLY
op your self if needed. Such as if a spammer enters the channel or
channel maintenance needs to be done.

To op yourself if you don't have op and you are on the access list:
(currently roey, whot, and reportingsjr)

`/msg chanserv op #linuxwacom`

You can only run the following commands if you are an op.

To op someone:

`/mode #linuxwacom +o nickname`

To deop someone:

`/mode #linuxwacom -o nickname`

To add channel modes:

`/mode #linuxwacom +mode`

To remove channel modes:

`/mode #linuxwacom -mode`

Dealing with problem people
---------------------------

If you want someone to not be able to enter the channel, or to not talk
if they are in the channel (this bans them):

`/mode #linuxwacom +b nickname`

This is useful if you quickly want to mute someone.

To kick a person out of a channel:

`/kick nickname reason`

Reason is optional in this command. If you kick someone out of the
channel they can rejoin. If you want to permanently kick someone out of
the channel ban them first using the above command and then kick them.
They will not be able to rejoin.

Also, many people have auto-rejoin enabled where if they are kicked out
of a channel or lose internet connection then their client automatically
rejoins.
