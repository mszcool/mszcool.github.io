---
layout: post
title: ARC305 Downloads – My Session @ TechEd Europe in Berlin on “Always Responsive Apps & Services”
date: 2009-11-11 07:46:29
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Right before my session is going to start here in Berlin at 5.30 p.m. I found time to upload my demos and slides. The session is all about approaches and thoughts we implemented in a project together **with [Frequentis AG](http://www.frequentis.com)**, a globally active ISV building solutions for public safety. These solutions span air traffic control, maritime communications, police- and fire-department control and communication etc.

 All the approaches and information from the session is from a whitepaper I’ve published earlier and I’ve created together with architects from Frequentis.

 I’ve published the PoC which is the demo-solution I use for the session earlier, but for TechEd I **updated the PoC** the whole solution **to Visual Studio 2010 Beta 2**.

 [Click here to download the Proof-of-Concept demo app (VS2010 b2).](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/2009-11-AsyncTaskScenario-b2.zip)

 [Click here to download a PDF of my presentation.](http://www.mszcool.at/blog/2009/20091111_ARC305_TechEd_Europe.pdf)

 [Click here to get to the whitepaper we’ve published with Frequentis.](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/2009-04-alwaysresponsiveapps.pdf)

 **Important Notes for testing**

 If you want to test the PoC you need the following bits installed on your machine:

 [Visual Studio 2010 Beta 2](http://www.microsoft.com/downloads/details.aspx?displaylang=en&FamilyID=dc333ac8-596d-41e3-ba6c-84264e761b81)

 [Microsoft CCR and DSS Runtime Toolkit](http://www.microsoft.com/ccrdss/)

 [SQL Server Express Edition (DB-scripts included in the PoC download)](http://www.microsoft.com/downloads/details.aspx?displaylang=en&FamilyID=01af61e6-2f63-4291-bcad-fd500f6027ff)

 If you don’t have Microsoft CCR installed, then you can **remove the CcrManager.cs class** in the **AsyncDemo.JobLibrary project** in the **Shared**-folder of the solution. Furthermore you need to **remove the references to the Microsoft.Ccr.*.dll** assemblies.

 Have much fun, I hope you enjoy(ed) my session:)


