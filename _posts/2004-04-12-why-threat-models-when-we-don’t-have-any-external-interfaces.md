---
layout: post
title: “Why Threat Models when we don’t have any external interfaces?”
date: 2004-04-12 17:41:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

In my opinion that is a common misbelief that can lead to grave consequences!xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


 




Last week I had an intense discussion with some hard core developers asked me for doing some design support according to the “security functionality” (what ever this is – for the moment) of their application. As we are working forward for a [*Developer Security Roadshow* through](http://www.dotnetexperts.at/default.aspx?cid=5477a5a2-87f9-4b36-8554-5bb98bdc54e5) xml:namespace prefix = st1 ns = "urn:schemas-microsoft-com:office:smarttags" /?[Austria](http://www.dotnetexperts.at/default.aspx?cid=5477a5a2-87f9-4b36-8554-5bb98bdc54e5) at the moment I have been concerned with Threat Analysis and Threat Modeling for a while – therefore I thought “great, to identify possible threats I’ll do some threat modeling with them”.


 




So I gave them a quick course in threat modeling and immediately afterwards we started with app decomposition (I decided doing it with DFD). After that I have identified some trust boundaries of the application (where some data will be imported and exported). My idea was to start with threat modeling at exactly those trust boundaries.


 




But even before I was able to identify some threats they asked me, what we are doing here. Their expectance was talking about users and roles and permissions and they told me: “we don’t have any external interfaces – nothing of our application goes beyond the company (which was not right – we identified true external interfaces via app decomposition!), so why are we doing that? Can’t we spend some time on doing a class and database design for our user and role system?”


 




Is that a good solution? Ignore possible threats and start designing an object model before knowing what is necessary? Of course threat modeling does not solve all the problems but it helps you identify problems – even if you never thought about them before. 


 




Even if you don’t have external interfaces, app decomposition and threat modeling is – in my opinion – essential for creating a secure system. Oh yeah, not only external interfaces are dangerous! Go forward and identify *trust boundaries* and spots that potentially will be attacked. If you don’t have external interfaces in your application and everything “stays in the same company”, can you be sure, that all employees of this company are angels? The world isn’t nice at all – perhaps one of the guys in the middle of them will take advantage of weaknesses.


 




So we continued with app decomposition and – what a surprise – we even identified some external interfaces (not known as external interfaces before). With all the threats and potential points of attack in mind we started designing the overall architecture as well as core components for the user / role and permission sub system! And as a by product we identified some design- and coding guidelines for the development of the whole thing.


 




Also I recommended reading [Michael Howard’s](http://blogs.msdn.com/michael_howard) “*Writing Secure Code (2nd Edition)”* which was my starting point for learning how to design and develop secure software.


