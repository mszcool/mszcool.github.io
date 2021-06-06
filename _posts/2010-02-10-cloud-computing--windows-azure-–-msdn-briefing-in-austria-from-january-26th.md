---
layout: post
title: Cloud Computing & Windows Azure – MSDN Briefing in Austria from January 26th
date: 2010-02-10 02:14:37
categories: msdnblogarchive
tags: Archive MSDNBlog
---

On January 26th I presented at our MSDN-Briefing on **Cloud Computing** and **Windows Azure**. During the session I introduced our (and my) point-of-view on Cloud Computing. Within the demos I migrated my good old [MicroBlog sample](http://blogs.msdn.com/mszcool/archive/2009/06/16/technical-university-of-vienna-presentation-from-last-week-building-a-micro-blog-engine-with-asp-net-3-5-and-asp-net-ajax.aspx) I created for a presentation at the technical university of Vienna last year to run on Windows Azure.

 Downloads: [presentation here](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/MSDN%20Briefing/20100126-MsdnBriefingAzure.pdf), [demo here](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/MSDN%20Briefing/20100126-MsdnBriefingAzure.zip)

 [Codefest.at posting incl. links to recordings (German, only)](http://www.codefest.at/post/2010/02/10/Cloud-Computing-Tutorial-10-e28093-MSDN-Briefing-vom-2612010.aspx)

 Here are the little challenges I had for the migration outlined (please note, that this is just a simple sample – for bigger apps you definitely will run in some more issues – Rainer Stropek and I will talk about these in our Session at BigDays 2010 in March):

 1. MicroBlog was a Web Site project – Azure requires Web Application Projects. So I had to convert the project. For doing so, follow these steps:
1. Create a new Web Application Project in the same solution as you have your Web Site Project.
2. Copy all Contents from the Web Site Project to the Web Application Project.
3. Right-click the web application project and select “Convert to Web Application Project” from the context menu.
4. Fix remaining compile errors or other errors (namespace changes etc.)

3. The ASP.NET Membership API and Roles API scripts and aspnet\_regsql both do not work on SQL Azure because they’re using features not supported by SQL Azure. Fortunately Microsoft released a fixed set of scripts and a tool called aspnet\_regsqlazure to solve this problem and make Membership API and Roles API available for SQL Azure:
1. <http://code.msdn.microsoft.com/KB2006191>

 The remaining parts of the application worked pretty well, of course I extended it during the briefing to use Blob- and Table-Storage. But more on that hopefully in later posts.


