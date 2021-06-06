---
layout: post
title: TechDays 2010 – Windows Azure AppFabric Session – Download a Sample – From all On-Premise to partial Cloud partial on-premise…
date: 2010-05-09 12:03:07
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Im [my last posting](http://blogs.msdn.com/mszcool/archive/2010/05/07/techdays-2010-in-basel-download-my-samples-for-windows-identity-foundation.aspx) I published the first set of downloads on my sessions at [TechDays 2010 in Switzerland, Basel](http://www.microsoft.com/switzerland/techdays). Basically I delivered two sessions at the conference:

 * Designing and implementing Claims-Based Security-Solutions with WIF   
see [my last posting](http://blogs.msdn.com/mszcool/archive/2010/05/07/techdays-2010-in-basel-download-my-samples-for-windows-identity-foundation.aspx) for all the details.
* Understanding and using Windows Azure AppFabric   
- [Presentation for Download](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Presentations/20100406-TechDays2010-AzureAppFabric-Session-Presentation.pdf)   
- [Code Samples for Download](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Presentations/20100407-TechDays2010-AzureAppFabric-Session-Demo.zip)   
- [Video of my presentation](http://www.microsoft.com/showcase/de/Ch/details/4d0fe867-87ca-4eea-8835-ed8a4fd12a48)

 In this post I’ll explain, **what I did in my session at TechDays and why.** As it’s going to be a little longer, I’ll create a separate post for explaining, how to setup the complete demo-environment. In this posting you’ll learn about a scenario where using Azure AppFabric as a means for integration cloud and on-premise solutions makes perfectly sense.

 Again I used the same sample as I did for the WIF-session at TechDays, I just modified it in a different way during the session (okay, that was for saving time:)). The original scenario is made up with two web shop applications that are calling a payment web service as follows:

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010WindowsAzureAppFabricSession_9BB7/image_thumb.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010WindowsAzureAppFabricSession_9BB7/image_2.png?raw=true?raw=true) 

 In the original solution everything runs on-premise. When thinking about the cloud it’s obvious what I want to do:

 * **The shop-applications should run in the cloud!**   
They can benefit from the elastic capabilities and the scale-out as well as pay-per-use from the cloud in a perfect sense. At the same time these shop-applications do not really store sensitive data.
* **The payment-service should stay on-premise!**   
Why? Because it stores highly sensitive data where I don’t know if I am allowed to store this data outside of our country – payment-information of our customers. Therefore I want to keep it in our own data centers to keep control over this sensitive data.

 **But – the shop needs to call the payment service.** Nevertheless the shop-frontends running in the cloud need to be able to call the payment-service without any hurdles. Of course you can do this by opening up firewall ports, registering names, dealing with proxies and network address translation and all that stuff. But with AppFabric you can do this in a different way, and it has advantages in my opinion. The modified architecture after the session looked as follows:

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010WindowsAzureAppFabricSession_9BB7/image_thumb_1.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/TechDays2010WindowsAzureAppFabricSession_9BB7/image_4.png?raw=true?raw=true) 

 The Windows Azure AppFabric and its **Service Bus** component act as a communication relay between the service consumer and the service provider. Both are connecting from inside-out of their data center to the AppFabric. All access to the Service Bus itself and between the involved service providers and consumers is secured by a component called **Access Control Service.**

 The ACS (access control service) allows you to do fine-grained, claims-based authorization based on standards ([OAuth WRAP](http://wiki.oauth.net/OAuth-WRAP) at the moment, later it will be extended with a full-blown STS using WS-* and SAML).

 Of course you could argue, I can open up the firewall ports, make sure the service is addressable and make sure we don’t run into proxy-issues. But the AppFabric approach has many advantages in my opinion:

 * You can decide on a service-by-service level whether to share or not.
* You can implement fine-grained authorization rules on a service-by-service level managed through access control.
* You can integrate single-sign-on for involved service parties by integrating the AppFabric access control service with [ADFS v2.0](http://www.microsoft.com/geneva).

 An there are many more to think about, these are just the ones dropped into my brain while writing this article here.

 The coolest AHA-effect for me came up while preparation of this stuff:) I was sitting in my hotel room in Basel connected to the hotel-Wireless LAN running a web service on my notebook (the one of this sample) and calling it from the cloud through all this “hurdles” (NAT, proxy etc.). I am, that’s crazy, that’s so cool – imagine what you’d need to do to accomplish that without Azure AppFabric…

 Stay tuned for a subsequent post where I will provide step-by-step instructions to setup my demo-environment.


