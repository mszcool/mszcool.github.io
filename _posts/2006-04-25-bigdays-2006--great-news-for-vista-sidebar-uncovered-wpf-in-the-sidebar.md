---
layout: post
title: Big>Days 2006 - Great News for Vista SideBar uncovered... WPF in the SideBar!!
date: 2006-04-25 12:21:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

I am currently working on the Demos for our local [Big>Days 2006 road show](http://www.microsoft.com/austria/events/bigdays) for Windows Vista and Windows Presentation Foundation. Originally the Windows Vista team claimed, that they will have no support for WPF in the SideBar. That was actually very disappointing for me.


But... the cool thing is... there is a little workaround and I already got confirmation from SideBar team, that this scenario will be supported for RTM. So finally you can create sidebar gadgets with Windows Presentation Foundation. And that's how the workaround works:


1. First of all create a [WPF Browser Application project](http://windowssdk.msdn.microsoft.com/library/default.asp?url=/library/en-us/wpf_conceptual/html/0d1b40a7-069c-4832-9764-b1c6bb8900df.asp) with Visual Studio 2005
2. Publish the WPF Browser Based application with Visual Studio 2005
3. To make sure if everything works open a browser and browse to the "*.xbap" file with your browser. This is the WPF Browser Application manifest file, which will be created when publishing the application to a web server. Even just a simple "Compile" in VS 2005 using Orcas extensions will create this file.
4. Then create a gadget using simple, plain HTML.
5. Within the HTML for the gadget you can embed the browser-based WPF application through an IFRAME as follows.
6. Finally as browser-gadgets run in a sand-box you have to deploy code access security policies. The best way for doing so is assigning a strong name to your project file and adding a policy to your machine level policies to trust based on the strong name of the WPF browser app's exe code beside.
7. That's it, you are done:)


Suppose you have the WPF browser project created in the directory of the gadget you can use the following IFRAME for embedding the gadget (unfortunately with February CTP you have to specify the full path to the WPF browser page... don't know if that's going to change with the release):


<h2>Hi there</h2><br />  
<table width="100%" border="0">  
    <tr align="center">  
    <td>  
        <iframe height="220"   
                width="140"   
                src="***%fullpathtoyourgadgetdirectory%***\TestBrowserApp.xbap" />  
    </td>  
    </tr>  
</table>


In the end I really feel much more relaxed about gadgets now. Because using WPF in your gadgets really gives you the opportunity for creating cool, useful and practical gadgets...


For more details about creating gadgets just go to <http://microsoftgadgets.com/build> and take a look at the articles posted there...


