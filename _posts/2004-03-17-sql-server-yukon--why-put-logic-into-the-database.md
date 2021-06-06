---
layout: post
title: SQL Server Yukon - Why put logic into the database?
date: 2004-03-17 13:04:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---


That is a follow-up blog to my last one about SQL Server Yukon - as I got some interesting feedback on it!


 


Well, it depends…! Just think in terms of classical two-tier projects with not more than 100 users where leaving (very often changing) business logic in the database would have been useful and made deployment/update issues much easier – just reduce the costs!!!!xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


 




Okay, but let’s rethink the whole story from another standpoint – *Service Oriented Architecture* and the role of fiefdoms. A fiefdom is nothing else than a participant in an SOA scenario that exposes functionality through standardized interfaces (e.g. Web Services). Each fiefdom has a data store and exposes data and functionality through its interfaces. And SQL Server Yukon will be one possible platform for implementing such fiefdoms thus it will support many things that are necessary for easily building such fiefdoms. Why implement the functionality in SQL Server – again because to reduce complexity, safe costs – all with the concept of “keep it simple!” 


 




I don’t say that we now should implement our whole fiefdom in SQL Server all the time – of course if it is necessary (necessary!!), we should still continue building n-tier distributed apps with presentation layer, business logic and data access in separate tiers. But now SQL Server offers the possibility of building back-end services and that’s the point.


 




So keep two things in mind: first of all in a SOA SQL Server is just another service while from the outside the service’s implementation is completely transparent (if accessed through Web Services no one will know, that SQL Server is running behind à loose coupling) and just be pragmatic and not always academic.


 




For more information on Service Oriented Architecture and Emissaries and Fiefdoms just take a look at the following links:


·       Pat Helland’s Blog  
<http://blogs.msdn.com/pathelland>


·       MSDN Web Cast from Pat Helland - ***Metropolis***  
<http://msdn.microsoft.com/architecture/enterprise/default.aspx> 


·       MSDN Mag Article  
<http://msdn.microsoft.com/msdnmag/issues/03/07/designpatterns/default.aspx> 


 




P.S.: To a comment on my previous SQL Server blog: don’t code your web site in SQL Server – web site coding is not business logic but it’s front end (presentation) logic and therefore not part of the core fiefdom itself and business logic.


