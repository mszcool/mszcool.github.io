---
layout: post
title: TechEd 2007 - TLA406 Downloads, CAB Solution Patterns
date: 2007-11-07 02:30:14
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Again one blog entry from the future;) I hope you enjoyed my session about Composite UI Application Block and Smart Client Software Factories at TechEd Europe in Barcelona, as well. 

 I know this session was fully packaged and the time was very short for digging into all of the details of the framework componets I created. But ***here you find these framework components together with the finished sample solution for download.***

 My primary goal with this session really was sharing ideas with you of how you could solve certain, very special problems with CAB/SCSF May 2007 release and I hope that you have been inspired by the session to think about your own patterns or re-use/extend the patterns I've introduced.

 [Here you find the sample code and "Alpha-versions" of the frameworks for download, that I've implemented for the TechEd-session.](http://mszcool.members.winisp.net/Demos/112007_TechEdDevelopers/TLA406_Demo.zip)

 I know this is just a starting-point but I think I did many thoughs for you, already, you would have been required to do on your own when thinking about things such as Context-sensitive behavior or workflow-integration. If you want to dig in my framework components, here are the projects with the appropriate components packaged into them:

 * Conext sensitive behavior (IContextManagerService & implementation)
	+ ***mszCool.Samples.Practices.CompositeUI.Context***
* Workflow Interfaces Library (IWorkflowService, WorkflowService, External Data Exchange Interfaces and Services)
	+ ***mszCool.Samples.Practices.CompositeUI.Workflow.Interfaces***
* Workflow Activity Library (RunWorkItem Activity, RunView Activity, HandleRunWorkItemCompleted, TransferState, CallService)
	+ ***mszCool.Samples.Practices.CompositeUI.Workflow.Activities***
* Framework for abstracting away type information of WorkItems into "user task names" so that it is easier for users to identify workitems they want to run. Basically the module then needs to register workitems as tasks in a central task-service that is then used by the WorkflowWorkItem to find the workitem type it needs to run through a dictionary. In the final implementation this should be read from a database for example...
	+ ***mszCool.Samples.Practices.CompositeUI.Workflow.Tasks***

 Have much fun with it... I'd be happy to see this at some time in the future in a codeplex workspace (unfortunately I can only share this samples but not the customer-implementation that will be developed;))...


