---
layout: post
title: Presentation on Microsoft's Software Factories and Modeling Strategy
date: 2008-12-22 03:30:28
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Last week I did a presentation at the technical university of Vienna on Microsoft's strategies for modeling and Software Factories. It was a great pleasure and fun for me to deliver this presentation as we had really interesting discussions afterwards with students on these topics.

 Below you can find the assets I produced for this presentation as downloads:

 [PowerPoint Presentation Download (as PDF)](http://cid-c57a431d03937fc0.skydrive.live.com/self.aspx/.Public/200812%20TU%20Vienna/ModelingStrategy.pdf)   
[Simple WCF Service Factory Demo](http://cid-c57a431d03937fc0.skydrive.live.com/self.aspx/.Public/200812%20TU%20Vienna/SimpleWSFactory.zip)   
[Simple Test DSL](http://cid-c57a431d03937fc0.skydrive.live.com/self.aspx/.Public/200812%20TU%20Vienna/TestSimpleDSL.zip) created with the [VS 2008 SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=59ec6ec3-4273-48a3-ba25-dc925a45584d&DisplayLang=en)

 Although I haven't been blogging a lot about this topic, yet, I strongly believe in this strategy. Especially since I attended the Strategic Architect Forum where Jack Greenfield clarified on some recent developments publicly announced at PDC 2008 last October. The key-questions Jack answered, are for me:

 * **UML and Microsoft - Why did Microsoft change it's perception according to UML and includes UML-diagrams with Visual Studio 2010 now while they were claiming it isn't a core strategy?**   
Well, simple and pragmatic: my understanding is that we think you can boost your productivity for most software products with factories and DSLs for about 60-80% of the development. For many more "specialized" developments that weren't part of your product-variation planning for some reasons (e.g. ROI) you still need to have structured processes and ways for covering these parts. This is exactly where UML and other general-purpose modeling-approaches can help, definitely. And I think that besides of the fact that customers want us to support UML, that is the major reason for doing so.
* **How does the future of DSL-Toolkit look like as Microsoft announced OSLO at PDC in October?**   
I think the easiest way to answer these questions is taking a look at [Jack Greenfields](http://blogs.msdn.com/jackgr) and [Stuart Kents](http://blogs.msdn.com/stuart_kent) blog. Click one of the following links below:
+ <http://blogs.msdn.com/stuart_kent/archive/2008/11/07/dsl-tools-and-oslo.aspx>
+ <http://blogs.msdn.com/stuart_kent/archive/2008/11/05/dsl-tools-in-visual-studio-2010.aspx>
+ <http://blogs.msdn.com/softwarefactories/archive/2008/05/26/software-factories-2-0.aspx>
+ <http://msdn.microsoft.com/en-us/architecture/blueprints.aspx>

 So the answer seems to be pretty simple according to these blog-postings: the much richer successor of GAX/GAT will be [Microsoft Blueprints](http://www.codeplex.com/blueprints). For DSL Tools and Blueprints the product team wants to have a plan in place for smooth migration. But as I personally think that OSLO will take some more time to complete, I think we have some "time-buffer" here. In the meantime I think DSL-tools are a pretty good way to move on;) Currently I am thinking about creating a DSL for Composite WPF which might be a funny think... depends on how much time I have left in my vacation next week:))

 Having that said I wish all of you a merry Christmas and a happy New Year;)

 **Mario**


