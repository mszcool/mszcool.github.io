---
layout: post
title: State machine or State-machine based Workflow
date: 2006-09-04 17:46:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Last week I was faced with some sort of a special challenge. I went to the south of Austria for developing a prototype on Windows Workflow Foundation for one of our customers. They have successfully established a first version of their product based on .NET 2.0 and Composite UI Application block, already. This product has its own state machine component (they call it RuleEngine) as well. This component does the following things:


* Knows the states of business documents and the state transitions (obvious, isn't it;))
* Automatically calculates possible tasks to be done on business documents and which states are set if a task is completed succesfully.
* Business rule checks (mostly field checks) and finally
* Security checks


This sounds much like a state machine based workflow, does it? There is a document running through serveral stages in its life cycle with several rule checks and security checks.


But then we finally started designing and implementing the workflow and I realized that they just wanted to leverage the state machine aspekts of WF in the following way: "Hey workflow, my document is in this state, which is the next one?" or "Hey workflow, am I allowed to complete this task?". Sounds still valid, does it? But in any of these cases they wanted the actual work to be executed outside the workflow and the workflow is just responsible for answering very simple questions (yeah, if you start asking which workflow, I understand that completly). If the real work takes place outside the WF engine, the WF engine does not really manage the life cycle or processing stages of the business document and the engine should be used for just answering simple questions, I ask whether you need such a powerful, huge engine. Furthermore when you do things such as "Hey, I am in state... tell me the next state..." and you want to use WF just as a simple state machine, you are finally breaking the workflow, because when leaving the control outside and instantiating everytime a new instance of your rule engine (implemented with WF in this example), you always start your WF from scratch.


Finally my conclusion and warning is: state machine based workflows are still "WORKFLOWS" and not just state machine components. Still it's all about implementing a business process - just with a state machine based approach instead of an flow-chart based approach. Still the workflow engine is responsible for managing anything belonging to this business process - so most of the control for managing the chain of activities sits inside the workflow engine, not outside (or even worse a mixture). If you don't need or want this, then keep your hands off and stay with a much simpler state machine component. Otherwise if you need to implement a business process with all of its characteristics, then engines such as Windows Workflow Foundation are the right thing.


