---
layout: post
title: Architecture Journal - Article about Federated Identity in the Health Care
date: 2008-09-01 12:08:59
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Last week, just one week after my vacation, I had it in my inbox - the announcement that **[Issue 16 of the Architecture Journal](http://msdn.microsoft.com/en-us/arcjournal/cc836389.aspx)** about Identity and Access Management has been published.

 In this issue of the journal you will find [an article I've written about federated identity architectures](http://msdn.microsoft.com/en-us/arcjournal/cc836394.aspx) in health care. This article is about a project that I have been involved in within the whole last winter-season and where [Emmanuel Mesas](http://blogs.msdn.com/emesas/default.aspx) from the Microsoft Innovation Center and I helped the Austrian Medical Association in Austria building a prototype. With that project and this prototype my extreme interest in the [Identity Meta System Vision](http://msdn.microsoft.com/en-us/library/ms996422.aspx) was born. Since then I really believe in the concepts of [Kim Cameron's](http://www.identityblog.com) vision.

 A brief history: a little more than a year ago the Austrian Medical Association asked us for helping them in providing additional concepts to the Electronic Health Care Records system which was planned in Austria. The primary challenge there was, that the health care environment is (a) a politically complex environment and (b) has extreme security-requirements due to the strong data protection law in Austria.

 While having several Architectural Design Sessions and finally a prototyping session with the Microsoft Innovation Center Copenhagen we came up with a solution which really leveraged some primary concepts of the Identity Meta System vision. 

 **The primary benefit - separation of concerns in the identity world**

 When taking a look at the concepts of the identity meta system it really proposes a clear separation of roles:

 * The **identity provider (IP)** is responsible for issuing, validating and proofing the validity of identities. By proofing statements about an identity the provider can definitely influence authorization - but he is not doing all the authorization by himself.
* Authorization is done by the **relying party (RP)**. The RP finally accepts identity tokens with statements about an identity proofed by the IP. Other than in classic architectures where each application authenticates and authorizes a user at their facade layer (such as a web service that authenticates each and every request), the application (which is the RP in this scenario) accepts a digitally signed security token from an IP as proof for an identity and just does the authorization and business logic part.
* And of course finally we have the user who can be authenticated by an IP while accessing an RP through a variety of mechanisms.

 This separation of concerns really allowed us to deal with the technically and politically heterogeneous and complex environment. Also due the flexible support of different standards such as SAML, Kerberos or X.509 it is much easier to build a security environment in heterogeneous environments that include .NET, Java or any other technology you could imagine. If you are interested in more details, [read on here](http://msdn.microsoft.com/en-us/arcjournal/cc836394.aspx).

 Btw. Cardspace was not part of the project, we were just talking about Security Token Services and Claims-based Web Services and Clients in the scope of this system.

 **My Personal Thoughts**

 This project really helped me understanding the primary advantages of claims-based and federated identity concepts. Since that I strongly... strongly believe in the Identity Meta System as a whole!! I've also given several presentations during the past 12 months on that topic... and more presentations will follow!

 Currently I am working on a presentation on using federated and claims-based security in heterogeneous environments including building a bridge between .NET and Java using DigitalMe and Eclipse Higgins... we'll see what's coming out there;)

 In any case I recommend reading the last journal issue and, of course, my article about how we leveraged the identity meta system's concept to support the Austrian Medical Association and the Austrian Electronic Health Care Records system for the future about a year ago:)

 [Click here to read the article;)](http://msdn.microsoft.com/en-us/arcjournal/cc836394.aspx)


