---
layout: post
title: Azure Demo Solution - Cloud Computing Day 2008 Last Week with David Chappell
date: 2008-12-16 12:29:18
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Right immediately after my previous post the next one is following;) Last week on Friday, December 12th, we had a great event together with David Chappell in our new conference center right above our M.I.C. in Vienna.

 At this cloud computing day, David gave a great insight on the Cloud Computing market in general and, of course, especially on Microsoft's offerings around the Windows Azure Services platform. David did a great job in talking about the different flavors of platforms that are available on the market from a variety of vendors (especially Microsoft, Amazon, Google and Salesforce.com). Also David was great in explaining the core business and architectural concepts of our own cloud offerings around Azure.

 In the second part of the conference day I had the pleasure to once again step up and do a deep-dive coding session (although I shouldn't as an architect... but I still love getting my hands dirty if the topic and the prototype is cool and interesting;)). You can download the demo I presented by clicking the link below. I'd recommend to further read this posting to get an understanding of what I did;)

  So I decided to implement a complete scenario to demonstrate some of the core concepts of the Azure Services platform (not touching other parts of the platform such as .NET services, SQL Services or Live Serivces;)). With the scenario I tried to show the following concepts:

 * Web Roles and Worker Roles and how-to use them.
* Queue-based storage and how-to leverage queues for asynchronous communication between web- and worker-roles.
* Finally the blob-storage as a means for storing binary data similar to file-systems in on-premise operating systems.

 The scenario I decided to implement was a simple shop-application as you can see in the following graphics. The web-role was supposed to be the front-facing part of the shop while the worker-role was intended to process orders submitted through the shop in an asynchronous way. As a simple but nice example for order-processing I decided to use the **Office Open XML APIs** to generate a Microsoft Office Word 2007-based order-document that gets stored in the blob-storage and made accessible through the web role afterwards.

 [![](http://zawq9w.bay.livefilestore.com/y1pjm4S46C8Ed9gocxp04_sfDR_mL5jW2wWvJ4c3qO7T0fzaeSf5DwbqR4ziTfPt5Qgiwmj7a4yqwI/ScenarioOverview.jpg)](http://zawq9w.bay.livefilestore.com/y1pjm4S46C8Ed9gocxp04_sfDR_mL5jW2wWvJ4c3qO7T0fzaeSf5DwbqR4ziTfPt5Qgiwmj7a4yqwI/ScenarioOverview.jpg) 

 During the 90 min. demo I started building a simple ASP.NET application with no special things. I used the ASP.NET Session-object for storing my shopping based just for the sake of having a simple start-up for the demo-session without any new concepts. We then even deployed the session on the Azure online platform to demonstrate the admin-UI the current CTP is offering. We did that at the beginning as currently the deployment and configuration-update takes some time on the CTP (about 20 min. ... but unfortunately I then forgot to show the running application).

 
>  **Hint about a question I got during the presentation on ASP.NET Session-State:** of course the ASP.NET session state isn't persistent and therefore available across multiple instances of a web-role. For this purpose you would need to write your own session-state provider that leverages Azure-Table-Store or Blob-Store for getting persistent storage on sessions. There is actually an example of how-to do this for the session provider and even for other providers of ASP.NET such as Membership or Roles API!!! So that's the way of dealing with persistent sessions in Azure:) Hope that answered the question now:)
> 
>  

 In the next step we created the Check-Out page that XML-serialized the contents of my Session-based shopping basked (which was a simple list of Product-items) and put it into an Azure Queue-Storage to make this stuff available for worker role instances that asynchronously processed the order (generated documents with Office Open XML APIs).

 
>  **One question here was about scaling up worker-roles because I used "waiting loops" that where waiting for incoming messages on the loop with thread-sleeps of 10sec.**. Of course that wasn't a very scalable approach, I tried to keep it simple. For scaling Worker Roles you have several but in my opinion very well-known approaches. Let me give you just a view of them: spawning threads whenever a new message was received or creating and running multiple instances of worker roles are ways for scaling out at this layer.
> 
>  **Then this brought up a second question - how is it about locking messages that are accessed from the Queue if multiple Worker Role instances (or threads) are running?** And well, the answer is simpler than I thought (I didn't know that at the presentation). Actually the queue has mechanisms to avoid "parallel" access to the same message by multiple worker role instances. When you issue a GET-operation onto the queue, it makes (a) makes sure that the sender of the first GET-message that arrives for a message exclusively gets access to the message (we do not need to deal with that) and (b) you can add a HTTP header to your request where you can specify, for how long a message on the queue will be invisible to other requests for retrieving the message. The default-value here is 30 sec. and the max. value is 2 hours in the current CTP. If you just want to access the messages without making them invisible you can use a PEEK instead of a GET-request to the queue.
> 
>  **Afterwards the last question was whether there is a way for a worker role to be notified if a new message is available at the queue instead of querying the queue continuously as we did it in our example!** Well, not really without using the .NET service bus and WCF here (I haven't found anything on this)... but the reason is pretty obvious for me, as well. Every storage-concept of the Azure services platform is accessed through REST-style APIs. That means whenever you want to get something from the storage you need to execute an HTTP request against the URL of your storage and that's it. For example, putting a message to a Queue is an HTTP post request with the XML-payload in the body to the Queue's URL. Retrieving a message from the queue and blocking it for a specified period of time is an HTTP GET request to the queue (the default-time is 30 sec. as outlined before) and retrieving a message without blocking it is an HTTP request with a custom verb (PEEK). So it always needs to be a pull-based access instead of being notified. Of course with a combination of using worker roles and WCF-services in the service bus you can "mimick" the behavior of being notified. I know this might be a weakness, but don't forget that we are (a) in an very early stage of the Azure platform and that (b) there are some more sophisticated aspects that allow you implementing more complex communication patterns such as the .NET service bus available which we didn't use at all in our demo.
> 
>  

 In the next step we implemented the logic of the worker role that generated the document. We then finally stored the document in the Azure blob-storage which is a kind of a replacement of a file-system as we know it from on-premise operating systems. From this storage we accessed the documents in the last ASPX-page we created back in our worker role...

 Well, and after that I was pretty tired because it was a long, hard and risky demo with these CTP-bits... but I am happy that anything worked out and I hope that you finally liked the presentation and the contents of this posting where I tried to also answer some of the questions I got during the demo-walk-through. ***For me it was great fun doing this in any case - it was a great day with David before lunch and it was so cool walking through this demo which I prepared until about 1 a.m. the evening before... cool stuff and I really believe in this kind of cloud-computing for many scenarios**...*

 If you want to start working with these things, just try to request your access tokens (will take some time) through the following resources:

 [Azure Services Invitations](https://connect.microsoft.com/site/sitehome.aspx?SiteID=681 "Azure Services Invitations") on the Microsoft Connect platform (connect.microsoft.com) as mentioned during my session...

 [http://lx.azure.microsoft.com/](http://lx.azure.microsoft.com/ "http://lx.azure.microsoft.com/") which is the official entry point for the Windows Azure serivces platform management console.

 [https://www.microsoft.com/azure/default.mspx](https://www.microsoft.com/azure/default.mspx "https://www.microsoft.com/azure/default.mspx") which is Microsoft's official entry-point to the Azure services platform and information on the Azure services platform.

 If you have any further feel free commenting on this blog post or contacting me through this blog here;) I'd be happy about any feedback from you:)

 Cheers   
**Mario**


