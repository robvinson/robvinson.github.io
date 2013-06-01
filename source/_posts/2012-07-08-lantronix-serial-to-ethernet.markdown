---
layout: post
title: "Lantronix serial-to-ethernet"
date: 2012-07-08 20:00
comments: true
categories: [scada, metasploit, security]
---

Serial-to-Ethernet Devices
--------------------------

I've come across various serial-to-ethernet adapters over the years.  What's a serial-to-ethernet adapter?  They allow you to plug in some device that communicates information via serial (E.G. [RS-232][1]) into an IP network. For example, maybe you have some temperature sensor shares out readings via a serial interface.  You hook this sensor into an IP network via a serial-to-ethernet adapter.  Voilà, now you can get your readings from your desktop, server, etc.

Lantronix
---------
Recently I've crossed paths with some [Lantronix][2] serial-to-ethernet adapters.

Some notable things that stand out on these devices:

- Web-based configuration has a blank default username/password
- Telneting to 9999/TCP offers up another means of configuration, again no password is necessary by default.
- 30718/UDP runs a service by which information about the device can be queried, and configuration may be done.

30718/UDP
---------
There are many functions available over 30718/UDP.  After some some time
googling, I discovered a [zip file][3] containing documents which detail these functions.

- Node reset (03)
- Firmware version query (F6)
- Response to firmware version query (F7)
- Setup record query (F8)
- Response to setup record query (F9)
- Set configuration - supplying a setup record (FA)
- Response to set configuration (FB)
- Set IP address (FC)

So, the device can be reset, configured, and have its IP address changed all via UDP, huh...

Particularly funny is that response to a setup record query has a field for "telnet config password".  If I'm understanding correctly, if 9999/TCP has a password, you can just look up the password over 30718/UDP.

Scanning for Lantronix Devices
------------------------------
My [Metasploit][4] scanner module can be found at [robvinson/metasploit-modules][5] on GitHub.

What a scan looks like:

```
msf > use auxiliary/scanner/scada/lantronix_discover
msf  auxiliary(lantronix_discover) > set RHOSTS 10.0.0.0/24
RHOSTS => 10.0.0.0/24
msf  auxiliary(lantronix_discover) > run

[+] Found 10.0.0.15 - Lantronix Device type: UDS-10/Cobox 4.X
[+] Found 10.0.0.112 - Lantronix Device type: XPort-03/04
[*] Scanned 256 of 256 hosts (100% complete)
[*] Auxiliary module execution completed
```

Update - 01/30/2013
-------------------
Looks like some others stumbled across lantronix and there are metasploit modules!  One extracts the telnet password, and one grabs the version from a telnet banner.

- https://github.com/rapid7/metasploit-framework/blob/master/modules/auxiliary/scanner/telnet/lantronix_telnet_version.rb   
- https://github.com/rapid7/metasploit-framework/blob/master/modules/auxiliary/scanner/telnet/lantronix_telnet_password.rb

[1]: http://en.wikipedia.org/wiki/RS-232 "RS-232 protocol"
[2]: http://www.lantronix.com "Lantronix"
[3]: ftp://ftp.lantronix.com/priv/cobox_tools/setup.zip "Lantronix 30178/UDP functions"
[4]: http://www.metasploit.com/
[5]: https://github.com/robvinson/metasploit-modules "my public metasploit-module repository"
