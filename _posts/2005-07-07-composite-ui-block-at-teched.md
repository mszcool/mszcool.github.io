---
layout: post
title: Composite UI Block at TechEd
date: 2005-07-07 04:45:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

On Tuesday I had an email in my inbox - "Just released, Composite UI block". And actually yesterday Alain Leroy and I did a demo about that thing in the Architecture session "Demystifying the Smart Client" at TechEd. Actually this block provides you with a web-part like framework for creating Smart Client applications that allows you to focus on


* Implementing re-usable WorkItems which encapsulate use cases for the users.
* Base classes for implementing the MVC pattern within a WorkItem by creating Views (so called SmartParts), Controllers and Models (just the data classes).
* Consolidate a number of WorkItems into Modules which are deployment units for WorkItems.
* Create a shell that enables you compose your application based on Modules and load the WorkItems in those modules into pre-defined WorkSpaces.
* Configure the appearance of the Shell through XML-based or any other type of configuration so that the shell changes it's appearance and offered WorkItems based on the user's requirement.


The important thing from my perspective is really that it supports configurations based on user's role. That means depending on the user's role in the company you can load a number of modules based on the configuration. For more information just take a look at <http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dnpag2/html/cabctp.asp>!


