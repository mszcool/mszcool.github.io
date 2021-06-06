---
layout: post
title: TechEd Developers 2006 - Pre-Conference Sessions on Visual Studio Tools for Office including WPF Interop
date: 2006-11-07 06:41:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Yesterday [Eric Carter](http://blogs.msdn.com/eric_carter), Martin Sawicki, both are Program Managers of the VSTO team at Microsoft Corp. in Redmond and I delivered a whole pre-conference on VSTO at TechEd Europe: Developers in Barcelona. I delivered a pre-conference with deep technical details on Visual Studio 2005 Tools for Office. You can find the downloads here within this blog-entry.


Primarily I did the part on ActionsPane and Smart Documents based on VSTO 2005 based on the experience I made about a year ago within the Smart Docs project we did with Data Systems Austria, one of the largest ISVs in Austria. I primarily covered the following parts and demos:


* What makes a Smart Document really "Smart"?
* DEMO on building context sensitive Smart Documents with Word
* Smart Tags and how they can be programmed in VSTO 2005 Doc-Level customizations
* DEMO on building a Smart Tag together with using ActionsPanes within a Word Document
* Schema Design proven practices for creating Smart Documents as well as archtectural guidance on create VSTO Smart Document solutions for large enterprise projects.
* DEMO on building re-usable UI-controls that can be used in WinForms and VSTO Office solutions.


Finally the re-usabiltiy demo was a good indicator for me to show, how you can use Windows Presentation Foundation controls within an Office solution. Fortunately with the Windows Forms Integration Assemblies that is fairly easy. All you need to do is developing a WPF control and then adding a reference within your VSTO solution to that WPF control. In addition you need to add references to the WPF-required .NET 3.0 assemblies which are WindowsBase.dll, WindowsFormsIntegration.dll, PresentationCore.dll and finally PresentationFramework.dll. With the following code then you can add the WPF control to your Office Document Actions Pane:



> 
> using
> 
> 

 System;  
using System.Data;  
using System.Drawing;  
using System.Windows.Forms;  
using Microsoft.VisualStudio.Tools.Applications.Runtime;  
using Word = Microsoft.Office.Interop.Word;  
using Office = Microsoft.Office.Core;  
using Microsoft.Office.Tools.Word;  
 **// This is required for Windows Forms Interoperability  
using System.Windows.Forms.Integration;**
public

 partial class ThisDocument  
{  
  private TestWPFControl.UserControl1 ucWPF = new TestWPFControl.UserControl1();
  private void ThisDocument\_Startup(object sender, System.EventArgs e)  
  {  
    ElementHost host = new ElementHost();  
    host.Height = 400;  
    host.Child = ucWPF;  
    ucWPF.SomethingSelected +=   
      new EventHandler<TestWPFControl.StringEventArgs>(ucWPF\_SomethingSelected);  
    ActionsPane.Controls.Add(host);  
  }  
}
You can find all the code-samples from the session including the WPF demo within the downloads of this blog post.


Finally we had about 160 attendees in both pre-conferences of my track - the SharePoint pre-conf and the VSTO pre-conf which is pretty cool. I hope you enjoyed it - for us it was definitely a great experience;)


[TechEdPreConfDemos.zip](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Components.PostAttachments/00/01/01/48/49/TechEdPreConfDemos.zip?raw=true)


