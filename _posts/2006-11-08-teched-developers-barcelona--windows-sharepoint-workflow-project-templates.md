---
layout: post
title: TechEd Developers Barcelona - Windows SharePoint Workflow Project Templates
date: 2006-11-08 08:18:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Within this little blog-post you can find the SharePoint Workflow Project Templates that I have used in my Demo Extravaganzza session on Tuesday - they are also available within the [ECM Starter Kit](http://www.microsoft.com/downloads/details.aspx?familyid=38ca6b32-44be-4489-8526-f09c57cd13a5&displaylang=en) as an installer-package. As I have used these templates with a build very close to the final release I expect those templates to work with the final release as well. To install the templates, please follow the following steps:


* Unzip the ZIP Archive I have attached to this blog post.
* Open a Command Prompt and execute **install.bat**.
* The Install.bat script actually sets up the project templates for Visual Studio 2005, but it does not import the snippets nor does it add the controls to the tool-box. So you have to do that manually.
* To add the Activities to the toolbox, just create a new Tab in Visual Studio's toolbox and call it "SharePoint Workflow". Then right-click the toolbox and select **Choose Items.** All all Activities that you can find within the **Microsoft.sharepoint.WorkflowActions** assembly (you can use the "add items" dialog-box to filter based on the Microsoft.SharePoint.Workflow namespace name).
* Next you can import the code snippets using the Visual Studio 2005 Code Snippet Manager (CTRL+K, CTRL+B), which are by default copied to the **"%ProgramFiles%\Microsoft Visual Studio 8\Xml\1033\Snippets\SharePoint Workflow"** directory. I'd recommend adding the whole folder in the code-snippets manager - then you are going to get exactly the same experience as I got in my session.


If you want to have a more convenient way of installing the templates, they are part of a sample project which has been published on MSDN. Finally [these templates are part of the Enterprise Content Management Starter Kit](http://www.microsoft.com/downloads/details.aspx?familyid=38ca6b32-44be-4489-8526-f09c57cd13a5&displaylang=en) which has been released for Beta 2 of Office SharePoint Server.


[SharePoint Workflow Support.zip](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Components.PostAttachments/00/01/03/50/29/SharePoint%2520Workflow%2520Support.zip?raw=true)


