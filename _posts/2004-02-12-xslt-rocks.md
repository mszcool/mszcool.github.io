---
layout: post
title: XSL/T Rocks
date: 2004-02-12 22:53:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Alex showed me a really cool site for XSL/T development! I have found the best style sheet ever seen before there:


<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">  
  <xsl:template match="/">  
    <xsl:value-of select="*******************" />  
  </xsl:template>  
</xsl:stylesheet>

Try this style sheet against the following document instance:

<?xml version="1.0" encoding="utf-8" ?>   
<?xml-stylesheet type="text/xsl" href="FunnyStyle.xslt"?>  
<q>2</q>

I think, this is a very good example for *how readable XSL/T actually can be*. 

So ... just love XSL/T... and enjoy things on this site: [http://www.dpawson.co.uk/xsl/sect2/sect21.html](http://www.dpawson.co.uk/xsl/sect2/sect21.html "http://www.dpawson.co.uk/xsl/sect2/sect21.html")
