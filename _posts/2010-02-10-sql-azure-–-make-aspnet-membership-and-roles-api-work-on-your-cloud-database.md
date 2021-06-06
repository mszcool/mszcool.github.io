---
layout: post
title: SQL Azure – Make ASP.NET Membership and Roles API work on your Cloud Database
date: 2010-02-10 02:17:32
categories: msdnblogarchive
tags: Archive MSDNBlog
---

In my last posting on the MSDN Briefing I outlined, that I migrated a sample that uses ASP.NET Membership API and Roles API for authenticating and authorizing users. Unfortunately the ASP.NET Membership and Roles API database as we know it does not work with SQL Azure as it uses some features not supported on SQL Azure.

 **Microsoft fortunately released updated scripts that you can use for creating a Membership and Roles API database that works with SQL Azure and with classic SQL Server:**

 [**http://code.msdn.microsoft.com/KB2006191**](http://code.msdn.microsoft.com/KB2006191)


