---
layout: post
title: PDC 2009 – In my opinion a more consistent platform story than last year… Windows AppFabric & Co
date: 2009-11-18 11:15:42
categories: msdnblogarchive
tags: Archive MSDNBlog
---

[![](http://www.mszcool.at/blog/2009/20091118_PDC1.jpg)](http://www.mszcool.at/blog/2009/20091118_PDC1.jpg) While Max is blogging on codefest.at about todays key note, where Steven Sinofsky came up with a Windows 7 Acer Laptop as a gift for every attendee, I thought it’s a good time for a short note.

 In my very personal opinion, compared to last year’s PDC Microsoft’s overall platform story is much more consistent than last year. It’s great to see, how the symmetric platform story (on-premise & cloud) is evolving and is really getting into a strong and easy-to-understand picture.

 In my opinion some of the strongest announcements during yesterday’s sessions were the following:

 [![](http://www.mszcool.at/blog/2009/20091118_PDC2.jpg)](http://www.mszcool.at/blog/2009/20091118_PDC2.jpg) * Windows AppFabric – finally our project codenamed “Dublin” has a roadmap with some cool investments and ideas. Providing a common deployment, monitoring and caching-infrastructure for WCF, WF and applications is a very good step. And in 2010 we will see AppFabric being included in Windows Azure providing the same feature-set as we have it on-premise.
* Common management possibilities for on-premise and cloud-based applications hosted in Windows Azure through System Center.
* Launch of the Windows Identity Foundation (WIF) and the fact that ADFS v2 will get into RC this calendar year and into RTM early 2010.

 I am especially conviced by Windows AppFabric as application-server bits built on-top of IIS. Windows AppFabric as a first deliverable for the on-premise world puts together things we know from “Dublin” (WCF and WF management and monitoring tools on-top of IIS 7) and Velocity (distributed caching framework).

 And further things are planned in the road map. Especially the long-term vision Microsoft presented yesterday in terms of having a common app-server infrastructure that unifies deployment, management, monitoring, caching etc. across on-premise and cloud-based applications sounds cool. **Great is the idea and the vision of bringing the concept of roles such as web roles and worker roles we know from Windows Azure into the on-premise world, as well. Therefore we then would have a fully unified model between the cloud and the on-premise world. That’s really promising…**

 Also the announcement, that BizTalk Server will integrate with AppFabric and leverage the monitoring and management functionality. That’s nice as then BizTalk will be able to leverage the integrated distributed caching features, the management features etc. Also with BizTalk 2009 R2, the team will add Visual Studio 2010 and .NET Framework 4.0 support – at least promised at PDC 2009;) We’ll see where this journey will go...

 I really hope that Microsoft is able to get these ideas on the ground fast:) At least the first bits are available, already:

 Windows AppFabric Beta 1 home page:   
[http://msdn.microsoft.com/en-us/windowsserver/ee695849.aspx](http://msdn.microsoft.com/en-us/windowsserver/ee695849.aspx "http://msdn.microsoft.com/en-us/windowsserver/ee695849.aspx")

 Windows AppFabric for the Cloud home page:   
[http://www.microsoft.com/windowsazure/developers/dotnetservices/](http://www.microsoft.com/windowsazure/developers/dotnetservices/ "http://www.microsoft.com/windowsazure/developers/dotnetservices/")

 Windows Identity Framework RTW:   
[http://www.microsoft.com/wif](http://www.microsoft.com/wif "http://www.microsoft.com/wif")

 BizTalk Server Roadmap including outlook on WCF/WF support   
[http://www.microsoft.com/biztalk/en/us/roadmap.aspx](http://www.microsoft.com/biztalk/en/us/roadmap.aspx "http://www.microsoft.com/biztalk/en/us/roadmap.aspx")   
[News on Infoworld.com about BizTalk 2009 R2](http://akamai.infoworld.com/d/applications/microsoft-align-biztalk-server-azures-appfabric-935?source=rss_cloud_computing)


