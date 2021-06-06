---
layout: post
title: SQL Server Yukon - CLR & XML, but when?
date: 2004-03-16 15:05:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Yesterday during one of my UserGroup sessions in xml:namespace prefix = st1 ns = "urn:schemas-microsoft-com:office:smarttags" /?Vienna I had very interesting discussions about usage scenarios for some of the new features of SQL Server Yukon - especially about User Defined Types with .NET and XML. SQL Server Yukon will have the possibility of creating complex User Defined Types with .NET as well as includes support of XML through a native XML data type with XML Schema support. But what are the usage scenarios for these things?xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


I think, for XML it's a little bit easier than for UDTs. XML is useful for semi structured data - e.g. document management or content management systems. In such a case storing documents formatted in XML in the database and add some additional properties of those documents through columns of the table is quite obvious. Another nice thing might be the possibility of keeping XML messages received through service interfaces (e.g. Web Service) without any modifications in the database – that might be especially interesting if messages are signed by the originator and/or might be re-sent to other actors of a business process later on.


But keep in mind - engine of SQL Server is still - what a surprise - a *relational engine*. Relational engines are optimized for storing and querying relational structured data. Thus any properties that will be used in very often for querying data are candidates for table columns while things that are used in queries only rarely can be left in the XML, only.


The next topic are User Defined Types. With User Defined Types you can write complex .NET classes and include them to the scalar type system of SQL Server. “Great, now we have an object oriented database” is what I have heard very often these days. But is that really correct? Just think about it – you are still accessing data in a set oriented manner – SQL – and the engine is still a relational engine. It is optimized for working with entities and relations between those entities given through constraints. Therefore Object-Relational-Mapping layers or data access layers (however they work) are the bridge between your programming model and the relational data model.


Okay... user defined types are not for creating an OO database – what are the usage scenarios? Basically they are used for extending the scalar type system of SQL Server. We already had a little version of UDTs in SQL Server 2000 where they are nothing else than a binding between SQL Server scalar types and rules applied to these types as well as defaults. With UDTs in .NET, Yukon enables you to create richer types for the scalar type system. But the most examples I have seen till now are nice but not typical for business applications: *Point, Vector, Coordinate, GeoSpacialInfo* – good for mathematicsJ (actually yesterday I did a Point-demoJ and I was glad to have a mathematician in the audience). But when thinking in terms of classic business applications – banking, finance, portal-solutions, CRM… – what are candidates for user defined types? Some thoughts on this: special types like UTF8String, URL/URN/URI, and phone number, service tags that are company special, social security numbers, email addresses, postal codes, and monetary values combined with currency and exchange rates, metric units…).


 




I am still looking for other, better examples. If any ideas are out there I’d appreciate sharing them with meJ…


