---
layout: post
title: Add-Ins and Scripting in your own .NET app – but secure
date: 2004-06-14 23:17:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Many applications have the need of being extensible either through an add-in or a scripting mechanism. Technically both can be implemented very easy using the .NET framework at all:xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


·  Supporting add-ins often means nothing else than supplying an interface that have to be implemented by an add-in and loading the add-in at runtime through Assembly.CreateInstance(type…).


·  Even adding scripting support to your .NET application is not that hard if you have taken a look at CodeDOM (just take a look at a [web cast](http://weblogs.asp.net/beatsch/archive/2004/05/08/128541.aspx) of my colleague [Beat](http://weblogs.asp.net/beatsch)).


 




But very often as enthusiastic developers implementing this cool add-in or scripting mechanism we forget about security at all (or say, the danger of forgetting is highJ). How can we ensure security in that case? Exactly that’s what I want to shortly right now.


 




Basically, the answer is quite easy. Just load the assembly and types of the add-in (or the compiled script) into a separate application domain and ***restrict the security policy of this domain*** to a minimum of required permissions. Take a look at the following code snippet:


 




private void SetDomainPolicy(ref AppDomain scriptDomain) 


{


  PolicyLevel level = PolicyLevel.CreateAppDomainLevel();


 




  // Create a new permission set and add the permission set to the policy level


  **PermissionSet scriptSet = new PermissionSet(PermissionState.None);**


        **scriptSet.AddPermission(new FileIOPermission(**


           **FileIOPermissionAccess.Read, @"E:\Workshop"));**


  **scriptSet.AddPermission(new System.Security.Permissions.SecurityPermission(**


        **SecurityPermissionFlag.Execution |** 


            **SecurityPermissionFlag.SkipVerification));**


 




  // Add a policy statement


  PolicyStatement stmt = new PolicyStatement(scriptSet);


  CodeGroup group = new UnionCodeGroup(new AllMembershipCondition(), stmt);


  level.RootCodeGroup = group;


 




  // Apply the policy level


  scriptDomain.SetAppDomainPolicy(level);


}


 




This function just gets a reference to the application domain previously created through *AppDomain.CreateDomain* and sets the code access security policies to this application domain manually. After you have set those policies through *SetAppDomainPolicy* you can load new types and assemblies into the app domain running with the restricted set of permissions.


 




Another important point here is that the add-in or script is not able to perform critical actions by itself – it is the task of the host application to provide an appropriate object model to the add-in / script through which it can do call-backs into the host application for performing some critical actions. Therefore the host application perhaps has to perform some *asserts* on permissions the add-in / script does not have to avoid leading up in security exceptions (remember: stack walk and don’t forget to revert all asserts in your host application). You can find examples for assert on our local community web page [www.dotnetexperts.at](http://www.dotnetexperts.at/) within the article for [demos-download of our security road show](http://www.dotnetexperts.at/default.aspx?cid=8e47a5db-6802-48fc-9d1b-2ca199dd45cc).


 




What I have found very interesting with this is the existence of ***a whole object model for code access security***. You even can refer to configured code groups and permission sets with this object model or create new ones without touching the .NET Framework Security Tool or caspol.exe anyway. I think I will focus on exactly this object model with my following blog entries…


 




You can find a detailed description of the things I have described above in the book “[.NET Framework Security](http://www.amazon.com/exec/obidos/tg/detail/-/067232184X/qid=1087247178/sr=1-1/ref=sr_1_1/103-3937208-7477424?v=glance&s=books)” which is in my opinion a very good reference but not a book for reading from the beginning to the end at once.


