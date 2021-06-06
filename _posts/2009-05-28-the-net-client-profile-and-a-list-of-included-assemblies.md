---
layout: post
title: The .NET Client Profile and a List of Included Assemblies
date: 2009-05-28 08:26:01
categories: msdnblogarchive
tags: Archive MSDNBlog
---

The .NET Client Profile is a very interesting extension made available with the release of .NET Framework 3.5. [If you’re interested into more details, follow these link to the official documentation!](http://msdn.microsoft.com/en-us/library/cc656912.aspx)

 As a subset of the full .NET Framework package containing client-side functionality, only, it should make the deployment of the .NET Framework for client-only applications in corporate networks easier (30 MB are easier to deploy, maintain and patch than the full Framework with a footprint of more than 100 MB).

 So far I was not really challenged with reasons for using the .NET Client Profile from customers – but now it has been the case three different times in series where the deployment of the full framework was a problem for client-only based applications.

 Of course one of the first questions customers are asking is: “Which assemblies are included in the client profile and are therefore available for client developers?”

 The answer: you can find a list of client-profile assemblies for each version, .NET 2.0, .NET 3.0 and .NET 3.5, in files called “Client.xml” in the following directories on your system:

 %windir%\Microsoft.NET\Framework\v2.0.50727\SubsetList\Client.xml

 %programfiles%\Reference Assemblies\Microsoft\Framework\v3.0\SubsetList\Client.xml

 %programfiles%\Reference Assemblies\Microsoft\Framework\v3.5\SubsetList\Client.xml

 Furthermore you can tell Visual Studio 2008 to check your project-references at compile-time by enabling the “Client-only Framework subset” option in the project-properties dialog. In that case Visual Studio comes up with a compiler warning for each assembly you’ve referenced that is not available in the client profile as you can see below:

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/abb.NETClientProfileListofIncludedAssemb_C94C/image_thumb_1.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/abb.NETClientProfileListofIncludedAssemb_C94C/image_4.png?raw=true?raw=true)

 To get more details on how-to deploy .NET Client Applications using the client-profile just take a closer look at this [MSDN-documentation for the .NET Client Profile](http://msdn.microsoft.com/en-us/library/cc843122.aspx)!

 Hope that’s something useful…


