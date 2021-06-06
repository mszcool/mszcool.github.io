---
layout: post
title: Project with the Medical Association in Austria, a Pragmatic Services Architecture with .NET 3.5 and SQL Server 2005/2008
date: 2009-05-05 14:46:50
categories: msdnblogarchive
tags: Archive MSDNBlog
---

*Hint: technical presentations about this project as download in the link list at the end of this post!!*

 This year the *Austrian medical association* together with the medical associations of the different federal states in Austria as well as one of our Gold-certified partners, *Anecon Software Design und Beratung GmbH.*, completed a project we (Microsoft Austria) started together on the country-wide management of data for medical practitioners and their ordinations.

 The management of this data is prescribed by the Austrian law and is used for several scenarios such as sponsorships of medical practitioners, promotions, payments, traceability or even for support in lawsuits and is therefore mission-critical!

 Attached to this blog-entry you will find two presentations with technical information on how we architected the solution. While the first presentation (Part1.pdf) contains shows some of the most important requirements within the environment, usage-scenarios of technologies as well as some really cool screen-shots of parts of the application, the second presentation (Part2.pdf) is based on an architectural specification I’ve written for the project on where and how-to apply which technologies of the .NET Framework 3.x in the application architecture.

 **Understanding the political and technical environment**

 Austria’s health care environment is one of the most complex political environments in Europe – and the most complex political environment in the country itself. The environment is organized in a federal way, that means each federal state of Austria (we have 9 of them) is treated as an autonomous unit. 

 Therefore each federal state has its own state-medical association with its own responsibilities and duties. Many of these responsibilities and duties are self-managed by these medical associations for a federal state, but on the other hand many of them are prescribed by a country-wide medical association which is the *Austrian medical association*.

 Having these autonomous associations’ leads to the fact that each association manages both, a common set of information on medical practitioners which is prescribed by the Austrian medical association as well as its own, additional set of information they want to and need to manage for the federal state they’re acting in. That means that the application of discussion of this web blog as well as the attached presentations need to be deployed in each federal association with their own data storage, their own service instances and client applications while on the other hand they need to synchronize the common set of data between the federal associations to be able to manage and process data on medical practitioners having ordinations in multiple federal states.

 Finally that means we are talking about federated data and federated services from a technical point-of-view with medical associations in the states within Austria as well as one overall organization which is the Austrian medical association. Technologies such as workflows for synchronization using SQL Server Service broker for data-synchronization transactions with “transformation”-rules in between are core in the application architecture.

 **The role of the medical association, Anecon and Microsoft in the project**

 Requirements as well as the underlying data model haven been defined by a working group defined by the Austrian medical association that consisted of several representatives of the different medical associations from the federal states. Our partner, Anecon, was responsible for the design, implementation and test of the overall solution based on latest Microsoft technologies. We from Microsoft acted as a trusted advisor for the Austrian Medical association: Robert John, our business development manager ensured getting the right support from the Area and Microsoft Corp. while I helped creating the overall architecture for the system together with Anecon and the representatives of the medical association.

 **Download the presentation**

 [Requirements, usage-scenarios of technologies and screen-shots from Anecon](http://www.mszcool.at/blog/2009/20090505_Austrian_Med_Association_Part1.pdf)

 [Core technical architecture recommendations from Microsoft / Mario Szpuszta](http://www.mszcool.at/blog/2009/20090505_Austrian_Med_Association_Part2.pdf)

 **Involved technologies, links and further resources**

 [.NET Framework 3.5 (incl. Service Pack 1)](http://msdn.microsoft.com/en-us/netframework/aa663326.aspx)

 [Windows Presentation Foundation](http://msdn.microsoft.com/en-us/netframework/aa663326.aspx)

 [Windows Workflow Foundation](http://msdn.microsoft.com/en-us/netframework/aa663328.aspx)

 [Windows Communication Foundation](http://msdn.microsoft.com/en-us/netframework/aa663324.aspx)

 [SQL Server 2005 and SQL Server 2008 (for newer deployments)](http://msdn.microsoft.com/en-us/sqlserver/bb671064.aspx)

 [SQL Server Service Broker](http://msdn.microsoft.com/en-us/library/ms345108.aspx)

 [Microsoft Patterns & Practices Home](http://msdn.microsoft.com/en-us/practices/default.aspx)

 [Microsoft Patterns & Practices Application Architecture Blueprint](http://www.codeplex.com/AppArchGuide)


