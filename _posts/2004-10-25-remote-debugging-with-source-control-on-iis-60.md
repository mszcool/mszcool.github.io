---
layout: post
title: Remote Debugging with Source Control on IIS 6.0
date: 2004-10-25 15:44:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

As last week I got a question about how we can configure remote debugging on a Windows Server 2003 running with IIS 6.0 together with source control if Visual SourceSafe cannot work together with FrontPage Server extensions I thought… just try it.

  

 Simply let’s start with the first point: how can we perform remote debugging from a Windows XP Service Pack 2 (as non-admin) on a Windows Server 2003.

  

 **The Windows XP SP2 Developer Machine (client)xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?**

  

 My client machine is a Windows XP SP2 with the Windows Firewall enabled. I am not working as administrator on this machine. For enabling remote debugging on my machine I have to perform some configurations on the firewall. More exactly for remote debugging some ports must be opened on the client developer machine. In my exception list I have configured the following ports:

  



 * xml:namespace prefix = st1 ns = "urn:schemas-microsoft-com:office:smarttags" /?Remote Debugging  
Port = 135, Protocol = TCP, Scope = My Network (subnet only)
* Remote Debugging IP Security  
Port = 4500, Protocol = UDP, Scope = My Network (subnet only)
* Remote Debuggin IP Security (2)  
Port = 500, Protocol = UDP, Scope = My Network (subnet only)

  Furthermore don’t forget to add the Visual Studio .NET development environment (devenv.exe) to the exception list (but I think that’s what every developer is doing the first time when using Visual Studio .NET after installed SP2J). As I have seen afterwards there is a tool available for enabling remote debugging on XP SP2 - see link list below.

  

 **The Windows Server 2003 Server Machine**

  



 First of all for simplicity I have installed a full Visual Studio .NET on the server. But in general it is sufficient to just install the Remote Debugging components either through the setup wizard or manually (see links below). 

  



 I have tried very long finding a practical way for project teams of a size larger than 2 developers for doing the whole thing not as administrator on the server. But to be honest, the way described in the corresponding [article on MSDN](http://support.microsoft.com/default.aspx?scid=kb;en-us;817265) works for a couple of users (I am working similar on my developer machine with Windows Server 2003 as I am not developing as administrator) or for a hand full of users but not for a large project team. Therefore I decided to investigate in that more but leave the configuration so that any developer is administrator on the “development team web server” (but not on the development machine; and of course this should be an isolated web server provided to the development team, only, that has no relationship to any production environment or the outside world).

  



 So currently the only way how I did it was adding me to the administrators group of the remote server. With that in place you can create an ASP.NET web project by specifying an URL like the following: <http://remotecomputer/webdirectory> and start development and debugging.

  



 **Source Control, FrontPage Server Extensions and Remote Debugging**

  



 Remote debugging requires FrontPage Server Extensions enabled. But when working with FrontPage Server Extensions on the server, Visual Source Safe stops working for the corresponding project or solution.

  



 But there is a possibility for continue working with the Visual Source Safe add-in when using FrontPage server extensions. The only restriction that you have is that adding the project to source control must be done through FrontPage Server Extensions console (or web on IIS 6.0 and Windows Server 2003).

  



 For that purpose you have to install Visual Source Safe on your server machine. Afterwards configure Visual Source Safe to automatically connect to your source control database (which might reside on the same or another machine). In any case all developer users must be configured separately in your Source Safe database (all developers with their user name only, not in the format <DOMAIN>\<USERNAME> but only in the format <USERNAME>) using Visual Source Safe Admin.

  



 As soon as your Source Safe instance is configured properly you can configure webs using the SharePoint Administrator from the administrative tools. Open the SharePoint administrator, select the Default Web Site. Then create a sub web for your web applications. If you already have created an ASP.NET web application on the server fire up the IIS management console, right click the ASP.NET virtual directory and select “All Tasks – Configure Server Extensions 2002” for creating a sub web. As soon as you have done that you can configure Front Page Server Extensions for your sub web. When opening the SharePoint Administrator again from Administrative Tools and selecting the Default Web Site the new sub-web should appear at the bottom of the page.

  



 In the configuration for the sub web you can select the “Configure Version Control”. In that option select “Use external version control” and specify the Source Safe path in your source control database that should be the root for your project. When opening the remote project next time using Visual Studio .NET you have all the Source Control options in place – check-in, check-out as when managing the project from the local machine.

  



 **Interesting links for configuring remote debugging:**

  



 * Setup Remote Debugging – general article  
<http://support.microsoft.com/kb/318041/EN-US/> 
* Setting up remote debugging on MSDN  
<http://msdn.microsoft.com/library/default.asp?url=/library/en-us/vsdebug/html/vctskinstallingremotedebugmonitor.asp> 
* Minimum Permissions Required for remote debugging  
<http://support.microsoft.com/default.aspx?scid=kb;en-us;329282> 
* Remote Debugging and Windows XP SP2  
<http://support.microsoft.com/default.aspx?scid=kb;en-us;833977> 
* Debug Firewall Utility for Windows XP SP2  
<http://support.microsoft.com/default.aspx?scid=kb;en-us;841177> 
* Multi-User Debugging on IIS 6.0 (in my opinion for small teams, only)  
<http://support.microsoft.com/default.aspx?scid=kb;en-us;817265> 
* FrontPage Server Extensions and Source Control  
**Warning:** if you are using IIS 6.0 the configuration is not done through IIS MMC but through the “Administrative Tools – Microsoft SharePoint Administrator” web administration tool!!  
<http://msdn.microsoft.com/library/default.asp?url=/library/en-us/sccvs70/html/veurfaddingfrontpagewebapplicationstosourcecontrol.asp> 
* If the external option is not available in FrontPage Server Extensions  
<http://support.microsoft.com/default.aspx?scid=kb;EN-US;q296715> 

 Hope this is useful for some of you out there...

 

 
