---
layout: post
title: VSTO 2005 - "Inheritance" to implement "Template-based" Solutions??
date: 2006-03-27 11:48:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Another question I got from the VSTO 2005 event in Denmark in February was a really interesting one. In short words the customer wants to create a central document template with a VSTO 2005 customization with the primary logic. Every outpost or department then should have the possibility to create a template that inherits functionality and basic layout from the central template. The primary reason for having this type of "inherited" templates is that the departments and outposts want to add some additional standard texts and logos in dedicated sections of the document.


**The first view: man, it's not simple...**


Well, unfortunately you cannot create templates that inherit behavior and customization assemblies from other templates with the current version or the next version of Office. But for the scenario above I think there is another nice way for solving the problem.


**Take a closer look: hey, it's really simple;)**


Actually you can create a template that has all the boilerplate sections, texts and images in it as well as the association with the customization assembly. For inserting "customized" logos, text portions or sections, your customization assembly could provide functionality, that reads this information from other word documents and copies them into this document. Of course if you insert version information about the artifacts your customization assembly has added to the document, you even can update these parts if they are out of date.


The parts inserted by your customization assembly might come from one of the following locations:


* Another Word document stored on the file system
* A Word document stored in a database
* Word documents retrieved from web services
* any other text file retrieved from any other data source.


Actually as you are using .NET for creating the customization assembly, you can get the information from anywhere you want. Your customization assembly just needs to get the data to be inserted into your document and use Word's **InsertFile()** method.


**A sample solution - attached to this post**


I have attached a simple example that demonstrates a possible way for achieving this result. This is a word document that has a VSTO 2005 customization attached. The document defines the overall structure of the content and the portions of text and paragraphs that need to be in each and every document. It also defines a number of place-holders through bookmarks (of course you can do the same thing by attaching an XML schema, I just used bookmarks for simplicity).


The VSTO customization assembly comes with a control that gives you the possibility for selecting a template from a list and applying this template to your document. This actually takes information from a number of other Word documents stored in a sub directory of the solution - but remember, you can also retrieve them from a database or a web service, InsertFile() just needs you to save them temporarily on your file system (shouldn't be a problem;)). Every Word document in each of the templates sub directory is a part that will be inserted to it's designated section of the document containing the place-holders and the VSTO customization. 


The Word documents in the template sub directory are NOT attached to a customization, nor do they have any functionality. These documents contain just text, images or other content without functionality.

![](/mszcool/attachment/562071.ashx)
It then uses the **InsertFile()** method of word to insert the different pre-defined text and image parts from the template documents into the primary document as follows:


object

 start, end;  
object missing = System.Type.Missing;  
string TemplatePath = TemplatesList.SelectedItems[0].Tag as string;
// Insert the header templates  


start = Globals.ThisDocument.HeaderBookmark.Start + 1;  
end = Globals.ThisDocument.HeaderBookmark.End - 1;  
Globals.ThisDocument.Range(ref start, ref end).Select();  
Globals.ThisDocument.Application.Selection.InsertFile(  
            string.Format("{0}\\{1}\\Header.doc",  
                        MySettings.TemplateDirectory,  
                        TemplatePath  
            ),  
            ref missing, ref missing, ref missing, ref missing  
);
// Insert the footer templates  
start = Globals.ThisDocument.CompanyFooterBookmark.Start + 1;  
end = Globals.ThisDocument.CompanyFooterBookmark.End -  
1;  
Globals.ThisDocument.Range(ref start, ref end).Select();  
Globals.ThisDocument.Application.Selection.InsertFile(  
            string.Format("{0}\\{1}\\Footer.doc",  
                        MySettings.TemplateDirectory,  
                        TemplatePath  
            ),  
ref missing, ref missing, ref missing, ref missing  
);


// Insert the logo templates  
// Logo is within a text box shape - so select ranges in shape  
Globals.ThisDocument.CompanyLogoBookmark.Range.Select();  
Globals.ThisDocument.Application.Selection.Start += 1;  
Globals.ThisDocument.Application.Selection.End -= 1;  
Globals.ThisDocument.Application.Selection.InsertFile(  
            string.Format("{0}\\{1}\\Logo.doc",  
                        MySettings.TemplateDirectory,  
                        TemplatePath  
            ),  
            ref missing, ref missing, ref missing, ref missing  
);


Of course instead of having those templates on the file-system as it is the case in my solution, it would be better retrieving them through a web service or from a database. But the concept is still the same.


With this solution you can provide a "central template" with the customization and all the automatic update features of the "central template" for the VSTO customization and at the same time you have the ability to customize the "central template" with contents from plain word documents without any additional functionality.


As I had to attach the image to this blog entry, please [follow-up with my next blog entry](/mszcool/archive/2006/03/27/562076.aspx) to get the sample:))


[SolutionExplorer.JPG](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Components.PostAttachments/00/00/56/20/71/SolutionExplorer.JPG?raw=true)


