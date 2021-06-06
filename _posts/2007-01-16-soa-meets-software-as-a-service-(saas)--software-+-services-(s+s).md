---
layout: post
title: SOA meets Software as a Service (SaaS) / Software + Services (S+S)
date: 2007-01-16 07:19:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

While researching and preparing for the upcoming [18. Architect Forum on January 24th next week on SaaS](http://blogs.msdn.com/msdnat/archive/2007/01/11/jpfpodcast.aspx "18. Architect Forum") I have made an interesting discovery, that really shows, how successful a company can be betting on SaaS and how they are providing business services in a clean, service oriented fashion to their customer through [Phil Wainewright's blog-posting](http://blogs.zdnet.com/SAAS/?p=170 "Phil Wainewright"). My first thought was that this is a really good example showing, how Service Orientation can help building a SaaS-ready solution to catch the [long tail](http://en.wikipedia.org/wiki/Long_tail "Long Tail") (see [Gianpaolos](http://blogs.msdn.com/gianpaolo "GP") whitepaper [here](http://search.msdn.microsoft.com/search/Redirect.aspx?title=Architecture+Strategies+for+Catching+the+Long+Tail+(Building+...+&url=http://msdn.microsoft.com/architecture/saas/default.aspx?pull=/library/en-us/dnbda/html/ArchStratCtchLngTail.asp "GP Whitepaper - Long Tail")). In fact I think, for many business-scenarios, Service Orientation is an important pre-condition for building SaaS/S+S ready software (but don't get me wrong, as it is always the case with architecture, of course this has not to be true for each and every case).


***Employease - Outsourcing your HR software infrastructure***


The company I have discovered by crawling through a number of blogs is [Employease](http://www.employease.com/ "Employease"). This company provides a hosted application that an enteprise company can use for managing data of their employees. With about 1500-2000 customers this is a really nice example of leveraging an economy of scale (just think a moment, how hard it is for a traditional ISV to get 1500 customers with a standard-product if you are not one of the larger ones!). The whole HR application is hosted and operated by [Employease](http://www.employease.com/ "Employease"), an enterprise can subscribe and use the functionality. Now the interesting part is coming: usage of Employease is possible through web-based interfaces (what a surprise:)) and through [web services](http://developer.employease.com/extend.html "Employease Developer").


***A service-infrastructure even our HR-staff can understand***


Now I coming to the point where SOA meets SaaS. Clearly Employease hosts a SaaS-application based on what I outlined in the first paragraph. But now take a look at the services they are providing as web services:


[![](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/SOAmeetsSoftwareasaServiceSaaSSoftwareSe_A9AB/image%7B0%7D_thumb%5B12%5D.png?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/SOAmeetsSoftwareasaServiceSaaSSoftwareSe_A9AB/image%7B0%7D%5B24%5D.png?raw=true?raw=true) 


These are all "business services" rather than RPC-style functions. E.g. HireEmployee or TerminateEmployee are encapsulating complete business processes and notice there is no service such as "DeleteEmployee" or similar, technical things with no meaning to a business employee. I think that level of service-granularity hits the spot. The only point I have to make here is, that I am just not sure with the level of granularity for the services pointed out for example within the "Job Title" category. In my opinion they make the impression of being to fine-granular, but the point is, that *I don't have the complete picture of their business and requirements,* of course. Personally I'd rather treat the Job-title as part of a Hireing-Info or -Profile similar ot the "Personal Info" category outlined in the image above. This could be designed in a way of having services encapsulating factors such as promoting an employee, relocating an employee into another department and of course getting the current hireing-details as the following services (business capabilities):


* PromoteEmployeeToDifferentJob  
Promotes the employee into a different job with probably a different salary etc.
* PromoteEmployeesSalary  
Changes the salary-level of the employee with all related information.
* RelocateEmployeeToDepartment  
Relocates the employee into a different department affecting the current manager, the job title, the salary etc.
* GetEmployeeHiringDetails  
Of course reads all the hiring details of an employee such as department, job title, manager, salary etc.


But as mentioned earlier, ***I don't know their business requirements exactly and therefore this can just be a guess***.


In general I really like these type of interface as it uses business capabilities as a foundation for deciding which services are exposed instead of a technical approach. This is really what *in my opinion* service orientation is all about - exposing business capabilities with the right level of granularity to other organisations...


***When Business Capabilities meets SaaS***


As outlined in [Phil Wainewright's](http://blogs.zdnet.com/SAAS/?p=170 "Phil Wainewright") posting from last summer, this is really a great example for a SaaS application and through it's service interfaces at the same time it introduces a very cool example of a business-oriented service interface design for making its services available for automated business and integration.


*My personal point actually refers back to what I mentioned before (of course the following paragraphs are rather an [enterprise perspective](http://msdn2.microsoft.com/en-us/architecture/aa905332.aspx "Enterprise and SaaS") in addition to what's outlined in one of [Gianpaolo's blog entries](http://blogs.msdn.com/gianpaolo/archive/2006/08/19/707876.aspx "GP on Enterprise and SaaS") and his [third whitepaper on SaaS](http://msdn2.microsoft.com/en-us/architecture/aa905332.aspx "Enterprise and SaaS")): the design of the services is very business-capability oriented. But when you just raise the level of detail a little bit, you can treat the whole HR-application as a "composite" businesss capability - and now SaaS comes into the game. The interesting point is, that as a company you now can make the decision based on your own business capability model, which business capabilities you'd like to out-source. And these are exactly potential candidates for being out-sourced to a SaaS provider, which builds these capabilities (or buys these capabilities from an ISV) and hosts them for you. Of course then these capabilities needs to be provided as set of services so that you can integrate them into a composite application architecture within the enterprise.*


*Simplified this means for me, the decision on whether to bet on a SaaS provider or not can be built upon a set of business capabilities and the company's decision on which capabilities they try to focus on. Having a capability map in place (independant from the tool), this is just a matter of drawing a line as a border between the capabilties you'd like to host and control by your own and the other ones you just need to leverage. To illustrate that I got an older image of a capability map from a presentation done by [Arvindra](http://www.thearchitectexchange.com/asehmi/ "Arvindra") and [Beat](http://blogs.msdn.com/beatsch "Beat") at TechEd 2005, which in my opinion hits the spot:*


*[![](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/SOAmeetsSoftwareasaServiceSaaSSoftwareSe_A9AB/image%7B0%7D_thumb%5B13%5D.png?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/SOAmeetsSoftwareasaServiceSaaSSoftwareSe_A9AB/image%7B0%7D%5B27%5D.png?raw=true?raw=true)* 


*I know there are not so great tools out there for creating such maps and doing a capability-analysis is a really hard, long-term process - but in the end it's a flavour of business process analysis and therefore I think a decision on what capabilities (or processes:)) you want to host by yourself or you want to out-source to a SaaS vendor can be done based on every useful, business-oriented analysis. I just want to point out here, what a good foundation for a decision would actually be. And as business capabilities on the "border lines" are perfect candidates for services, this is for me the place, where Service Orientation meets SaaS at the first level of abstraction...*


*Btw. for an ISV a market analysis showing the most requested business capabilities by other componaies could be a base for deciding on whether to swich from an on-premise software delivery model to an SaaS based delivery model.* 


Finally there are a number of questions I am asking myself right know on the SaaS solution architecture and the services part of this solution:


* I guess they have a multi-tenant architecture for their solution. Which degree of customization have they taken and much more important, based on which factors have they decided on this degree of customization?
* Dealing with multi-tenant data models is not rocket-science anymore. But what's about multi-tenancy on the following layers - how do they deal with multi-tenancy there??
+ Business components and business rules (okay, rule engines seem to solve parts of this obviously)
+ Especially the facade layer is an interesting part: what if a customer requires a complex data structure as an extension to an employee? How do they deal with such changes and extensions on the service facade layer without breaking interfaces on the one hand side and without running into a loosy-goosy pattern and a weakly-typed contract?

* Which tools and user interfaces do they provide for customizing tenants? And especially which concepts do they use: a more generic approach or a "generated code" approach?


While studying this topic, I am thinking more and more on the solution architecture rather than the enterprise architectural, strategic topics that are covered in most of the blogs and the articles... this is really going to be a tough challenge. If you'd like to read more, [Gianpaolo](http://blogs.msdn.com/gianpaolo "GP") has outlined a number of postings and articles he created in [his last blog entry](http://blogs.msdn.com/gianpaolo/archive/2006/12/30/my-2006-saas-posts-hit-parade.aspx "SaaS Hit Parade")...


