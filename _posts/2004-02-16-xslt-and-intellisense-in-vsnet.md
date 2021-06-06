---
layout: post
title: XSL/T and IntelliSense in VS.NET
date: 2004-02-16 21:11:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

As I will hold an XML Workshop focused on XSD, XPath and XSL/T for a large group of employees of a customer tomorrow, I have tried many things with XSL/T during the last few days. The annoying thing with XLS/T is not only, that it is hard to read when getting more and more complex (remember my last posting), but you even don't have IntelliSense in Visual Studio .NET.xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


Fortunately there is a very easy possibility for getting IntelliSense support (look at the article from Aron Skonnard in the January Web Q&A of MSDN Magazine <http://msdn.microsoft.com/msdnmag/issues/04/01/XMLFiles/>), because Visual Studio xml:namespace prefix = st1 ns = "urn:schemas-microsoft-com:office:smarttags" /?.NET has a built-in generic IntelliSense mechanism based on XML Schema for tag oriented languages like HTML, XML, XSD and so forth.


Thus, all you need, is a schema for XSL/T (which is not easy to find, because the W3C does not have created one). Just navigate to <http://www.fesersoft.com/dotNet/> and look for XSL on the page. It seems to be a nearly complete schema for XSL/T. You need to download the ZIP archive and put the **xslt.xsd** file (I have renamed it to *xsltschema.xsd*) into the schema directory of Visual Studio .NET which is located in the **Common7\Packages\schemas\xml** directory of the Visual Studio .NET program files folder. After you have copied the file into this directory, create a new XSL/T document and try it out.


