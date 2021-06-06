---
layout: post
title: CAB/SCSF and Workflow Integration - Demo Update from last TechEd
date: 2008-06-04 14:51:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Attached you can find an updated version of the demos [I've presented at TechEd Europe 2007](http://blogs.msdn.com/mszcool/archive/2007/11/07/teched-2007-tla406-downloads-cab-solution-patterns.aspx). I figured out that under some circumstances the downloaded demo does not really work and so I included some fixes as in the following bullet-list.


**[You can download the updated version here!!!](http://mszcool.members.winisp.net/Demos/112007_TechEdDevelopers/AdvancedCabDemo.zip)**


**Important Note: You need to have SQL Server Compact Edition 2005 (version 3.1) installed on your machine. Visual Studio 2008 ships with version 3.5 instead of 3.1 and the database file formats are incompatible. [So download the SQL CE 2005 (v3.1) runtime here...](http://www.microsoft.com/downloads/details.aspx?FamilyID=85e0c3ce-3fa1-453a-8ce9-af6ca20946c3&DisplayLang=en)**


* Fixed some bugs in the Workflow integration
* Added the required Backend Web Services (NorthwindServices) to the solution.
* Added the data base required by the Northwind Services in the App\_Data directory - you need to have SQLEXPRESS on your machine, then the web services "just work".
* Updated the Web Service URLs in the Shell's app.config configuration to point to the localhost integrated web server that runs the Northwind Services
* Updated the SQL Server Compact Database for the Offline Disconnected Agent request queue. Unfortunately the database was corrupted in my original download - I am very sorry about that.


Well - after so long time being quiet on my blog you might ask why I am updating the demo right now. Well, first -i've got enought feedback now to do so:)) and second we've applied the concepts in a customer project and I am publishing an architecture whitepaper about that, soon, where I refer to this demo. So a working version would be a good starting point. Take a look at the blog attachment and give me feedback if you are not able to get the stuff running...


Cheers  
**Mario**


