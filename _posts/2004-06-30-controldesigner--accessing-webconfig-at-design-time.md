---
layout: post
title: ControlDesigner - Accessing Web.Config at Design Time
date: 2004-06-30 16:41:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

TechEd Europe is quite cool till now. Some interesting sessions (especially things about Team System and Web Services) and - if you have read one of my older blog entries - I am at the *ask the experts* getting some interesting questions from attendees. Well, one of the more interesing questions I got was how to access the web.config through an Add-In or Control Designer at design time from within Visual Studio .NET. Therefore I decided to publish some code to demonstrate that.


When you are writing an Add-In for Visual Studio .NET you have a complete object model for accessing all the functions provided by Visual Studio. The object model for the development environment is called DTE (Design Time Environment) and currently only available through COM-Interop (add a reference to the COM type library "Microsoft Development Environment 7.0"). Just look for "DTE" in the MSDN library and you'll find some interesting entries. Now take a look at the following code (which is nothing else than a control designer for a custom ASP.NET web control) - which acutally is the answer for the question I got at the booths:


public class TestControlDesigner : System.Web.UI.Design.ControlDesignerxml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


{


  public override string GetDesignTimeHtml()


  {


   string html = "found nothing";


 




   try 


   {


     // Go through the design time environment, look for the config and read some data


**EnvDTE.DTE devenv = null;**


**devenv = (EnvDTE.DTE)System.Runtime.InteropServices.Marshal.GetActiveObject("VisualStudio.DTE.7.1");**



**Array projects = (System.Array)devenv.ActiveSolutionProjects;**


     if((projects.Length == 0) || (projects.Length > 1))


     {


       html = "Exactly one project must be active";


     } 


     else 


     {


**// go through the items of the project to find the configuration**


**EnvDTE.Project project = (EnvDTE.Project)(projects.GetValue(0));**


 




**foreach(EnvDTE.ProjectItem item in project.ProjectItems)**


**{**


         // if it is the configuration, then open it up


         if(string.Compare(item.Name, "web.config", true) == 0)


         {


          System.IO.FileInfo info = 


            new System.IO.FileInfo(project.FullName);


 




          html = "<b>Found configuration</b>";


          html += "<br>" + GetConfigSetting(


            info.Directory.FullName + "\\" + item.Name);


         }


 **}**


     }


   } 


   catch(Exception ex) 


   {


     html = "Exception occured: " + ex.Message;


   }


 




   return html;


  }


 




  private string GetConfigSetting(string fileName)


  {


   System.Xml.XmlDocument doc = new System.Xml.XmlDocument();


   doc.Load(fileName);


 




   System.Xml.XmlNode node = doc.SelectSingleNode("//appSettings/add[@key='mySetting']");


   if(node == null)


     return "<font color='red'><b>missing configuration</b></font>";


   else


     return string.Format("<font color='blue'><b>setting value: {0}</b></font>", node.Attributes["value"].Value);


  }




}


This is a Control Designer for my custom control that needs to access information in web.config at design-time. The GetDesignTimeHtml() method returns the HTML code that will be displayed by the VS.NET designer when the control is dropped onto a UserControl or a web page. Through the following code I get access to the currently running instance of Visual Studio:


**EnvDTE.DTE devenv = null;**


**devenv = (EnvDTE.DTE)System.Runtime.InteropServices.Marshal.GetActiveObject("VisualStudio.DTE.7.1");**


As soon as I got the instance of the development environment I can start getting the active projects and iterating through the project items. One of these items must be the web.config file of the web application (or it might be the app.config of a Windows Forms app). As soon as I have found the configuration file I can parse it through an XmlDocument - unfortunately no classes for modifying configuration files are included with .NET 1.1.


**Array projects = (System.Array)devenv.ActiveSolutionProjects;**


     if((projects.Length == 0) || (projects.Length > 1))


     {


       html = "Exactly one project must be active";


     } 


     else 


     {


**// go through the items of the project to find the configuration**


**EnvDTE.Project project = (EnvDTE.Project)(projects.GetValue(0));**


 




**foreach(EnvDTE.ProjectItem item in project.ProjectItems)**


**{**


         // if it is the configuration, then open it up


         if(string.Compare(item.Name, "web.config", true) == 0)  
           ...


       **}**


}


**Debugging the control designer**


Unfortunately if you start debugging you will not be able to place controls onto web pages of your test web application as long as the IDE is in debug mode. Therefore for debugging a control designer for a web control you will have to open up a separate IDE with just the control project and attach to the other IDE which has the web project opened that uses the control - attach for debugging CLR code. Then switch to the other IDE, open web page designer and add the control to the page. The second IDE will switch to debugging mode (of course don't forget to set a breakpoint) and you will have the chance to debug the control designer.


