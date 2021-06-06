---
layout: post
title: DevCamp 2009 – My Session on Always Responsive Applications and Services covering .NET 4.0 Task Parallel Library and CCR with Connection to a Real-World Scenario
date: 2009-10-15 02:57:02
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Maybe some of you have read the Whitepaper I’ve written together with two architects from Frequentis AG (Ulrich Hüttinger and Stefan Domnanovits) a few months ago. At the [DevCamp 2009 Conference](http://www.devcamp.at/) I baked that into a session now!

 [Download the paper by clicking here if you haven’t read it, yet.](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/2009-04-alwaysresponsiveapps.pdf)

 Finally after giving a presentation at TechReay, Microsoft’s internal conference for technical and strategic education, I managed to give a “fine-tuned” version of the session for the first time in Europe – at our local [Developer Camp 2009 conference](http://www.devcamp.at/) organized by key-partners of Microsoft in Austria.

 If you’re interested in seeing the concepts of the paper in action using **.NET 4.0 Task Parallel Library**, then check out the presentation material and the **running demo solution**.

 [Download the Demo and Presentation here!!](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/2009-11-AsyncTaskScenario.zip)

 The demo solution isn’t an easy thing, it’s really a little prototype to show the job/queue/command pattern using Task parallel library in an end-2-end-fashion:

 * client doing his tasks with Jobs in the background to be always responsive
* little service bus example sitting in the middle coordinating the communication between all working positions and a few back end services.
* back end services leveraging the “job-pattern” as well for asynchronous and scalable processing of incoming requests.

 Note that the sample also includes a CCR-based implementation. If you don’t have CCR/DSS installed on your machine, just comment-out the CcrManager-class in the JobLibrary-assembly.

 As promised I will provide the back-port to Visual Studio 2008 containing just the CCR-based implementation so that those who don’t have Visual Studio 2010, yet, will have a chance to take a look at the sample, as well!

 If you have any questions, feel free asking me!!!


