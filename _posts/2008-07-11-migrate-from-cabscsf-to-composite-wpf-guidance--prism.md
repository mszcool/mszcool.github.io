---
layout: post
title: Migrate from CAB/SCSF to Composite WPF Guidance / Prism!?
date: 2008-07-11 09:47:56
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Finally, after the [Composite Application Guidance for WPF](http://www.codeplex.com/CompositeWPF) (codename "Prism") was released last week, I started thinking about necessary steps for migrating CAB/SCSF based solutions to the new Smart Client framework built with WPF. I also started migrating [the solution](http://blogs.msdn.com/mszcool/archive/2008/06/05/hp-banqpro-the-demo-for-the-paper-posted-earlier.aspx) I've created for last year's TechEd and for the [HP banqpro/ case study](http://blogs.msdn.com/mszcool/archive/2008/06/05/hp-banqpro-a-wpf-smart-client-project-with-wf-integration.aspx) to Composite WPF guidance.

 I would like to share the thoughts I made with you right now... but don't expect this to be the only post about this topic. I believe while moving on with the migration I definitely will find out some more things and my thoughts will evolve, definitely;)

 *Important note: please understand that these are my own, personal thoughts and that this is NOT Microsoft's official opinion and this is not an official Microsoft-statement at all!*

 **#1: What should I use when starting a new project?**

 *My personal conclusion: For me that question is obvious: use Composite WPF guidance and WPF if your client-hardware is sufficient for WPF.*

 WPF adds a lot value that make things for development of enterprise line-of-business client apps much easier - although the design time experience isn't where it should be, today. And Composite WPF brings you the framework for building extensible and manageable enterprise-level smart clients with WPF! So just use it!

 **#2: Should you migrate to Composite WPF from CAB/SCSF?**

 Of course many customers are asking this question right now. They are afraid that they are using an old technology which is not going to be state-of-the-art anymore and running into panic-mode.

 Well, I don't see it that critical. CAB/SCSF is still driven forward by the community (see [SCSFContrib](http://codeplex.com/scsfcontrib), [SCSFWPF](http://www.codeplex.com/SCSFWPF) etc.) and there are lots of people out there answering questions, sharing experience etc. Furthermore according to the Microsoft homepage [it is still supported the same way it was supported all the time](http://msdn.microsoft.com/en-us/library/aa480482.aspx#scsfroadmapfeedbackandsupport): it is simply treated as custom code in support cases with Microsoft.

 *My personal conclusion therefore:* *if you do not get additional business value out of Composite WPF and if you are happy with CAB/SCSF in your solution, why should you take the effort of migrating to it!? Then you don't need to... some good reasons to migrate might be:*

 * *native WPF implementation that leverages the base classes of WPF such as its existing command mechanisms etc.*
* *more lightweight - Composite WPF is more lightweight than CAB/SCSF and I had the feeling that it is easier to learn...*
* *extended flexibility of Composite WPF: you have the choice which features you want to use and which you don't want to. Composite WPF features are independent from others - e.g. you could use the MVP/MVC implementation without being bound to any "Controller" or "WorkItem" base classes or to the dependency injection mechanisms.*
* *the new Unity framework as an easier-to-use, more lightweight dependency injection framework*
* *the ability of Composite WPF to use another dependency injection framework such as spring etc.*

 But these are just some thoughts that went through my head. And again - for me important is that if you don't get any value for YOU out of these advantages then why migrate!? There is no reason for doing so. If you get additional value, then let's go;)

 **#3: How do components from CAB/SCSF map to Composite WPF?**

 As far as I have seen, the Composite WPF framework achieves exactly the same goals as CAB on it's own. There is no replacement for SCSF available, so far, and [according to Microsoft](http://msdn.microsoft.com/en-us/library/cc707819.aspx) none is planned for the foreseeable future. The team is in a kind-of gathering customer feedback loop right now;) Okay, but let's map some features from CAB/SCSF to Composite WPF - I assume that you read [my paper on CAB/SCSF](http://www.microsoft.com/downloads/details.aspx?FamilyID=5f9a8435-1651-4be2-956d-0446a89a7358&DisplayLang=en) to fully understand the following table...

 

| Shell Workspaces | Region Managers |
| Shell UI Extension Sites | none |
| WorkItem class | none or your own ordinary class   
It's on you whether you want to use "use case controllers" as described in my paper or not. And if, you just create ordinary classes without any attributes or base-class dependencies;) |
| WorkItem -   
Use Case Controller-part | none |
| WorkItem -   
Container-part | Unity Framework and it's container capabilities |
| WorkItem -   
Client Services management | Unity Framework and it's container capabilities |
| Event Broker | Event Aggregator |
| Command Pattern | WPF Commands and/or Composite WPF extensions to WPF commands |
| Dependency Injection | Unity Framework or your own |
| Smart Parts | WPF controls as views |
| Presenter class | none or your own ordinary class   
It's up to you whether you use MVP/MVC - and if you just implement ordinary classes without any attributes or base class dependencies. |
| ModuleController, ModuleInit | IModule classes |
| ProfileCatalog, ModuleEnumerator/Services | ModuleEnumerator |

 As you can see, Composite WPF supports all scenarios but leaves you more "freedom" of whether you want to implement a scenario or not. And also if you implement a scenario you often don't need to inherit from any base class or apply attributes in most cases. That means you can build-up your own hierarchies without running into too many dependencies of the Composite WPF guidance.

 **#4: The very basic steps to get from CAB/SCSF to Prism**

 These are some of the first steps I had to complete while migrating the shell and modules from CAB/SCSF to prism. Essentially these where the following:

 1. Create a new solution for the Composite WPF version of my demo.
2. Import the interface libraries from the CAB/SCSF solution to the Composite WPF solution and remove all namespace-, attribute- and base-class references to CAB/SCSF-related classes.
3. Re-create the shell as described in the guidance: I have to admit I didn't even try to migrate the shell because many of the concepts are different. But I left my **IShellExtensionService** interface concept and therefore I was able to hide this pretty heavy change from the modules I migrated later on.
4. Add a new class library with some of my own base classes:   
(1) A Controller base class with a state-bag to replace WorkItems. This is a simple as possible and I just used it because I used the WorkItem.State property. (2) A presenter base class with a reference to the original work item.
5. Add the first CAB/SCSF module to my new solution.
6. Replace the "ModuleInit" class by a IModule implementation. I even left the ModuleController-concept for simplicity.
7. Remove the WorkItemController base class reference in my ModuleController class and replace it by the Controller-base class I created in step 4.1 above.
8. Replace CAB-Commands [CommandHandler] by DelegateCommands as used in WPF and WPF composite guidance.
9. Replace all WorkItem.Service usages against a Unity Container Service usage.
10. UI Extension Site usage must be replaced by my IShellExtensionService concepts as described in my paper as they are not available anymore. Or you create your own IShellService that supports extension of menus etc. in your shell.
11. Uncomment CAB/SCSF generated code in the code-besides of the smart parts.
12. Remove all attributes in Presenter classes and SmartParts which are specific to CAB/SCSF.
13. Replace all WorkItem classes with your own, custom Controller classes that inherit from the Controller-base class I've created in step 4.
14. Update the configuration and run the application:))))

 I know, that looks heavy, but it took me 1/2 day to migrate the first two modules and the shell from CAB/SCSF to Composite WPF guidance without having played with the new guidance before, at all!!!! So I was a bloody beginner with Composite WPF guidance when I started and it took my just 1/2 day...

 I have published the first version of my migration solution where I completed the tasks described above for the first two modules of my last year's TechEd demo.

 [You can find that download in my previous post.](http://blogs.msdn.com/mszcool/archive/2008/07/11/cab-scsf-migration-demo-part-1.aspx)

 While this is just a first step I know that I will encounter some additional issues when I have time migrating the remaining parts... so stay tuned on future entries about this story... whereas I am not sure whether I will be able to do this soon:))) because... well... it's vacation time:)))

 Cheers   
**Mario**


