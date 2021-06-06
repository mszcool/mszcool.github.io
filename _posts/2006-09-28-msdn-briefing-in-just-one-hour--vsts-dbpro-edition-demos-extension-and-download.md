---
layout: post
title: MSDN Briefing in just one hour - VSTS DbPro Edition... Demos, Extension and Download
date: 2006-09-28 06:43:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Just one hour, then I'll start talking about Visual Studio Team Edition for Database Professionals in our first [MSDN Briefing](http://blogs.msdn.com/msdnat/archive/2006/09/27/773546.aspx). The software life-cycle management topic really hunts me for the next couple of years as an Architect in our group - but I actually love this topic (it was one of the first roles I have taken on, partially, in my first really big project with 22 members in our team about 8 years ago).


Finally, this is the only reason why I am doing this MSDN briefing today as in the future they will be driven by our new Developer Evangelist who is going to start next week...


Because today, I'll talk about how **Visual Studio Team Edition for Database Professionals (Data Dude)** fits into software life cycle management and I'll demonstrate how it supports the database developer with his tasks. It really solves a number of very well known-issues fairly elegent:


* *Fine-granular version control of your schema objects*  
as it shreds the schema objects in fine-granular SQL script files when importing from a script generated with design tools or from an existing database.
* *Schema comparison between different version of your databases*  
which allows you understanding all changes you have made to your schema including support for generating *update scripts* to update existing database schemas without loosing the data stored in the database.
* *Simple refactoring of databases*  
currently renaming objects is supported, only. But VS really updates anything related to an object being renamed (procedures, triggers, related tables, keys, constraings... anything).
* *Generation of test data and unit tests for procedures, functions and triggers*which is in my opinion the most useful part of the whole infrastructure as it allows you to implement some sort of test-driven development even for the database developers.


Based on all these features you have to understand that it targets the **developer aspects** of the database-related life-cycle management parts, only. Unfortunately so far there is no support for logical and physical database design as you might know from tools such as *ErWin* or *Visio* or *Silverrun*. But this is something going to be addressed with one of the future versions.


Also the infrastructure is highly extensible. For example the data generator for generating tons of test data can be extend with custom data generation strategies as the unit testing infrastructure can be extended with custom testing strategies and test evaluation functions / conditions. Just to give you one example - a (very simple) custom XML data generator for Visual Studio Team Edition for Database Professionals as follows:


namespace TestVstsGenerator  
{  
  [Generator(typeof(DefaultGeneratorDesigner))]  
  public class CustomXmlGenerator : Generator  
  {  
    public override string ToString()  
    {  
      return "Custom XML Generator";  
    }


    [Output(Description="String Value with the XML",   
            Name="XmlContent")]  
    public string XmlOutput  
    {  
      get   
      {  
        Random rnd = new Random();


        // Of course using XmlWriter would be better here:)  
        XmlDocument doc = new XmlDocument();  
        doc.LoadXml("<sample />");


        for (int i = 0; i < rnd.Next(5, 10); i++)  
        {  
          XmlNode n = doc.CreateNode(  
               XmlNodeType.Element, "node", string.Empty);  
          n.InnerText = string.Format("Value {0}", i);


          doc.DocumentElement.AppendChild(n);  
        }  
          
        return doc.OuterXml;  
      }  
    }


    private int \_InputCount;


    [Input(DefaultValue=10, Visible=true, ReadOnly=false)]  
    public int ElementCount  
    {  
      get { return \_InputCount; }  
      set { \_InputCount = value; }  
    }  
  }  
}


All you basically need to do is creating a class library, adding a reference to Microsoft.VisualStudio.TeamSystem.Data.dll, then create a class inheriting from Generator (in the Microsoft.VisualStudio.TeamSystem.Data.DataGenerator namespace) with a number of properties: one or more properties used as input whereas one property is queried by VS for the output. This is the case when you use the DefaultGeneratorDesigner (specified in the [Generator] attribute at class-level), but you can build your own designer, as well.


Next you need to strong-name the assembly, add it to the GAC and then create an XML-file called *YourAssembly.Extensions.xml* which you need to copy to the C:\Program Files\Microsoft Visual Studio 8\DBPro directory. Oh yes, and in CTP 5 in addition the assembly of your custom generator needs to be at the C:\Program Files\Microsoft Visual Studio 8\DBPro\Extensions directory, as well. The XML configuration file registers the assembly and each generator-type contained in the assembly looks as follows:


<?xml version="1.0" encoding="utf-8" ?>  
<extensions  assembly="TestVstsGenerator, Version=1.0.0.0, Culture=neutral, PublicKeyToken=806d263e5103e588" version="1">  
  <extension type="TestVstsGenerator.CustomXmlGenerator" enabled="true" />  
</extensions>


Now you are done, just restart Visual Studio, create a new test generation plan and you have you custom XML test-data generator in the list.


This is something I am going to demo in about 2,5 hours:) (I'll start with a couple of other demos, before, of course). I have attached the demos for the briefing to this post.


[VstsDbPro\_Demos.zip](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Components.PostAttachments/00/00/77/52/71/VstsDbPro_Demos.zip?raw=true)


