---
layout: post
title: Sidebar Gadgets in WPF - Add On
date: 2006-04-28 04:39:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

After some discussions with the team they confirmed, that this will work with RTM, but there are a couple of limitations we should be aware of:


* We cannot really communicate between the hosted WPF page and the gadget.
* Therefore we don't have access to the JavaScript APIs provided by the Sidebar gadget API and
* we don't have access to the Sidebar events from within the WPF page hosted through an IFRAME in the gadget.
* And of course we have to be aware of Code Access Security, which shouldn't be an issue for enterprise-level applications (especially for those who are already using URL-launched .NET applications today:))


In the end **I am very excited about the option of hosting WPF through IFRAME in a gadget** because it's much easier creating a gadget that communicates with more complex web services in a much easier way than it is possible through the (in **my opinion** "limited") JavaScript APIs.


What does it mean? Well, everybody has the option! Create richer, WPF based pages for integrating with complex back-end applications and with other client applications like Outlook (as calling into Outlook or other client apps is much easier from a .NET based application than from JavaScript) or creating the nice, little, fancy JavaScript / DHTML based Sidebar gadgets...


