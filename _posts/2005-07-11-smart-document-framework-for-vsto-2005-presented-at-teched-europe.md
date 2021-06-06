---
layout: post
title: Smart Document Framework for VSTO 2005 presented at TechEd Europe
date: 2005-07-11 12:00:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Wow, what an amazing week. TechEd Europe was really great and I enjoyed it (I am happy about the feedback for my Smart Documents session;)). Finally I have developed an updated version of my Smart Documents Framework - this time for Visual Studio Tools 2005 for Office. This framework covers the following things:


* Basic interfaces for controller, document and user control classes.
* Base Controller implementations for Excel and Word with automated context management for automatically showing and hiding controls based on context (which needs to be done manually in Word and Excel by default - and it's much harder for Excel...believe me).
* A sample implementation of how you can enforce user control reusability based on the concepts controller/document/user control (which is similar to MVC for Smart Documents).


I have created a [Gotdotnet Workspace for this framework](http://www.gotdotnet.com/Workspaces/Workspace.aspx?id=dacdee7f-6e99-4303-b743-4be141ff7710). So feel free joining the workspace, give me feedback and help me improving the design and implementation of this framework. Actually I am thinking about a way for automatic code generation and schema generation based on reusable code fragments and schema fragments... but these are still thoughts and when they are ready I'll post them:))


