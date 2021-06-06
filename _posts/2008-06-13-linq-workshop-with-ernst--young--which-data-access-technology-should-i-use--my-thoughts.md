---
layout: post
title: LINQ Workshop with Ernst & Young - Which Data Access Technology should I use?? - My Thoughts
date: 2008-06-13 07:52:33
categories: msdnblogarchive
tags: Archive MSDNBlog
---

While [preparing the workshop](http://blogs.msdn.com/mszcool/archive/2008/06/11/linq-workshop-with-ernst-young-in-prag-demos.aspx) and discussing the variety of LINQ technologies in the aforementioned workshop I really started thinking of when to use which technology in the big landscape of data access possibilities on the Microsoft platform. Summarizing the technologies we have there you see the following now:

 

| **Technology** | **Available since** |
| ADO.NET Command, DataReader | .NET 1.x / 2.0 |
| ADO.NET DataSets, DataTables | .NET 1.x / 2.0 |
| ASP.NET Data Source Controls | .NET 2.0 |
| Windows Forms Data Source Controls | .NET 2.0 |
| *LINQ Foundation* | *.NET 3.5* |
| LINQ to DataSet | .NET 3.5 |
| LINQ to SQL | .NET 3.5 |
| *LINQ to XML* | *.NET 3.5* |
| ADO.NET Entity Framework | .NET 3.5 SP1 |
| LINQ to Entities | .NET 3.5 SP1 |
| Dynamic Web Data Forms | .NET 3.5 SP1 |
| *ADO.NET Data Services* | *.NET 3.5 SP1* |

 Lots of technologies and that of course fires up the question of what to use when!? I tried to think a bit about it and came up with the following conclusion as my personal opinion.

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/LINQWorkshopwithErnstYoungThoughts_8D2B/image_thumb.png?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/LINQWorkshopwithErnstYoungThoughts_8D2B/image_2.png?raw=true?raw=true)

 I think you should base your decision based on two factors: how fast do you need to be (Developer Productivity axis) and how are your requirements in terms of maintainability/flexibility and performance. Having that said I see the world as follows:

 * If you need high performance in an enterprise-level application that is NOT a prototype I would prefer manually coded data access layers going to the DB directly through commands and data readers. It's still the fastest thing but you need to code it manually.
* LINQ to SQL combines performance with productivity as it includes designer support. The OR Mapping in LINQ to SQL happens via .NET attributes applied to your classes. That is tightly coupled to the database and less flexible than complete OR mappers. But as the mapping layer is "coded" it's still faster than OR framework. So its in between of these worlds and still has a really good performance.
* If flexibility and maintainability is the major thing and you want to have your object models strongly **de-coupled** from your database, then OR-Mappers such as the ADO.NET Entity Framework and LINQ to entities (adding even more productivity to it) are the way to go.
* For **Rapid Prototypes** that you want to use for demonstrating value to customers to prepare larger projects or that are not going to be used in a long-term fashion in your company you can use Dynamic Data Forms shipping with .NET 3.5 SP1 or the classic SqlDataSource stuff we've introduced with .NET 2.0.

 I haven't added LINQ Foundation and LINQ to XML to this diagram as I think you can use them everywhere you want incl. LINQ to objects;) I also have not added LINQ to DataSets as I rather see this as a migration scenario to migration slowly form DataSets to these new technologies.

 Based on what I've written above to simplify the decision of which technology to use when I would update my diagram as follows:

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/LINQWorkshopwithErnstYoungThoughts_8D2B/image_thumb_1.png?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/LINQWorkshopwithErnstYoungThoughts_8D2B/image_4.png?raw=true?raw=true)

 I hope these thoughts help you for your future decision on which data access technology to use when. As mentioned these are my personal thoughts and not an official opinion of Microsoft in any way;)

 Cheers   
**Mario**


