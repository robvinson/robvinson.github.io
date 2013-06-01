---
layout: post
title: "Ubiquitous Web Server"
date: 2012-05-18 19:30
comments: true
categories: [security, scada]
---

##Web servers on everything...

Vendors put web servers on damn near **everything**.  You can find some pretty interesting stuff out there running web servers if you just start looking for it.  Serial to TCP/IP adapters, cameras, HVAC stuff... folks plug all kinds of these kinds of things into IP networks given the chance.  Often enough, these devices have a web server running that you can visit to configure the device, monitor its status, etc.

##Sometimes they have passwords set

Sometimes these sorts of devices have the ability to set a password on them.  If that's the case, there's a good chance whoever installed the device didn't bother to change the password from the default.  Someone can just search for the manual/documentation for the device to find out if there are default passwords for it.  The installers probably think, "Why bother setting a decent password on this thing?  I'll just forget it.  There's no sensitive information stored on this thing anyway, it just supports some instrument."

Analogy time:  I don't keep my life savings in my car.  That doesn't mean I'm cool with someone opening my car door, getting in, and screwing around with it.  Also, I'm not inclined to leave my car parked in a bad neighborhood for too long.  I need my car; I depend on it.

##Here comes the rocket science

Put a decent password on your network connected stuff!  Also, you probably don't really need to plug it into that accessible network in the first place.  Give it a private IP if you can, or segregate it into a network for devices of its kind.

##People make mistakes sometimes

Yeah, people make mistakes, some don't know any better, and others are hopeless.

##Here's a trick

1. Do a port scan across a range of IPs to find open web services (port 80/tcp)

``` bash Discover web servers
    nmap -PN -p80 -T4 --max-retries 1 10.0.0.0/16 -oG port80scan
    grep '80/open' port80scan.gnmap | awk '{print $2}' > port-80-open-ips.txt
```

2. Use wget to pull down the index page for each IP address and store those in files. ([download dl.sh](https://gist.github.com/2723040))

``` bash Download the pages https://gist.github.com/2723040 (download.sh) 
    ./dl.sh port-80-open-open-ips.txt
```

3. grep across the files for things that could be interesting

``` bash Find goodies
    grep -i 'serial' 80open/*
```
