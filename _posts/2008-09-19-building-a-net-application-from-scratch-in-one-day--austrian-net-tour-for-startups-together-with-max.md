---
layout: post
title: Building a .NET Application from Scratch in One Day - Austrian .NET Tour for Start-Ups together with Max
date: 2008-09-19 10:27:49
categories: msdnblogarchive
tags: Archive MSDNBlog
---

This week we started a road show through Austria for developers which are rather new to the world of .NET that want to see and learn, how modern applications will be developed with the .NET Framework.

 [You can download all the content of our tour from Max' blog...](http://blogs.msdn.com/knom/archive/2008/09/19/net-roadshow-demo-solution.aspx)

 At each location we will stay for a full day of code and content and we develop an application from scratch. That means - while we've prepared tons of slides - Max and I are walking just through a few of the most important slides to cover theory and then start building an application for managing events with the following bits and pieces:

 * Data access layer with the ADO.NET Entity Framework.
* Business Logic Interfaces and separated Implementation Class Libraries.
* Simple WCF service interfaces for publishing our logic as interoperable web services.
* An ASP.NET web front-end that is used by end-users to find and register for events.
* A Silverlight UI that is embedded into our ASP.NET Web Front End to have some cool experience.
* And finally a WPF Application that re-uses the XAML from the Silverlight-Control to build a fully fledged desktop client.

 While we had to make some architectural drawbacks (of course, I mean we have just one day for the whole implementation... e.g. we created rather RPC-style interfaces instead of message-based interfaces to keep things more simple;)), I think the application really demonstrates, how you can build an application that follows a simple 3-tier architecture splitting Data from BL and UI into autonomous units. But the picture below captures what we are building within one day nearly from scratch:

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/8b2.NETRoadShowforStartUpstogetherwithMa_E54E/image_thumb_1.png?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/8b2.NETRoadShowforStartUpstogetherwithMa_E54E/image_4.png?raw=true?raw=true) 

 Here are some screen-shots about the application. Material is available for download on Max' blog;)

 Down there you see the data base diagram. We thought we won't do something that is too simple and "constructed" for the real world. So our database even has "is-a" constructs (inheritance) through the EventDelivery, OfflineEventDelivery and OnlineEventDelivery entities:

 [![dbdiagramm](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/8b2.NETRoadShowforStartUpstogetherwithMa_E54E/dbdiagramm_thumb.jpg?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/8b2.NETRoadShowforStartUpstogetherwithMa_E54E/dbdiagramm.jpg?raw=true?raw=true)

 We use these to demonstrate the inheritance capabilities of the ADO.NET Entity Framework in our business layer of the application. The business layer of course is exposed through interoperabile, [WS-I compliant](http://www.ws-i.org) web services. Maybe I will post a Java-Client for our Event Solution somewhere around Christmas-time when I have completed many of my currently running engagements:)

 Just that you see the application in action as well, below is a screen shot of the web front-end that is using our web service layer in between for accessing the business logic and the data for our event management system:

 [![screenshot1](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/8b2.NETRoadShowforStartUpstogetherwithMa_E54E/screenshot1_thumb.jpg?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/8b2.NETRoadShowforStartUpstogetherwithMa_E54E/screenshot1.jpg?raw=true?raw=true) 

 What you see above is an ASP.NET Master Page with a TreeView control filled with data from a SiteMapDataSource. In the main area the events are displayed - and that rather 'complex' list is something we've implemented with Silverlight 2.0 Beta 2. And now the interesting part comes. Below you see a WPF application that is using - of course - the same web services... and ... much better... the same XAML-ListBox-UI that we've created for the web application above (it's just styled in a different way through resources - we even haven't had to touch the XAML - we just required some compiler pre-processors to avoid the desktop-compiler to try to compile Silverlight-specific stuff):

 [![screenshot2](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/8b2.NETRoadShowforStartUpstogetherwithMa_E54E/screenshot2_thumb.jpg?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/8b2.NETRoadShowforStartUpstogetherwithMa_E54E/screenshot2.jpg?raw=true?raw=true)

 For me, the fact that we can share assets between Silverlight (and therefore - the Web) and Desktop applications is crucial as it really helps saving development- and design-time for specific parts of the application. This is really cool and a real advantage compared to developing parts of the UI two times, one time for the web and one time for the desktop app if you need to support both.

 Okay, these are some of my thoughts on the application that we've developed for our road show. [As mentioned before, just navigate to Max' blog if you want to download the complete solution here.](http://blogs.msdn.com/knom/archive/2008/09/19/net-roadshow-demo-solution.aspx)

 Finally I just wanted to mention that it is great touring with Max through the country delivering the show and know-how to the developer community, again;)

 Cheers   
**Mario**


