---
layout: post
title: JAOO 2008 in Aarhus - Downloads and Thoughts
date: 2008-10-03 12:32:48
categories: msdnblogarchive
tags: Archive MSDNBlog
---

This actually was my first year as a speaker and participant at JAOO in Aarhus... and I have to admit - it's very cool but different to any other conference I've been so far as a speaker.

 What I really like is the focus on the conceptual level... which is exactly where I know I need to do better next year. Spending too much time in code or practical examples doesn't pay off - it's really about concepts. And this is really what I like with this conference and it's sessions. It's more about enlarging your horizon and then take the new concepts you've learned about and mapping it to your local world. Also the focus on languages is something I find really interesting - especially because it's simply different to what you get usual on developer conferences.

 I really would recommend changing the slogan "from developers for developers" to a new one like "from architects and scientists for architects and developers" or so;)

 Cool conference and great learning for me in any case. I hope I will get the chance to be there next year as well.

 Furthermore I promised I will make my demos and presentations available on my blog. And here they are - just click on the links below for the downloads:

 [Click here for the presentation Material of the Office SharePoint for Enterprise 2.0 session and the ASP.NET tutorial.](http://www.mszcool.com/Blog%20Downloads/2008/10_JaooPresentations.zip)

 [Click here for the ASP.NET tutorial demos we've built during the whole day on Friday.](http://www.mszcool.com/Blog%20Downloads/2008/10_JaooAspNetTutorial.zip)

 [Finally click here for the larger ASP.NET MVC demo at the very end of the day. I fixed the problems, **updated everything to ASP.NET MVC CTP 5** (so make sure you try with this one) and tested it again.](http://www.mszcool.com/Blog%20Downloads/2008/10_JaooMVCDemo.zip)

 What are the pre-requisites for running these demos:

 * [Visual Studio 2008](http://msdn.microsoft.com/en-us/vstudio/aa700831.aspx) - I didn't try [express editions](http://www.microsoft.com/express/) but you can do. There is actually nothing that is not possible **except** the unit testing. So for ASP.NET MVC demos you need at least the professional edition of Visual Studio 2008.
* [SQL Server 2005 or 2008 (Express Editions or full editions)](http://www.microsoft.com/sqlserver/2008/en/us/default.aspx) for running the databases on your local development machine. If you run SQL Server Express Edition you must update the connection-strings to use (local)\SQLEXPRESS instead of just (local) for the data source connection string property. Also if you're running a named instance of SQL you need to update the connection strings to point to your instance. For the membership API stuff and the SQL based session you need to create the databases using aspnet\_regsql. For membership just walk through the wizard and select **JaooUsers** as database name (or update the connection strings) and for session state just execute "aspnet\_regsql -S (local) -E -ssadd -sstype p" in a Visual Studio command prompt.
* For running the demos you need to have the [Northwind database installed on your system](http://www.microsoft.com/downloads/details.aspx?FamilyID=06616212-0356-46a0-8da2-eebc53a68034&DisplayLang=en). You can find it in the database-samples-downloads for SQL Server [**here**](http://www.microsoft.com/downloads/details.aspx?FamilyID=06616212-0356-46a0-8da2-eebc53a68034&DisplayLang=en) (don't worry, these still work with SQL 2005 and 2008)!
* [Rhino Mocks](http://ayende.com/projects/rhino-mocks/downloads.aspx) for the ASP.NET MVC unit testing stuff as I use Rhino there. You can download Rhino Mocks from [**here**](http://ayende.com/projects/rhino-mocks/downloads.aspx). I used the .NET 2.0 version as I am testing nothing specific to .NET 3.5 but you can also try with the new betas they've published there.

 If you have any questions feel free contacting me through this blog - I'd be happy about any feedback!

 Cheers   
**Mario**


