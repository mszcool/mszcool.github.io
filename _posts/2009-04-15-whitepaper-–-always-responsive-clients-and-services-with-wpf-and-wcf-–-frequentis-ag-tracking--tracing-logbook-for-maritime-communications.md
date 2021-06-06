---
layout: post
title: Whitepaper – Always Responsive Clients and Services with WPF and WCF – Frequentis AG Tracking & Tracing Logbook for Maritime Communications
date: 2009-04-15 09:45:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

I am delighted to **publish a whitepaper** of one of the most interesting engagements I’ve been part of so far – **together with** [**Frequentis AG**](http://www.frequentis.com/).


Together with the architects from Frequentis, **Ulrich Hüttinger** and **Stefan Domnanovits**, we’ve been writing this whitepaper I am publishing now with this blog-entry. In this paper you can read about some of the most important (not all!!) architectural approaches and design decisions Frequentis made for building always responsive clients and services in the mission critical area of ship-vessel traffic management.




| [Download the paper by clicking here…](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/2009-04-alwaysresponsiveapps.pdf)

 |


Frequentis is building the newest applications in this area with **.NET Framework 3.5 SP1**, primarily **Windows Presentation Foundation (WPF)** and **Windows Communication Foundation (WCF)**.


[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/WhitepaperAlwaysResponsiveClientsandServ_D52C/image_thumb.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/WhitepaperAlwaysResponsiveClientsandServ_D52C/image_2.png?raw=true?raw=true) 


Topics we’ll cover in this paper are:


* Understanding the environment of the tracking and tracing solution and its technical requirements.
* Discussing some decisions Frequentis had to make on their message bus infrastructure based on these requirements.
* Implementing a reliable message bus infrastructure for smaller havens/ports and large havens/ports with WCF at the same time.
* Putting job-, queue- and command-patterns together for always responsive applications on clients and in services.
* Combining patterns such as the presentation model pattern on the front-end in WPF and understanding the communication-flow between the presentation model and the business logic that uses queues, commands and jobs on the backend.


I hope you find this paper interesting and the information in it useful!


**Mario**


