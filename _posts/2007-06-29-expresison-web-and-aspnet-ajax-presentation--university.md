---
layout: post
title: Expresison Web and ASP.NET AJAX Presentation @ University
date: 2007-06-29 04:17:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Last Monday I did a 2 hour nearly demo-only session at the technical university in Vienna to show some of the nitty-gritty details of ASP.NET AJAX. It was a more detailled version (especially in demos on the AJAX stuff) of the portal I created at our last Big Days road show. As the task was building a web site from scratch I started building this site with Expression Web designer and then completed it using Visual Studio ORCAS. Although - because still in early beta - ORCAS sometimes has some stability issues the [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/Expresison.NETAJAXPresentationUniversity_900C/image_thumb.png?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/Expresison.NETAJAXPresentationUniversity_900C/image.png?raw=true?raw=true)roundtripping between these two worlds, Expression Web and ORCAS worked seamless although ORCAS ships with a new version of the AJAX Extensions integrated into the .NET Framework 3.5.


Still the AJAX Extensions provide the same functionality as they do with the version available at [http://ajax.asp.net](http://ajax.asp.net/) - therefore I ported the web site back to work seamless with ***released-only*** technologies which aren't in beta. Attached to this posting you can find the samples and PowerPoint slides I presented at the university. The samples attached to this post can be opened with Visual Studio 2005 without any problems. Actually back-porting took me about 10 mins. incl. testing and setting up the database as a file-database using the freely available SQL Express so that you do not need to attach it to your SQL Server.


Required software to run the sample therefore is:


* Windows with [.NET Framework 2.0](http://www.microsoft.com/downloads/details.aspx?FamilyID=0856eacb-4362-4b0d-8edd-aab15c5e04f5&DisplayLang=en)
* ASP.NET [AJAX Extensions](http://www.microsoft.com/downloads/details.aspx?FamilyID=ca9d90fa-e8c9-42e3-aa19-08e2c027f5d6&displaylang=en) and [Toolkit](http://www.codeplex.com/AtlasControlToolkit/Release/ProjectReleases.aspx?ReleaseId=4923)
* Free [SQL Server 2005 Express Edition](http://www.microsoft.com/downloads/details.aspx?FamilyID=220549b5-0b07-4448-8848-dcc397514b41&DisplayLang=en)
* Free [Visual Studio 2005 Web Developer Express Edition](http://msdn.microsoft.com/vstudio/express/web/) with integrated development web server


Basically that's it (I know, developers from Microsoft field know these link above, but the presentation was given to primarily non-Microsoft focussed developers, so I spend some time summarizing the requirements more detailled:)).


You can [download the presentation and samples here...](http://mszcool.members.winisp.net/Demos/062007_AJAXUniVienna/UniAJAX.zip)


