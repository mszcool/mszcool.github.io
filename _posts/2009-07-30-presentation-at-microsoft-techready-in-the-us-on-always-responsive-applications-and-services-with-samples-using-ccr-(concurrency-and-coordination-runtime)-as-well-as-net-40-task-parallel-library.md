---
layout: post
title: Presentation at Microsoft TechReady in the US on Always Responsive Applications and Services with samples using CCR (Concurrency and Coordination Runtime) as well as .NET 4.0 Task Parallel Library
date: 2009-07-30 19:00:40
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Today in the morning I gave a presentation at Microsoft’s largest internal conference for employees in Seattle, WA (called TechReady, about 5000-6000 Micorsoft employees are there on technical education).

 The presentation I gave is essentially based on the whitepaper I’ve written and we’ve published a few weeks ago together with Frequenits AG on always responsive and scalable apps and services. You can find **[more details as well as the paper for download here](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/2009-04-alwaysresponsiveapps.pdf)**.

 While the presentation is strictly confidential, I can publish the demo scenarios. Therefore click the link below if you are interested in a complete scenario that shows asynchronous processing within clients and services as well as across services… of course I do not cover all possible “exceptions”, but it’s a starting point.

 [**Click here to download the demo**](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/2009-11-AsyncTaskScenario.zip)   
(for Visual Studio 2010 Beta 1, only, I will provide a VS 2008 version with CCR-only implementations soon)

 The demo scenario supports a few arguments discussed in my whitepaper as well as the presentation:

 * Performance comparison between Peer-2-Peer and Service-bus based communication metaphors.
* Base classes for implementing the Command/Job/Queue patterns discussed in the whitepaper.
* And finally – a mapping of these patterns to .NET Framework technologies that definitely help implementing the patterns themselves. I include two implementations, one that uses the **Concurrency and Coordination Runtime** from the Microsoft Robotics Studio and another one that uses the **.NET Framework Task Parallel Library** that we are going to publish with the .NET Framework 4.0. To switch between those two implementations, just modify the JobManagerFactory in the AsyncDemo.JobLibrary project to use one or the other implementation.

 The project with Frequentis definitely showed me, that Asynchronous programming and thinking is not just for the sake of performance, it’s also for “responsiveness” and “availability”. The neat thing is, that simply by keeping a few things in mind, these things can go hand-in-hand. 

 Nevertheless, it was very special for me delivering this session at TechReady. Seven years ago, right before I started working for Microsoft in October 2002, Seattle was the place where I attended the first Microsoft conference of my life (and the first conference in the US, at all). And it was in the very same location as TechReady this year – in the Washington State and Convention Center, in the Sheraton Hotel and Hyatt Hotel in Seattle.

 My session was in one of the Grand Ball Rooms in the Sheraton with about 100 attendees… and back in August 2002 I had my room in the Sheraton at the Windows .NET Server 2003 conference… at that time I would have never thought that I will hold a session in the same location at any time:) So this was special for me! And I hope it was not for the last time!


