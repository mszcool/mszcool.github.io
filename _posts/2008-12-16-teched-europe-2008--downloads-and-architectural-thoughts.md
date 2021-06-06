---
layout: post
title: TechEd Europe 2008 - Downloads and Architectural Thoughts
date: 2008-12-16 11:32:11
categories: msdnblogarchive
tags: Archive MSDNBlog
---

A little late but nevertheless I managed to publish my demos from this year's TechEd Europe 2008 in Barcelona from my session on the **identity meta system applied to real world projects** in Austria.

 As mentioned, the experience I summarized in the session comes from a project we've been driving in Austria in the electronic health care sector as outlined [in issue 16 of the architecture journal](http://msdn.microsoft.com/en-us/architecture/cc836394.aspx) in [my article](http://msdn.microsoft.com/en-us/architecture/cc836394.aspx).

 You can download the demo applications from my session at TechEd by clicking the link below:

  Also here you can download the presentation material of my session by clicking below:

  Essentially in my presentation I really focused on discussing four things where in my opinion the concepts from the identity meta system vision really helped us implementing our stuff:

 * **Clear separation of concerns**   
Separation of where authentication happens from where authorization happens enables you switching authentication modes without affecting your back-end services. If authentication happens at all your services, you need to touch all the clients and services for doing so. If you separate authentication out into a Security Token Service you just need to touch your clients and the STS while the services at the back end can remain untouched. In the attached demos you need to work with the NET.SecondTestClient, the mszcool-ActiveSTS and the ClaimsAwareWebService-VS2008 projects to test out what I've shown in my session.
* **Simplification through Claims**Claims-based security helped us implementing the two-factor authentication that was a requirement for according to the strong data protection law. More on that later in this post.
* **Building bridges between domains and/or platforms**   
On the one hand side trust-chains between Security Token Services helped us separating out responsibilities and ownerships given based on political reasons while still remaining an easy possibility to change these responsibilities and ownerships by just merging or splitting STS'es if the political interests changed. On the other hand transformation of tokens from rather proprietary tokens to standardized tokens is another thing where the separation of concerns between authentication (STS) and authorization (Relying party service) really can help. In the attached demo you need to work with the **JavaWebHostNew** Netbeans 6.5 project as well as the NET.TestClient and the SimpleActiveSTS-VS2008 projects to try things I've shown in my session at TechEd.

 Coming back to the second point I outlined above. If you're working with the NET.ThirdClient, the mszcool-activeSTS and the ClaimsSuperTokenService you can try out a simple implementation of our approach for making sure that only things are published into some e-health system if the patient explicitly agrees at leat two times. The model from a business point-of-view was the following:

 * For getting read access, a patient needs to explicitly authenticate with his e-card when visiting a doctor for a medical treatment at the reception. For the time of the treatment the doctor got **read-only** access to documents published in a variety of e-health services. For this purpose, the e-card STS issues a **standard-token** with standard-claims based on the e-card authentication. e-health services do not allow to update any content with this standard-token because the STS does not add a what we called **super-token-claim**.
* For updating content in e-health services the patient needs to explicitly authenticate for each update-process in addition to the previously mentioned, first authentication step. During this authentication the client application sends the previously issued token as a means of authentication to the STS which leads the STS to add a super-token-claim to the issued token. Therefore e-health services detect the presence of the super-token-claim and allow writing / updating content to their storage.

 Subsequent graphics should outline what's going on there. Take the first graphic as an example. Here the previously defined process is executed as needed. The client authenticated for read-stuff and gets a token for reading stuff without the super-token-claim. Later during the medical treatment the doctor wants to publish stuff and therefore the patient authenticates a second time. That second authentication includes the previously issued SAML token which leads the STS to include the super-token-claim in the newly issued token. With this newly issued token the client software of the doctor can issue an update on the back end e-health service of the current context.

 [![](http://zawp9w.bay.livefilestore.com/y1ppnvhOSj8zP0_n9qJRYg805PcT1V-FxfUwasefJIXdnBqFhe9L16OIRAn5snMZbKMSFyMWq0Ztwk/TechEd Demo Part 1.jpg)](http://zawp9w.bay.livefilestore.com/y1ppnvhOSj8zP0_n9qJRYg805PcT1V-FxfUwasefJIXdnBqFhe9L16OIRAn5snMZbKMSFyMWq0Ztwk/TechEd%20Demo%20Part%201.jpg "Everything Okay") 

 If one of the authentication steps, either the first or the second one is missing, the STS won't issue a token with a super-token-claim. And therefore the e-health services should and can simply deny access to any updating operation - based on a simple query whether a claim has been added to the token issued by the STS or not. The following graphic demonstrates what happens if the client tries to update content in an e-health services without the first authentication-step so that you can better understand our selected approach and idea:

 [![](http://zawp9w.bay.livefilestore.com/y1pY4xj4VtY_Q7kPOsir0E2Lqh0dA8soaJcbR1RzQX0lfy1_nW7lV8NGD7pqpKcq2lOt4kqint0zAw/TechEd Demo Part 2.jpg)](http://zawp9w.bay.livefilestore.com/y1pY4xj4VtY_Q7kPOsir0E2Lqh0dA8soaJcbR1RzQX0lfy1_nW7lV8NGD7pqpKcq2lOt4kqint0zAw/TechEd%20Demo%20Part%202.jpg "No two-phase authentication") 

 The neat thing is that the e-health services really just need to query the issued SAML-token from the STS for the super-token-claim. If it's there let updates happen, if not then not;) As simple as that. In classic scenarios they would need to manage sessions, state and all that stuff what is much more complicated.

 In my opinion this really shows, how claims-based security (as a part of the concepts from the identity meta system vision) really simplifies complex processes in the security world by separating the responsibility of authentication from authorization and by making authorization as simple as querying standardized tokens for claims.

 If you're interested in my approach for building bridges between platforms through the separation of concerns with STS'es and relying parties, just take a look at my [previous posting](http://blogs.msdn.com/mszcool/archive/2008/10/30/devcamp-2008-making-security-interoperability-work-with-a-zermatt-based-security-token-service-sts-a-net-client-and-a-java-web-service-hosted-in-glassfish.aspx) which I created for TechEd 2008 and the local DevCamp conference [here...](http://blogs.msdn.com/mszcool/archive/2008/10/30/devcamp-2008-making-security-interoperability-work-with-a-zermatt-based-security-token-service-sts-a-net-client-and-a-java-web-service-hosted-in-glassfish.aspx)

 Any questions - feel free asking me through comments or the contact link here of the blogging-engine;)

 Cheers   
**Mario**


