---
layout: post
title: Cloud computing contest with Windows Azure – The architecture and technology of our Austrian contest application
date: 2010-04-14 05:33:23
categories: msdnblogarchive
tags: Archive MSDNBlog
---

As many of you might know, in Austria we are currently running a **Windows Azure Contest** where it’s possible to win a **Samsung flatscreen TV together with an XBox 360** as well as **10 external 500GB hard disks** for all participants successfully completing the contest.

 In this blog-posting I will describe the technical architecture and provide the source-code as a download so that you can take a look at the details of this nice “in-production”-application on Windows Azure!

 Contest home: <http://atazurecontest.cloudapp.net>   
Official announcement: [official announcement here](http://www.codefest.at/post/2010/03/31/Contest-Cloud-Computing-mit-Windows-Azure-e28093-Samsung-HD-Fernseher-und-Xbox-360-gewinnen.aspx)   
Azure Contest App Source Code: [Azure Contest App](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Various/201004%20Azure%20Contest/Microsoft%20Azure%20Contest%20Application.zip)   
Working Participant Sample App Source Code: [Participant Test App](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Various/201004%20Azure%20Contest/Participant%20Test%20Code%20Azure%20Contest.zip)

 **How does the contest work?**

 To participate in the drawing for the flat-screen and the Xbox 360, you basically need to [register at our contest home page](http://atazurecontest.cloudapp.net/) (if you’re in Austria, only, see [qualification requirements](http://atazurecontest.cloudapp.net/Default.aspx?linkId=Details)), then write and deploy a simple web service on Windows Azure and finally let our Azure-application try to call your web service. Graphically that looks as follows (click for details):

 [![Scenario%20-%20Overview[1]](http://public.bay.livefilestore.com/y1pNmo_Nnmg7R7nNommfm6YF-yrOvsPLgDlzPBMMgI5oxnhml4PFl1I996nFl-Uose1qjH8Wg-7zwJTTwo10UXS5g/Scenario%20-%20Overview.PNG "Scenario%20-%20Overview[1]")](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Various/201004%20Azure%20Contest/Scenario%20-%20Overview.PNG) 

  **Architecture of the Microsoft Windows Azure Contest App** 

 The contest-front-end you see when navigating to <http://atazurecontest.cloudapp.net> is just a little part in the overall game. Although the code has rather the state of a sample, the architecture of the system is asynchronous by its nature to scale out a bit better. Therefore the front-end is really just accepting and storing registrations and displaying some simple web pages – not more not less. Sending emails and testing participant applications happens asynchronously in a worker as shown in the illustration below.

 [![Architecture%20-%20Overview[1]](http://public.bay.livefilestore.com/y1pvt3EX962iAiLlTYAtlJUm-eJba2J75JtGgA2w3JfQerOmdZ5fOkkn55pEgCA6v263aa84mEktmKIj6vGW-fDJg/Architecture%20-%20Overview.PNG "Architecture%20-%20Overview[1]")](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Various/201004%20Azure%20Contest/Architecture%20-%20Overview.PNG) 

 As you can see above, the web role accepts the requests through simple ASP.NET pages from the user. These are the following:

 * Users can register for participation.   
With that the application generates a registration token (a GUID) and stores the registration data in a Windows Azure table storage so that we can query the data, easily. Behind the scenes the following actions happen:   
-) a registration-token is generated   
-) the registration data is stored in table storage   
-) a message is added to the queue with the token and a command for the worker   
-) the worker takes the token, reads registration data and uses it to send an email.
* Users can test their registration.   
Every test requires the user to enter the registration token – not more and not less. With this information the following actions happen behind the scenes:   
-) the web role tries to read the registration data   
-) if valid, it adds a message with the token and the validation-command to the queue   
-) the worker picks this message up and reads registration data based on the token   
-) it tries to call the service according to the token and the registration data   
-) it updates the registration data after the call succeeded or failed appropriately

 In the web role, all the actions are delivered through an AJAX-web service. So you won’t find any code in the pages except JavaScript-calls to this AJAX-web service. If you are interested in how messages are added to the queue or data is added to the table storage, the AJAX-web service contains this code as well as the code for the worker role does.

 Also note the solution I’ve implemented for the content pages. I knew that our marketing department definitely will want to change some parts of the textual and graphical content of the application. These are independent of the actual application logic and application pages and therefore I’ve separated them out as stand-alone HTML pages. All these pages are stored in blob storage and whenever our marketing requires an update, I just update those simple HTML pages and upload them in blog-storage again instead of re-deploying the whole application. This is a pretty neat approach for this situation to be able to make simple textual and graphical changes quickly without the need of re-deploying the whole application (mini-mini-mini-mini CMS functionality;)).

 I think the application is a pretty nice sample for leveraging the different parts of Windows Azure. Feel free downloading the source code for the contest application as well as a example of the participant-test-application and setup your own environment if you want to try something more interesting than just a hello world application:)

 [Download the Microsoft Austria Azure Contest Application Source Code here](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Various/201004%20Azure%20Contest/Microsoft%20Azure%20Contest%20Application.zip)

 [Download a working and ready-to-deploy sample participant application web service here](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Various/201004%20Azure%20Contest/Participant%20Test%20Code%20Azure%20Contest.zip)

 For me it was definitely great fun building this content application, I hope it’s funny for you implementing and deploying the participant application web service, as well! Feel free sharing any feedback with me!


