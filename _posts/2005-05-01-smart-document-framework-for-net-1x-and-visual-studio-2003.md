---
layout: post
title: Smart Document Framework for .NET 1.x and Visual Studio 2003
date: 2005-05-01 23:59:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

This week I have published my Smart Document Framework (download [here](http://www.gotdotnet.com/Community/UserSamples/Details.aspx?SampleGuid=94197d33-82a3-4912-88d1-3bf0f783c5e2)) as User Sample on [www.gotdotnet.com](http://www.gotdotnet.com). I have created this framework for a customer to simplify development of Smart Docs with VS 2003 and VSTO 2003. Okay, it's shortly before Visual Studio 2005 ships but I have created that for a customer who wants to go into production this summer (that's the case for our customer), and I think I would not want to roll out beta software to hundrets of clients (although the build is really very stable...). The framework brings the paradigm of controls to the ISmartDocument interface and adds a couple of things for faster and easier development like


* Per-Document instance state management  
Actually Word and Excel create just one instance of an ISmartDocument implementation per launched process. That means if you start a document template multiple times on the same machine, only one instance of your ISmartDocument class is created. Well that's cool for saving resources (yeah :-) on today's machines...) but if you keep state in your ISmartDocument as simple members the state is shared across all opened documents. The infrastructure includes state-management down to the level of control properties.
* Composition of Smart Documents  
If you have already created one Smart Document and want to reuse it's schema as well as UI rendering code in another Smart Document you can do that with the framework as I have split up the implementation of ISmartDocument and the actual UI logic code into two separate parts - a DocumentHandler and a Controller.


Although I think the framework makes development of Smart Docs with Visual Studio 2003 much easier I clearly have to state that developing such solutions is much more than just writing some code for creating a couple of user interfaces. It's much more important to have the different contextual situations for the users in mind and design the schema documents appropriately. I'll post more on that later but for the while you can take a look at the framework here: <http://www.gotdotnet.com/Community/UserSamples/Details.aspx?SampleGuid=94197d33-82a3-4912-88d1-3bf0f783c5e2>


Oh yeah, just one more statement: of course you can use the framework for free but I'd suggest if you don't need to go into production this calendar-year start developing the solutions with Visual Studio 2005 Tools For Office. They are much better in terms of designer support and supported controls (as full-blown Windows Forms Controls are supported now). ... but ... the right schema design is still the most important thing there as well.


