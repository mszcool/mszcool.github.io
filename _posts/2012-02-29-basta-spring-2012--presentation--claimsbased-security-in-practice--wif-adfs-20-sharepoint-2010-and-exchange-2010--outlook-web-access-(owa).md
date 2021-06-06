---
layout: post
title: Basta Spring 2012 - Presentation - Claims-based Security in Practice - WIF, ADFS 2.0, SharePoint 2010 and Exchange 2010 / Outlook Web Access (OWA)
date: 2012-02-29 00:06:48
categories: msdnblogarchive
tags: Archive MSDNBlog
---

I am a strong believer in claims-based security - those who've read my postings over the past years know that, already. At this year's [Basta 2012 Spring Edition](http://basta.net/2012spring) I've been giving a presentation on that topic again. It was primarily based on experience from my past years within Microsoft Services and some projects I've been involved in there.

 [Please click here to download the presentation material.](https://skydrive.live.com/redir.aspx?cid=d37c9d7bfbce8418&resid=D37C9D7BFBCE8418!301&parid=D37C9D7BFBCE8418!252)

 [Please click here to download the demo applications and source code.](https://skydrive.live.com/redir.aspx?cid=d37c9d7bfbce8418&resid=D37C9D7BFBCE8418!300&parid=D37C9D7BFBCE8418!252)   
(note that I have removed all identifying references to my ADFS 2.0 STS)

 Below you find the most important facts from the session as ideas, concepts or aspects you should be aware of and keep in mind when moving to claims-based security. As usual there was not enough time to walk through all of the details, so maybe if time permits I will jump into some details in future blog postings.

 **Claims-based security enables flexible yet simple authorization schemes:**

 That is the most important aspect I want you to take away. Smart designs with claims-based security can enable you to simplify authorization logic in your applications dramatically. Authorization can be controlled by the STS through rules that define, when you issue which claims. Authorization logic in the application then is as simple as querying for a claim of a specific type with a specific value.

 By deciding in the STS, which claims you want to issue based on the context (logged-in user, time of the day, etc.) you can get very close to implement the dream of a central authorization engine. Of course with current implementations (e.g. the ADFS 2.0 claims rules language) that might not be as comfortable as with dedicated entitlement systems. But from a price/performance-ration perspective that way you can get huge benefits for low costs.

 **Security Token Services and Federated Security help building bridges across security domains:**

 By implementing trusts between security token services you can open up your service to users from your partner organizations without replicating user accounts or creating shadow accounts and the like. That is a huge benefit and reduces security risk dramatically. The reason for that is that users are authenticated in their own security domains (those of the partner organizations instead of replicated accounts or shadow accounts in your own organization. That way e.g. if they leave their company and get deleted from their old company's directory you don't have any action (such as deleting shadow accounts or replicated accounts). ADFS 2.0 supports such trust-relationships through Claims Provider Trusts:

 * In your ADFS 2.0 you setup the trusted IdP / STS of your partner as Claims Provider Trust.
* Your partner needs to configure your STS as a trusted Relying Party to which it issues tokens to.

 **Claims-transformations are a powerful concept to "normalize" claims issued for your applications:**

 Again one of the most powerful concepts I've ever seen. In a smart design I'd recommend for each service you define a set of claims the service uses for authorization. These claims should be more service/application-specific. The STS can transform a variety of claims into those required by your application or service such as:

 * Active Directory group memberships into claims for your application.
	+ e.g. members of the AD group "Domain Admins" get a "super-user"-claim with advanced permissions.
* Role-claims issued from partner STS can be translated into your application-/service-specific claims by your STS.
	+ e.g. every user with a "Department"-claim of value "Purchase-Department" issued from your partner STS is translated into the "product-catalog-permission" claim with value "read".

 The really neat aspect of this approach is that your services/applications now can support users coming from many different sources through your own STS because your STS is normalizing claims from all of those different sources to what your application or service truly needs for its authorization. The following code excerpt shows a sample rule from ADFS 2.0 that issues a custom claim <http://mycustomclaims/masterclaim> if the user's User-Principal-Name (UPN) in AD is [Administrator@demos1.local](mailto:Administrator@demos1.local) (line-breaks for readability, only!!):

 
>  c:[Type ==    
>      "<http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn>",    
>    Value == "Administrator@demos1.local"]   
> => issue(   
>        Type = "<http://mycustomclaims/masterclaim>",   
>        Value = "true");   
> 
> 
>  

 Code in your application now can authorize based on the "normalized" claim issued above. ADFS 2.0 (or any other STS) can implement many different rules that might potentially result to the same claim leading to similar authorization decisions in your service or application (line-breaks for readability, only!!):

 
>  
> ```
> IClaimsIdentity MyId = User.Identity as IClaimsIdentity;
> var MyClaim = MyId.Claims.FirstOrDefault(  
>  c => c.ClaimType == "http://mycustomclaims/masterclaim");
> if (MyClaim != null)
> {  
>      if (MyClaim.Value.Equals("true"))  
>          ViewBag.Message = "You have advanced permissions based on   
>  the http://mycustomclaim/masterclaim value!";  
>      else  
>          ViewBag.Message = "NO advanced permissions based on the   
>  http://mycustomclaim/masterclaim value!";  
> }
> else
> {     ViewBag.Message = "Limited permissions due to NO   
>  http://mycustomclaim/masterclaim at all!";
> }
> ```
> 
> 


**Single-Sign-On is easy and the primary scenario as of today, Single-Log-Off seems to be easy but it isn't:**


From all the claims-based scenarios web single sign on is the scenario I've seen most often. Implementing SSO with technologies and frameworks such as ADFS 2.0, WIF, Sun/Oracle Metro, Ping Identity Server and the like is really easy. As outlined in my presentation many of the classic browser-based principles such as authentication cookies still apply and are used effectively to implement SSO. The approach is similar to what most of you experience from well-known providers such as Windows Live ID or Google ID:


* When a user browses to a web site it redirects to the STS/IdP if it does not have its own authentication cookie, yet.
* The IdP executes the login-process and if successful does two things:
	+ First it issues it's own authentication cookie (typically non-persistent, but that's up to the STS)
	+ Second it responds with a HTML form containing the SAML token for the web site and a JavaScript that posts the SAML token to the target web site it has been issued for.
* The target web site receives the SAML token and if valid issues its own authentication cookie.
* When the user navigates to the next web site trusting the same IdP/STS, the same process happens for this web site.


Given this for every web site including the IdP/STS the browser will have authentication cookies in its cache. If the user signs-out, all of them need to be deleted from the browser cache to be sure that the user is really signed out from all applications he visited. In WIF you can issue such a federated sign-out in your code with the following API calls:



> 
> 
> ```
> var FedAuthModule =   
>  FederatedAuthentication.WSFederationAuthenticationModule;
> var FedSignOutUrl =   
>  WSFederationAuthenticationModule.GetFederationPassiveSignOutUrl  
>                          (  
>                              FedAuthModule.Issuer,  
>                              FedAuthModule.Reply,  
>                              null  
>                          );  
>  
> var SessionAuthModule = FederatedAuthentication.SessionAuthenticationModule;
> SessionAuthModule.SignOut();
> SessionAuthModule.DeleteSessionTokenCookie();
>  
> WSFederationAuthenticationModule.FederatedSignOut  
>      (  
>          new Uri(FedSignOutUrl),  
>          new Uri(FedAuthModule.Realm + "Home/About")  
>      );
> ```
> 
> 


This code will erase the web applications own cookie as well as start a federated sign out to the original IdP/STS to start erasing other web site's cookies as well. ADFS 2.0 will do this by embedding iframe-constructs into the response so that the browser performs a sign-out with all web sites visited as part of a browser session. If for some reason someone manages to interrupt this process and the user leaves the machine he might not be signed out from all web sites. That might potentially give users which are subsequently accessing the same machine access to web sites visited by the previous users if authentication cookies are not expired, yet.


This browser-based SLO (Single Logout) is one possible option and the only option supported by ADFS 2.0 as of today. The reason - at least from my understanding - is that the WS-Federation protocol which is used for browser-based SSO and claims-security does not define other mechanisms. SAML protocol offers a second option through SOAP-calls performed by the STS/IdP to relying parties to ensure the sign-out. This is way more complex, not supported by ADFS 2.0 and WIF at all but definitely more secure.


The bottom line is: SLO (Single Logout) is a hard topic and even in the standards many part are still open. In practice many organizations therefore go a "compromise" between using browser-based SLO as described above and using cookies with sliding expirations having a low time-interval. But of course that solution does not work always... sometimes you just have to take the hard way and implement what's not implemented by many products and technologies, yet.


**SharePoint 2010 and Claims - Portal SSO and BCS/WCF Integration as the primary scenarios:**


Starting with SharePoint 2010 the product comes with integrated support for claims-based and federated security. It ships with its own STS that is used as the primary point-of-authentication for its own web sites and services. SharePoint is a complex beast - and claims-security does not make the complex beast simpler! Indeed the opposite is true in my opinion. So if possible I'd reduce the scenarios to the following - and even those will be hard enough (although the benefits with SSO are clear, of course):


* Single-Sign-On in interoperable portal solutions: if you need to integrate SharePoint with other portals based on interoperable standards, then federation is a good approach. I wouldn't do it just for your Intranet applications, especially if you have AD in place, as it just makes things a bit harder. But for SSO in heterogeneous environments it's the way to go.
* Integration of SharePoint with backend services through business connectivity services - also in heterogeneous environments. BCS then would request tokens through it's SharePoint STS which in turn might request tokens from trusted STS for services. In such a case you can decouple your services from SharePoint and establish a common, normalized authorization model through claims for your services while still being able to integrate them with SharePoint. That's definitely worth it!


**Exchange 2010, OWA and Claims-Security: Portal SSO is the only valid reason in my opinion:**


If you don't need to implement SSO with other web sites through in heterogeneous environments, I don't see value in undergoing the steps of using SAML and claims with Exchange 2010 and OWA as of today. My reason for that: claims are not supported and the path you need to go with the Claims-to-Windows-Token-Service (C2WTS), AD Constraint Delegation and still having the AD dependency is a big effort for little value.


As mentioned - a valid scenario is the seamless integration of OWA incl. SSO and SLO inside of portals that do use claims and federation. And that was exactly a scenario I was involved in. Of course I'll change my opinion as soon as Exchange 2010 supports claims out-of-the-box without any workarounds. As of today the basic steps for enabling OWA to use claims are:


* Enable the WIF Claims-to-Windows-Token-Service (C2WTS) on the machine that runs OWA. The C2WTS translates SAML tokes containing a UPN claim to Windows accounts of your AD. That said the C2WTS needs a high level of privileges as outlined below!
* The machine that runs OWA and the account used for the C2WTS must be enabled for Kerberos Constraint delegation using the "any authentication protocol" and "specific services" option. You need to specify all Exchange services of the exchange server as valid targets for constraint delegation.
* After that you need to configure client access in the Exchange management console to use NO authentication method from the pre-defined authentication methods.
* Then you can configure WIF in your OWA application. But again that can get tricky as well. You need to ensure the following aspects (high-level):
	+ In case of IIS 7.x integrated pipeline mode all http-modules must be configured for preCondition="managedHandler" to ensure they do not run before the WIF modules run.
	+ The order of the modules must be defined in a way that WIF modules run before any other modules. This is to ensure that the SAML token is translated to a Windows token through the C2WTS, already.
	+ The "ASPX"-page-handler MUST be enabled and it MUST accept both, GET and POST requests. Especially in the Exchange Control Panel (ECP) web application POST for the ASPX handler is disabled which is what you need to change to make it work with WIF.
	+ To be able to query FederationMetadata.xml documents you need to include the mime-type for XML-files in your system.webServer configuration if removed in the default Exchange configuration (again for the ECP panel that's needed).
	+ I'd recommend to not use fedutil.exe when configuring OWA for WIF as fedutil.exe might destroy parts of your web.config configuration from your Exchange installation. So manual configuration - at least for production environments - is your friend.


**Summary, Wrap-Up:**


As a concept claims-based security is closer to how we are dealing with security in the real world - and that makes it so powerful in my opinion. Authorization based on simple attributes issued by a trusted issuer is what we're all used to. E.g. the conference badge that permits me to get into the sessions of a conference and the color of the badge's band which permits me to get into the speaker lounge or not depending on the color.


Claims-based and federated security is **the** future in the authentication (and authorization) space. What happens to .NET 4.5 in this area shows that more than anything (please look at [Dominick Baier's](http://www.leastprivilege.com) talk from Basta Spring 2012). Looking at the session ["Using claims-based access control for compliance and information governance"](https://channel9.msdn.com/Events/BUILD/BUILD2011/SAC-422T) from BUILD 2011 you'll also note the deeper integration of the concepts in Win8 and .NET 4.5.


But as of today I strongly recommend you never ever underestimate the path to claims-based security. With my previous customer I've been working on this path for 2 years. Although we made progress it was truly painful training developers who are bound to traditional security or to Windows-only security (where most of the people literally don't care about authentication because the operating system does).


**My final advice:**


Plan your path to claims-based security carefully and start planning it today! You will need it for a variety of scenarios starting from more effective cross-company integration as we all know it to cloud-based solutions or hybrid solutions (as outlined in my previous blog postings). Claims are rapidly getting more and more important, already, and you should speed up your teams early enough to be ready when you need to be!


