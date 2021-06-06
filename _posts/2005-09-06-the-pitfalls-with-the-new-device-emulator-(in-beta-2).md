---
layout: post
title: The Pitfalls with the new Device Emulator (in Beta 2)
date: 2005-09-06 20:23:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Today (or better tonight) I have prepared a Virtual PC with a huge amount of demos for customer briefings. Of course I had to do one devices demo as well. So far so good. The new device emulator even works within Virtual PC ... great improvement ... but ... if you are not connected to a network (or better to a DHCP server) you have to complete the following steps:


* Go to Visual Studio 2005 Device Configuration and configure an IP address for the pocket PC device in the TCP Transport options, e.g. 192.168.0.50.
* Then open the emulator (try to connect). It will still fail. What you have to do now is selecting one of the network cards of your device in the emulator in the connections configuration and assign an IP address.
* Then restart the emulator and try connecting again ... it should work now.


Last but not least if you want to connect to your full-blown Windows OS (which is running in VPC) you have to configure your network configuration for the "My Work..." setting. Then this should work as well.


Hopefully this information was useful for you and saves you lots of time...


