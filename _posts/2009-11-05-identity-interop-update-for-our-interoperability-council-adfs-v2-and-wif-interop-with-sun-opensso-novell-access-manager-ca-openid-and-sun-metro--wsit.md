---
layout: post
title: Identity Interop Update for our Interoperability Council ADFS v2 and WIF Interop with Sun OpenSSO, Novell Access Manager, CA, OpenID and Sun Metro / WSIT
date: 2009-11-05 05:30:10
categories: msdnblogarchive
tags: Archive MSDNBlog
---

As today we’re going to host our 3rd Microsoft Austria Interoperability Council, I thought that in addition to our existing results we’re presenting today, it’s a good time to publish an update of my **Identity Interoperability Demos and samples** I created earlier this year.

 Furthermore, **based on the feedback of members from our interop-council**, I’d like to provide a few links with more information on Identity Interoperability between Microsoft Windows Identity Framework (WIF) and Active Directory Federation Services v2 (ADFS v2- both formerly codenamed Geneva Framework and Geneva Server).

 **The Foundation – OASIS Identity Metasystem**

 Primarily the foundation for all these interoperability thoughts is the Identity Metasystem vision originally started by [Kim Cameron](http://identityblog.com/) (Microsoft – [see article on MSDN](http://msdn.microsoft.com/en-us/library/ms996422.aspx)). More information on the official standards can be found on the following link:

 [http://docs.oasis-open.org/imi/identity/v1.0/identity.html](http://docs.oasis-open.org/imi/identity/v1.0/identity.html "http://docs.oasis-open.org/imi/identity/v1.0/identity.html")

 **Sun Metro / WSIT Interoperability**

 The interop-identity PoC I’ve created demonstrates interoperability with Sun Metro / Web Services Interoperability Toolkit. For the PoC you need a Glassfish v2 application sever to host a Java-based Relying Party and integrate this RP with a .NET-based Security-Token-Service (STS) and a .NET-based client. The PoC shows, how you can integrate Java-based services in a Windows-based security-infrastructure based on standards.

 [Click here to download the lates version of my ID-Interop-PoC](http://www.mszcool.at/blog/2009/20091105_Identity_Interop_Geneva_Beta2.zip)

 [Click here to view my previous blog-entry on how-to setup the PoC](http://blogs.msdn.com/mszcool/archive/2009/01/23/identity-interoperability-geneva-beta-1-sts-with-net-client-and-netbeans-6-5-metro-1-3-wsit-java-client-working-again.aspx)

 Note, that the download package is a little big larger this time. You don’t need to download any additional bits. Everything including all Java-Pre-Requisites is included, except Microsoft Geneva Beta 2 ([click here to download](http://www.microsoft.com/downloads/details.aspx?displaylang=en&FamilyID=118c3588-9070-426a-b655-6cec0a92c10b)).

 **Interoperability with CA, Novell Access Manager, Sun Open SSO**

 While working with colleagues (Michael Steinböck, Dominik Paiha from Microsoft) on a proposal for a customer (who is also a member of the council) on identity interoperability, we’ve collected a number of additional resources on identity interoperability. First and foremost I would like to mention papers on interop between Sun Open SSO, Novell Access Manager and CA.

 [Click here to download Interop-Whitepapers for ADFS v2 (Geneva)](http://www.microsoft.com/downloads/details.aspx?displaylang=en&FamilyID=9eb1f3c7-84da-40eb-b9aa-44724c98e026#filelist)

 **OpenID Interoperability with Microsoft ADFS v2 and WIF**

 Also a question continuously asked is the interoperability between the Microsoft-platform and OpenID. Of course OpenID can be used as a means of authentication on top of a WIF/ADFSv2-based STS.

 Thanks to **Matias Woloski** who is working very close with Microsoft’s patterns & practices team, you can find more information and a conceptual view below (click image to enlarge):

 [Click to read Matias’ blog entry on OpenID integration](http://blogs.southworks.net/mwoloski/2009/07/14/openid-ws-fed-protocol-transition-sts/)

 [Click to get to DotNetOpenAuth, a library for .NET-OpenID-integration](http://dotnetopenauth.net:8000/)

 [Download .NET Open Auth here](https://www.ohloh.net/p/dotnetopenauth/download?package=DotNetOpenAuth&release=3.2.2)

 [![20091105_OpenIDInterop[1]](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/IdentityInteropUpdateforourInteroperabil_C26B/20091105_OpenIDInterop%5B1%5D_3.jpg?raw=true?raw=true "20091105_OpenIDInterop[1]")](http://www.mszcool.at/blog/2009/20091105_OpenIDInterop.jpg) 

 **Novell Bandit Project provides Information-Card Interoperability**

 [![bandit-logo](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/IdentityInteropUpdateforourInteroperabil_C26B/bandit-logo_thumb.gif?raw=true?raw=true "bandit-logo")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/IdentityInteropUpdateforourInteroperabil_C26B/bandit-logo_2.gif?raw=true?raw=true) In partnership with Microsoft, Novell is working on an initiative called “The Bandit Project”. This initative provides components and source code to implement a complete Identity Metasystem-based solution with STS, RP and even identity selectors (DigitalMe) for clients. To get these components to ensure complete interoperability of your Java- and browser-based components and end user experience on Linux, click the link below:

 [http://www.bandit-project.org/](http://www.bandit-project.org/ "http://www.bandit-project.org/")

 **Identity Interoperability with IBM Tivoli**

 At last year’s PDC 2008, [Vittorio Bertocci](http://blogs.msdn.com/vbertocci) and [Kim Cameron](http://identityblog.com/) demonstrated interoperability with IBM Tivoli access manager and ADFS v2 / Geneva Framework.

 [Take a look at Vittorio’s blog here for more details.](http://blogs.msdn.com/vbertocci/archive/2008/10/28/identity-pdc08-roadmap-session-with-kim-cameron-himself-yours-truly.aspx)

 [Click to view the session, directly, and get the info from live-demos.](https://channel9.msdn.com/pdc2008/BB11/)

 **Interoperability between Shibboleth and ADFS**

 Microsoft published a guide on interoperability between Shibboleth and the Microsoft platform for it’s previous version of Active Directory Federation Services, already. Of course this guide is still available.

 [Download Shibboleth – ADFS Interop here.](http://www.microsoft.com/downloads/details.aspx?familyid=0EBC0F18-C8F5-4699-AA36-5B6562448912&displaylang=en)

 With ADFS v2, Microsoft is implementing the SAML 2 protocol in addition to the WS-* protocols which are available in both, ADFS v2 and the Windows Identity Framework. Having that said, interoperability will be given for Shibboleth and ADFS v2, as well. I’ve found the following references on this interoperability and I’ll continue my search to find or build more concrete step-by-step guides and samples on this kind of interoperability:

 [Geneva and SAML 2 from Don Schmidt of the identity-team](http://identity-des.com/2008/11/02/geneva-saml-interop-with-a-lot-of-help-from-our-friends/)

 [SAML 2 protocol and Shibboleth Interop-Announcement](http://identity-des.com/2008/10/28/microsoft-geneva-server-supports-saml-20/)

 **Patterns & Practices Identity and Access Guide**

 Finally I wanted to share one last, extremely important resource. My friend [Eugenio Pace](http://blogs.msdn.com/eugeniop) from the Microsoft patterns and practices team in Redmond is currently working on a PnP-guide on identity and access management using ADFS v2 and WIF. This guide is currently under development and is published as a open project on [www.codeplex.com](http://www.codeplex.com). That means, feel free to start reading through the guide and provide the PnP team with feedback as much as you can and about all the things you would love to read there.

 **They are also working on a guidance on how-to implement BOTH, single-sign-on (**which is available out-of-the-box in Geneva**) and single-sign-off, which is a very special challenge, typically!"**

 [Click here to get to the guides workspace on codeplex.](http://claimsid.codeplex.com/)

 [Click here to get to Eugenio’s blog.](http://blogs.msdn.com/eugeniop/)

 **Final thoughts**

 I think, these are some of the most important pieces of information, architects and developers need when it comes to thinking about identity interoperability. I personally strongly believe in all the parts of the identity metasystem vision and claims-based security. I also see, that most of the vendors are (slowly) moving towards this direction with their products and offerings.

 So stay tuned, keep your eye on all these things.

 Cheers   
**Mario**


