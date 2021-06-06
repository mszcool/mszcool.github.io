---
layout: post
title: LINQ to SQL and my Previous Architectural Thoughts
date: 2008-06-25 17:16:19
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Thanks to Jim Wooley I have to extend what I wrote about my [LINQ-Architecture thoughts before](http://blogs.msdn.com/mszcool/archive/2008/06/13/linq-workshop-with-ernst-young-which-data-access-technology-should-i-use-my-thoughts.aspx). Actually LINQ to SQL supports putting the OR-Mapping definitions into a separate XML file instead of having the mapping meta data applied as attributes on your classes and properties. Just take a look at the following blog-entry: 

 [http://www.thinqlinq.com/Default/LINQ-to-SQL-support-for-POCO.aspx](http://www.thinqlinq.com/Default/LINQ-to-SQL-support-for-POCO.aspx "http://www.thinqlinq.com/Default/LINQ-to-SQL-support-for-POCO.aspx")

 It describes how-to put the OR-Mapping meta data into a separate XML file.

 Nevertheless the mapping-mechanisms of LINQ-to-SQL are much simpler compared to full-blown OR-Mapping frameworks such as the ADO.NET entity framework. And still I believe that the performance of simpler mapping mechanisms will be higher, of course. So the fact that this is possible does not really change anything to my recommendations in terms of deciding which DB technology to use when;)

 Anyway, thanks to Jim who pointed me to the link above. It's pretty cool that LINQ-to-SQL also supports a more "generic" mapping approach even for the small-to-medium sized applications.


