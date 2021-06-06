---
layout: post
title: Europe Union Developers Conference - Office 2003/2007 and SharePoint Interoperability Demo
date: 2007-02-19 08:23:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

The European Union developers conference last Thursday and Friday was really very exciting. It's the feeling that makes this conference different to other conferences and sessions I've given so far. I'd like to share the prototype (with all sources etc.) I did at the presentation for the EU as I really think it hits the spot for one, very important message: the capabilities of interoperability of Office through the .NET Framework and especially through it's new file format, the [ECMA Office Open XML File Formats](http://www.ecma-international.org/news/PressReleases/PR_TC45_Dec2006.htm). 


[You can download the prototype here... (attachment of this blog entry)](http://blogs.msdn.com/mszcool/attachment/1715448.ashx "Office Interop Solution")


The following picture outlines the scenario I've taken as a business case for the prototype:


[![](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/EuropeUnionDevelopersConferenceOffice200_A597/Business%2520Scenario_thumb%5B5%5D.jpg?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/EuropeUnionDevelopersConferenceOffice200_A597/Business%2520Scenario%5B5%5D.jpg?raw=true?raw=true)


The scenario takes an organization performing managing several IT projects. Each project manager is responsible to submit project budget reports in a regular interval to a server. The organization manager then generates a consolidated budget report in word for getting the big picture on the company's budget-situation with another application that gathers business information from the submitted budget reports and summarizes it in a consolidated word document based report. The core requirement: interoperability with other platforms and between Office versions. So any client-based functionality below runs on Office 2003 and Office 2007 including the VSTO solution (see blow) and the document generated on the back end. Web services and document generation is provided with .NET and Java.


***The back end of our system - Windows SharePoint Services 2007***


The back end for our system is a Windows SharePoint Services 2007 site with lists for available projects, departments and cost categories as well as a document library for hosting the Visual Studio Tools for Office based Excel project budget reports. The information from SharePoint is exposed through custom web services which are providing the information via WS-I compliant, strongly typed web service facades (opposed to the out-of-the-box, very "generic" types of web services provided by WSS). Here is one example code excerpt for accessing the information of SharePoint within a custom web service (the method is encapsulated within a service implementation class that is hosted by the web service facade):


public ProjectsResponseMessage GetProjects(ProjectsRequestMessage request)  
{



> 
> ProjectsResponseMessage response = new ProjectsResponseMessage(); 
> 
> 
> // Connect to the SharePoint site  
> SPSite site = new SPSite(ConfigurationManager.AppSettings["SPSite"]);    
> // SPSite URL passed to SPSite constructor is for example [http://mszCool:8081/eusite/](http://mszcool:8081/eusite/Projects%20List)  
> SPList projectsList = web.GetList(ConfigurationManager.AppSettings["ProjectList"]);    
> // SPList URL passed to GetList for example [http://mszCool:8081/eusite/Projects%20List](http://mszcool:8081/eusite/Projects%20List) 
> 
> 
> // Run through the library's entries and get data from the document  
> foreach (SPListItem project in projectsList.Items)  
> {  
>     ProjectEntity p = new ProjectEntity();  
>     p.ProjectID = int.Parse(project["Project NR"].ToString());  
>     p.ProjectName = project["Title"].ToString();  
>     p.Description = project["Description"].ToString();  
>     p.StartDate = DateTime.Parse(project["Start"].ToString());  
>     p.EndDate = DateTime.Parse(project["End"].ToString());  
>     response.Projects.Add(p);  
> } 
> 
> 
> return response;
> 
> 


}


***The first front-end: A Visual Studio 2005 Tools For Office Document Level Customization***


The project budget report is a **Visual Studio Tools for Office** document level customization, that retrieves information necessary for filling out the report from the server such as department information, a list of available projects with details and cost center categories (hardware costs, software costs etc.). All the information is managed on the previously introduced **Windows SharePoint Services 2007** and accessible through the previously introduced web services. Finally report then gets submitted as a old, binary Excel SpreadSheet (.XLS) to SharePoint to demonstrate that even accessing binary file formats is possible with VSTO without having Office clients installed on the server. VSTO uses cached data islands to store an XML-serialized version of cached data within the binary format's extensibility points. With Visual Studio ORCAS this data islands will leverage the Custom XML data islands extensibility points of the ECMA Office Open XML file formats. 


public partial class Sheet1  
{  
    ***[Cached]  
    public BudgetReportEntity ReportData;*** 


    private ProjectSelection ProjectPanel;  
    private DepartmentSelection DepartmentPanel; 


    private ProjectProxy.ProjectService ProjectProxyInstance;  
    private DepartmentProxy.DepartmentService DepartmentProxyInstance; 


    private void Sheet1\_Startup(object sender, System.EventArgs e)  
    {  
        // ...  
    }


    // ... rest of the implementation  
}


Within the SharePoint services web service implementation I am accessing the budget report in a web service that provides the BudgetReportEntity business data through a web service as follows:


public BudgetReportResponseMessage GetReports(BudgetReportRequestMessage request)  
{



> 
> BudgetReportResponseMessage response = new BudgetReportResponseMessage(); 
> 
> 
> // Connect to the SharePoint site  
> SPSite site = new SPSite(ConfigurationManager.AppSettings["SPSite"]);  
> SPWeb web = site.OpenWeb();  
> SPList docLibrary = web.GetList(ConfigurationManager.AppSettings["BudgetLibrary"]); 
> 
> 
> // Run through the library's entries and get data from the document  
> foreach (SPListItem report in docLibrary.Items)  
> {  
>     // Budget report for deserialization  
>     Budget.Entities.BudgetReportEntity reportEntity = null; 
> 
> 
>     // Get the file and open a stream  
>     byte[] fileBytes = report.File.OpenBinary();  
>     **ServerDocument document =   
>            new ServerDocument(fileBytes, report.File.Name);**    foreach(CachedDataHostItem host   
>                in document.CachedData.HostItems)  
>     {  
>         // A host item is a sheet hosting data in case of excel  
>         foreach (CachedDataItem item in host.CachedData)  
>         {  
>             **// There is one report in our item  
>             StringReader sr = new StringReader(item.Xml);  
>             XmlTextReader xtr = new XmlTextReader(sr);  
>             reportEntity =   
>               BudgetSerializer.Deserialize(xtr)   
>                  as BudgetReportEntity;  
>             response.Reports.Add(reportEntity);**        }  
>     }  
> } 
> 
> 
> return response;
> 
> 


}


***Generating Consolidated Reports using Office Open XML File Formats and Java***


Out of the submitted budget reports a consolidated budget report for Word needs to be generated. To demonstrate interoperability with Java / J2EE environments, I've written a .NET web service that extracts the business data from the old, binary format through VSTO 2005 ServerDocument programming model (see above) and make it available through a web service to another, Java-based service. This Java Based service (which I've implemented as Java Swing Client for simplicity) generates a Word document based on the ECMA Office Open XML File formats standard (DOCX). For that purpose I've created a Office document based on the new file format which is attached to a custom XML schema. Why did I use the Office 2003-approach for attaching to schemas instead of Custom XML schemas? Well, the 2003-approach is supported on both Office versions, 2003 and 2007. Therefore using the file converters the DOCX can be opened without any troubles with Office 2003 and Office 2007 as this functionality is supported with both versions.


[![](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/EuropeUnionDevelopersConferenceOffice200_A597/image_thumb%5B4%5D.png?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/EuropeUnionDevelopersConferenceOffice200_A597/image%5B4%5D.png?raw=true?raw=true) 


So generating the document is nothing else than opening the ZIP-package (the Office Open XML File is nothing else than a ZIP package), finding the word-document XML document and then finding, duplicating and inserting information into the XML-nodes of the custom schema (as "customXml" element with an "element" attribute). To have some abstraction for the relationships between XML files within a DOCX(ZIP)-package in place a packaging API would make accessing the parts of the package more convenient (but finally is optional). The .NET Framework 3.0 includes one with classes in the System.IO.Packaging namespace and thanks to [Julien Chable](http://julien.chable.net/) a wrapper around java.util.ZipFile exists as packaging API for Java as well. To demonstrate the same on .NET 3.0, I've also written a .NET command line application to generate the same budget report out of Microsoft's environment using the System.IO.Packaging API. So you can compare these two applications to see that the difference of generating a document on the server between Java and .NET is not really that large. Of course when using the [File compatibility packages](http://www.microsoft.com/downloads/details.aspx?FamilyID=941b3470-3ae9-4aee-8f43-c6bb74cd1466&DisplayLang=en) for Office 2003, the files stored in the new Open XML file formats can be processed on Office 2003, Office XP and Office 2000 as well. 


***Final Words***


Attached to this post you can find the sources of the sample scenario introduced above. I've used the following infrastructure for creating and testing the applications on both platforms (please note my comments on configuration settings which will affect you when you try to run the samples within your environment):


* ***Requirements for the Windows / SharePoint / .NET parts:***
	+ Windows Server 2003 SP1 or Windows Server 2003 R2
	+ [.NET Framework 2.0](http://www.microsoft.com/downloads/details.aspx?FamilyID=0856eacb-4362-4b0d-8edd-aab15c5e04f5&DisplayLang=en) and [.NET Framework 3.0](http://www.microsoft.com/downloads/details.aspx?FamilyID=10cc340b-f857-4a14-83f5-25634c3bf043&DisplayLang=en)
	+ IIS 6.0 installed with [Windows SharePoint Services 2007](http://www.microsoft.com/downloads/details.aspx?FamilyID=d51730b5-48fc-4ca2-b454-8dc2caf93951&DisplayLang=en)
		- The site I've used was [http://mszCool:8081/eusite](http://mszcool:8081/eusite) which is important for the web.config configuration of my web service wrappers which are referring to this site in web.config app-settings.
	+ [Visual Studio 2005 SP1](http://www.microsoft.com/downloads/details.aspx?FamilyID=bb4a75ab-e2d4-4c96-b39d-37baf6b5b1dc&DisplayLang=en)
	+ [Visual Studio 2005 Tools for Office and Visual Studio 2005 Tools for Office Second Edition](http://msdn2.microsoft.com/en-us/office/aa905533.aspx)
		- My web service wrappers around SharePoint ran on <http://localhost/EUServices> which is important to the app.config configuration of my VSTO based solutions and .NET and Java applications which are accessing the web services
	+ Office 2003 and Office 2007 client applications (to test compatibility between office versions)
	+ Optional: [Office Open XML File Format compatibility packages](http://www.microsoft.com/downloads/details.aspx?FamilyID=941b3470-3ae9-4aee-8f43-c6bb74cd1466&DisplayLang=en)
* ***Requirements for the Java-based web services and Java-application for generating the documents***
	+ Java2 Standard Edition 1.5.x (JDK 1.5.x)
	+ [Apache Tomcat 5.5.20](http://tomcat.apache.org/download-55.cgi#5.5.20)
		- Running on [http://localhost:8780](http://localhost:8780/) on my machine which is important if you want to call the Java-based web services I am providing in the sample as possible replacement for some the .NET based web services to show Office client interoperability with Java-based back end systems.
	+ [Apache Axis2 version 1.1.1 for Java web services](http://ws.apache.org/axis2/)
	+ [Eclipse Callisto Enterprise Project Bundle from IBM](http://www-128.ibm.com/developerworks/eclipse/downloads)
	+ The Office Open XML Packaging API for Java from [www.openxmldeveloper.org](http://www.openxmldeveloper.org/) from the following article (many thanks to [Julien Chable](http://julien.chable.net/) who wrote the Packaging API wrapper for Java): [Open XML and Java](http://openxmldeveloper.org/articles/OpenXMLandJava.aspx)


Finally I think this prototype really shows important concepts of what's possible with Microsoft Office, .NET Framework and the new ECMA Office Open XML File Formats in heterogeneous environments. As long as your web services are really WS-I compliant and as long as you stay with features which are available on more than one Office version it's really possible implementing a solution that works well in heterogeneous environments. Feel free to get in touch with me if you have any questions on my solution. 


[OfficeInteroperabilityPrototype.zip](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Components.PostAttachments/00/01/71/54/48/OfficeInteroperabilityPrototype.zip?raw=true)


