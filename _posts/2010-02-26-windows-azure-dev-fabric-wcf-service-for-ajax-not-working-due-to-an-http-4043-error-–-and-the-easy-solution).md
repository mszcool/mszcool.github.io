---
layout: post
title: Windows Azure Dev Fabric, WCF Service for AJAX not working due to an HTTP 404.3 Error – and the easy solution;)
date: 2010-02-26 06:33:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Recently at my home machine I ran into a strange issue while developing an application for our Windows Azure winning game with the development fabric.


I added a **WCF AJAX Service** and tried to use it. Everything compiled fine and when running the ASP.NET web application in the local ASP.NET development web server anything worked. Then I created a Azure Cloud Service and added the working web application as a web role to the project.


I then tried to debug it and suddenly the AJAX types were not recognized anymore in my client-side JavaScript as shown in the following screen-shot.


 [![image01[1]](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/WindowsAzureWCFServiceforAJAXandH.3Error_D6E6/image01%5B1%5D_thumb.jpg?raw=true?raw=true "image01[1]")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/WindowsAzureWCFServiceforAJAXandH.3Error_D6E6/image01%5B1%5D.jpg?raw=true?raw=true) 


When I tried navigate to my AjaxService.svc hosted in the Azure development fabric in a browser, I got an error as shown in the following image – **it was an HTTP 404.3 telling me, that the requested page cannot be found or maybe the MIME-type was not registered.** Strange error message anyway.


[![image02[1]](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/WindowsAzureWCFServiceforAJAXandH.3Error_D6E6/image02%5B1%5D_thumb.jpg?raw=true?raw=true "image02[1]")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/WindowsAzureWCFServiceforAJAXandH.3Error_D6E6/image02%5B1%5D.jpg?raw=true?raw=true) 


The solution – **Installing the WCF HTTP and TCP activation through the control panel as shown in the following screen-shot**. I know it’s documented:) I just forgot about because anything for web development except these two options where installed on my home-machine, already… 


Therefore I thought it’s useful posting this hint.


[![image03[1]](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/WindowsAzureWCFServiceforAJAXandH.3Error_D6E6/image03%5B1%5D_thumb.jpg?raw=true?raw=true "image03[1]")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/WindowsAzureWCFServiceforAJAXandH.3Error_D6E6/image03%5B1%5D.jpg?raw=true?raw=true) 


The installation of these two components within the control panel solved the whole problem. So if you run into the same problem, check first, if these are installed;)


Hope that helps…


