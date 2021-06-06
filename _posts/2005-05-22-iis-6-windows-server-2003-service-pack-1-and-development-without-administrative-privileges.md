---
layout: post
title: IIS 6, Windows Server 2003 Service Pack 1 and Development without Administrative Privileges
date: 2005-05-22 22:38:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Wow, that's a long title;-).


As you probably know I am still developing without administrative privileges on my Windows systems. Even with Visual Studio 2005 Beta 2 it works very well (with knowing a couple of tricks like configuring the Administrative Tools Shortcuts with “Run As”). But since I have installed ***Windows Server 2003 Service Pack 1*** I was not able creating Web Projects as non-admin anymore although I have set the permissions according to the [MSDN article](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dv_vstechart/html/tchDevelopingSoftwareInVisualStudioNETWithNon-AdministrativePrivileges.asp). While Visual Studio (2003 and 2005) is able sharing a directory as web directory it cannot configure it as application anymore – therefore I had to do that manually. But know I have found out how (as expected it's one of the Web Servers COM components security configuration) - to solve this issue and get back to the original comfort in combination with staying secure as least-privileged user. All you need to do is the following configuration (not more and not less):xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


* Open Administrative Tools – Component Services
* Navigate to My Computer – DCOM Config – IIS WAMREG admin service
* Open the Properties of IIS WAMREG admin service and switch to the security tab.
* Configure Security for Launch and Activation Permissions so that your non-admin developer user has full control for launching this application.


With those two settings you have the necessary permission for calling the DCOM component (IIS WAMREG admin service) which is responsible for configuring IIS.


That’s it...


