---
layout: post
title: TechEd Developers 2006 in Barcelona - My second session, "In-Depth Level 400 Demo in InfoPath Forms Services and SharePoint Workflow"
date: 2006-11-08 08:31:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Yesterday I had the hardest session I have ever done at an international conference - a 75 min. demo-only session on developing a solution based on InfoPath, InfoPath Forms Services, Windows SharePoint Services and Windows Workflow Foundation. Actually the interest in that session was so high that it is going to be repeated on Thursday after lunch.



> 
> 
> 
> 
> 
> 
> | **OFF001 DEMO: Designing and Implementing a Solution with SharePoint 2007, Windows Workflow Foundation and InfoPath 2007** |
> | [Mario Szpuszta](https://www.mseventseurope.com/Online/Registered/speaker.aspx?id=21754)  |
> | Tue Nov 7 14:15 - 15:30 Room 111 [![Add to My Event Calendar](https://www.mseventseurope.com/Online/images/AddToSchedule.gif "Add to My Event Calendar")](javascript:WebForm_DoPostBackWithOptions(new WebForm_PostBackOptions("ctl00$ContentBody$DataGrid1$ctl04$Repeater4$ctl00$Linkbutton2", "", true, "", "", false, true)) "Add to My Event Calendar") [![Add to your personal Outlook calendar](https://www.mseventseurope.com/Online/images/AddToPersonal.gif "Add to your personal Outlook calendar")](https://www.mseventseurope.com/Online/Registered/vcsdownload.aspx?Type=ScheduleItem&ID=5709) , ***Fri Nov 10 13:30 - 14:45 Room 123*** [![Add to My Event Calendar](https://www.mseventseurope.com/Online/images/AddToSchedule.gif "Add to My Event Calendar")](javascript:WebForm_DoPostBackWithOptions(new WebForm_PostBackOptions("ctl00$ContentBody$DataGrid1$ctl04$Repeater4$ctl02$Linkbutton2", "", true, "", "", false, true)) "Add to My Event Calendar") [![Add to your personal Outlook calendar](https://www.mseventseurope.com/Online/images/AddToPersonal.gif "Add to your personal Outlook calendar")](https://www.mseventseurope.com/Online/Registered/vcsdownload.aspx?Type=ScheduleItem&ID=7266) |
> 
> 


As promised you can find the demos from my session with all the InfoPath forms and Visual Studio 2005 projects including the custom activity within the ZIP file attached to this blog post. If you want to create your own Workflow-solution from scratch, I'd recommend taking a close look at the following articles and resources on MSDN:


* [Developer Introduction to Workflows for Windows SharePoint Services v3 and SharePoint Server 2007](http://msdn2.microsoft.com/en-us/library/ms406057.aspx)
* [Introduction to Workflows in Windows SharePoint Services](http://msdn2.microsoft.com/en-us/library/ms434426.aspx)
+ Especially [understanding the Workflow Stages](http://msdn2.microsoft.com/en-us/library/ms479274.aspx) is really essential when it comes to SharePoint Workflows

* [Walkthrough - Creating Office SharePoint Server 2007 Workflows with Visual Studio 2005](http://msdn2.microsoft.com/en-us/library/ms564355.aspx)  
This really is a step-by-step guide for creating workflows with Visual Studio 2005 for Windows SharePoint Services and SharePoint Server 2007
+ [Step 1 - Creating the Workflow](http://msdn2.microsoft.com/en-us/library/ms580283.aspx)
+ [Step 2 - Creating the Initiation Form](http://msdn2.microsoft.com/en-us/library/ms585588.aspx)
+ [Step 3 - Creating a Task Form](http://msdn2.microsoft.com/en-us/library/ms497641.aspx)
+ [Step 4 - How-To Deploy Workflow Templates](http://msdn2.microsoft.com/en-us/library/ms460303.aspx)
+ [Step 5 - How-To Debug Workflow Templates](http://msdn2.microsoft.com/en-us/library/ms455354.aspx)

* Especially interesting are the articles around Workflow forms and InfoPath:
+ [Association Forms](http://msdn2.microsoft.com/en-us/library/ms563325.aspx)
+ [Instantiation Forms](http://msdn2.microsoft.com/en-us/library/ms550177.aspx)
+ [Task Forms](http://msdn2.microsoft.com/en-us/library/ms550938.aspx)
+ [How-to Design a Form for Association and Instantiation](http://msdn2.microsoft.com/en-us/library/ms515036.aspx)
+ [How-to Design a Workflow Task Form to use Task Data](http://msdn2.microsoft.com/en-us/library/ms550782.aspx)


Finally the attendees were interested in what they need to install on their machines to get the whole thing running. On my machine I had the following components installed in exactly the order I have written them right here:


* Basic Infrastructure
+ Windows Server 2003 R2 (but Windows Server 2003 with SP1 is okay, as well)
+ .NET Framework 2.0
+ [.NET Framework 3.0 Runtime Components](http://www.microsoft.com/downloads/details.aspx?FamilyId=10CC340B-F857-4A14-83F5-25634C3BF043&displaylang=en)
+ SQL Server 2005 including Service Pack 1
+ Northwind Sample Database

* Development Environment
+ Visual Studio 2005
+ [Windows Vista SDK](http://www.microsoft.com/downloads/details.aspx?FamilyId=C2B1E300-F358-4523-B479-F53D234CDCCF&displaylang=en)
+ [Visual Studio 2005 Extensions for ORCAS](http://www.microsoft.com/downloads/details.aspx?FamilyID=f54f5537-cc86-4bf5-ae44-f5a1e805680d&DisplayLang=en)
+ [Visual Studio 2005 Extensions for Windows Workflow Foundation](http://www.microsoft.com/downloads/details.aspx?FamilyId=5D61409E-1FA3-48CF-8023-E8F38E709BA6&displaylang=en)

* 2007 Microsoft Office System
+ Microsoft Office SharePoint Server 2007 (incl. Windows SharePoint Services v3)
+ Project Templates for SharePoint Workflow (see my [previous blog-post](http://blogs.msdn.com/mszcool/archive/2006/11/08/teched-developers-barcelona-sharepoint-workflow-project-templates.aspx))
+ Microsoft Office 2007 Professional


Finally that's it. I have attached all the demo code to this blog post. As I have used a build very close to the final release, I suppose that you don't need to change anything anymore for running the demos on the final release of SharePoint 2007. Have much fun... I hope together with this blog post the session was really useful to you and you enjoyed it.


[TechEdNow.zip](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Components.PostAttachments/00/01/03/51/30/TechEdNow.zip?raw=true)


