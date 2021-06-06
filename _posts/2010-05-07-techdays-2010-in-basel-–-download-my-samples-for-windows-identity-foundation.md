---
layout: post
title: TechDays 2010 in Basel – Download my Samples for Windows Identity Foundation
date: 2010-05-07 00:37:39
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Well, I know, it’s a few weeks ago, already when we had [TechDays Switzerland](http://www.microsoft.com/switzerland/techdays) in Basel. Finally now I was able to publish the material of the two sessions I delivered there:

 * Designing and Implementing Claims-Based Security-Solutions with WIF   
- [Presentation for Download](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Presentations/20100406-TechDays2010-Wif-Session-Presentation.pdf)   
- [Code Samples for Download](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Presentations/20100406-TechDays2010-Wif-Session-Demo.zip)   
- [Video of my presentation](http://www.microsoft.com/showcase/de/Ch/details/9a7d7ea9-be07-4eb8-aa65-3af14909913f)
* Understanding and using Windows Azure AppFabric   
Will follow-up in a separate post on this session;)

 In this post I’ll explain, **what you need to do to run the Windows Identity Foundation samples** I’ve created for TechDays. I’ll post a description on the Windows Azure AppFabric samples in a separate post!

 You will notice, that for both sessions I used the same startup-example, a little shop-scenario that consists of two web applications (a shop front-end and a payment management front-end as well as a payment-backend implemented as WCF web service):

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_9.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_20.png?raw=true?raw=true) 

 During the session on Windows Identity Foundation I modified this starting-point to let a security-token-service do the authentication of users and the issuing of tickets for authorization. With that we implemented a single sign-on across the two web applications as well as a cleaner and more flexible way of authorization through claims:

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_10.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_22.png?raw=true?raw=true) 

 Below you find the necessary steps for setting up and running these demos on your machine. **Please follow these steps exactly to make sure all the X.509-certificates as well as the database are setup correctly!**

 1. **Prerequisites**   
Before we start you should make sure to have all the pre-requisites installed:   
- [Visual Studio 2010 RTM](http://www.microsoft.com/vstudio) (trial works, as well)   
- [SQL Server Express Edition](http://www.microsoft.com/express/Database/)   
- [Windows Identity Foundation SDK](http://msdn.microsoft.com/en-us/security/aa570351.aspx)   
- [Windows Azure Tools for Visual Studio and the SDK v1.1](http://go.microsoft.com/fwlink/?LinkID=128752)   
- [Windows Azure AppFabric SDK v1.0](http://go.microsoft.com/fwlink/?LinkID=129448)   

2. **Make sure you have an Application Pool called “ASP.NET v4.0” in your IIS 7.0   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_11.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_24.png?raw=true?raw=true)**
3. **Setup the databases and certificates for the secure web service**   
I’ve written a little script that installs the database and the certificates on your local machine. Just open a command prompt *as administrator* and execute the script **SetupBasics.cmd** to install these on your local machine. Please note, that this script installs the database on your SQL Server Express Edition Instance (local)\SQLEXPRESS. **If you don’t have SQL Server Express Edition installed, then modify the SetupBasics.cmd-script to point to your own SQL Server instance!   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_6.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_14.png?raw=true?raw=true)**
4. **Enable SSL on your IIS-instance**   
As the security-token-service for the WIF-based solution requires SSL, you need to make sure you have SSL enabled with a self-signed certificate on your local IIS. Follow these steps for doing so:   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_2.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_6.png?raw=true?raw=true)    
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_1.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_4.png?raw=true?raw=true)   

5. **Setup the STS-project as IIS application**   
In the sub-folder **WifSolution** of the downloaded ZIP-package with my samples, publish the directory **.\WifSolution\MszTechDaysSts** as IIS-application <https://localhost/TechDaysSts> – **and don’t forget to enable SSL on this site!! This application needs to run in the ASP.NET v4.0 application pool (look at the screen shots).   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_12.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_26.png?raw=true?raw=true)   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_4.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_10.png?raw=true?raw=true)   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_5.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_12.png?raw=true?raw=true)**
6. **If you get this error – set the permissions for IIS to access the files of the STS**[**![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_7.png?raw=true?raw=true "image")**](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_16.png?raw=true?raw=true)[**![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_8.png?raw=true?raw=true "image")**](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_18.png?raw=true?raw=true)   
Simply add read access for everyone to the directory with the files containing the STS to make the app running.   

7. **If you have installed IIS 7.0 AFTER Visual Studio 2010 / .NET Framework 4.0…**   
…you need to enable ASP.NET 4.0 on your web server by running “aspnet\_regiis -i” in an elevated command prompt window.   

8. **Update <microsoft.identityModel> configuration in web.config**   
Next you need to open the web.config-file of the MszTechDaysShop and MszTechDaysShopAccount projects to update the URLs for your own Security-Token-Service. It should point to your local machine (local IIS) where you run the STS. Don’t forget to really use the machine-name instead of local host because the self-signed SSL certificate gets issued to the machine-name of your local dev-machine.   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_28.png?raw=true?raw=true)   

9. **Update the <system.serviceModel> sections in web.config**   
Next you need to update the system.serviceModel configuration in the web.config of the two web applications MszTechDaysShop and MszTechDaysShopAccount to point to the right STS for authenticating against the web service:   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_3.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_30.png?raw=true?raw=true)   
[![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_thumb_13.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010inBaselDownloadmySamplesforW_F066/image_32.png?raw=true?raw=true)   

10. **Next open the solution “.\WifSolution\MszTechDaysShop\MszTechDaysShop.sln” in Visual Studio 2010 (run Visual Studio as Administrator)**   
After you’ve completed these steps you can run the demo through Visual Studio 2010. Don’t forget to run Visual Studio 2010 **as Administrator** if you want to debug the STS hosted in IIS. When running, make sure you first start the MszTechDays.Services.PaymentApp project and then start the others. Use the following credentials to sign in into the STS:   
- user name = mszcool   
- password = pass@word1   
Or simply create your own credentials using ASP.NET WebAdmin when the STS-project is highlighted!

