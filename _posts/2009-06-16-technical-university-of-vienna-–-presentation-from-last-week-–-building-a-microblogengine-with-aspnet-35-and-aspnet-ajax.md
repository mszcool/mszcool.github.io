---
layout: post
title: Technical University of Vienna – Presentation from last Week – Building a Micro-Blog-Engine with ASP.NET 3.5 and ASP.NET AJAX
date: 2009-06-16 14:23:11
categories: msdnblogarchive
tags: Archive MSDNBlog
---

While being here in Redmond with customers at our Lead Enterprise Architect Conference I realized that I still owe students from a lecture I gave last week the presentation and demo-downloads. Therefore I am catching up with this now;)

 During the lecture I introduced ASP.NET foundational concepts such as the architecture of the runtime (Modules, Handlers...), Server Controls and the Web Forms programming model as well as ASP.NET AJAX and the latest release of the AJAX control toolkit.

 [![mszCools MicroBlog Demo - Windows Internet Explorer](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechnicalUniversityofViennaPresentationf_11356/mszCools%2520MicroBlog%2520Demo%2520-%2520Windows%2520Internet%2520Explorer_thumb.png?raw=true?raw=true "mszCools MicroBlog Demo - Windows Internet Explorer")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechnicalUniversityofViennaPresentationf_11356/mszCools%2520MicroBlog%2520Demo%2520-%2520Windows%2520Internet%2520Explorer_2.png?raw=true?raw=true) 

 You can download the presentation and the source code for this little sample application from the link below. For successfully testing the application you need the following bits installed on your machine:

 * [SQL Server 2008 (or 2005) Express Edition](http://www.microsoft.com/express/sql/register/)
* [Visual Web Developer Express Edition](http://www.microsoft.com/express/download)
* [ASP.NET AJAX Control Toolkit (open source)](http://ajaxcontroltoolkit.codeplex.com/Release/ProjectReleases.aspx?ReleaseId=27326)

 That’s essentially it, after you’ve these running you need to execute the included database script (Database.sql creates the tables, only, Database\_withData.sql creates the tables with some sample data), modify the connection strings in the web.config file to point to your SQL Server instance and database. If you have SQL Server Express Edition installed you can leave all settings in your web.config as they are because SQL Server Express Edition by default installs with the “(local)\SQLEXPRESS” instance name.

 [Click here to download the source code and presentation!](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/2009-07-MicroBlogDemo.zip)

 Have much fun, if you have any questions feel free getting in touch with me through this blog!

 **Mario**


