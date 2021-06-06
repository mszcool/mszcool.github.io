---
layout: post
title: TechEd Europe – DEV309 – WCF Data Services, A Practical Deep-Dive – Session Demos Download
date: 2010-11-12 16:33:34
categories: msdnblogarchive
tags: Archive MSDNBlog
---

As promised in the last session of this year’s TechEd Europe which I had today on WCF Data Services, here you find the complete demo-code for download. 

 [Click here to get to the session demo-downloads.](http://cid-d37c9d7bfbce8418.office.live.com/self.aspx/Public/Presentations/20101112-DEV309-WCFDataServicesDemosTechEd.zip)

 Please consider the following notes to make the demos working on your machine.

 * You need SQL Server (or SQL Server Express).
* If you use SQL Server Express, you need to modify the connection strings in the backend to point to (local)\SQLEXPRESS.
* Next execute **GenerateDatabase.sql** to create the database using SQL Server Management Studio.
* Finally add the physical directory <parent>\OdataSessionsBackend as <http://localhost/OdataSessionsBackend> to your IIS-installation.
* For the Windows Phone demo app you need the Windows Phone developer tools installed.

 Note that I’ve used the users **test1** and **test2** both having the password **[pass@word1](mailto:pass@word1)** set in the membership-database. Also note that for streaming large files I required to modify the web.config of the backend to allow large uploads for the WCF binding and at the same time to allow large uploads for the web-runtime in the ASP.NET settings:

 
```
  <system.web>  
   
***<httpRuntime maxRequestLength="10000000"/>***      
    <roleManager enabled="true" />  
    <authentication mode="Forms" />  
      
    <compilation debug="true" targetFramework="4.0">  
      <assemblies>  
        <add assembly="System.Data.Entity, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />  
      </assemblies>  
    </compilation>  
  </system.web>
```


```
  <system.serviceModel>  
    <serviceHostingEnvironment aspNetCompatibilityEnabled="true" />  
***<services>  
      <service name="OdataSessionsBackend.ManageSessionsService">  
        <endpoint binding="webHttpBinding"  
                  bindingConfiguration="ManageSessionServiceBinding"  
                  contract="System.Data.Services.IRequestHandler" />  
      </service>***    </services>  
    <bindings>  
***<webHttpBinding>  
        <binding name="ManageSessionServiceBinding"  
                 maxReceivedMessageSize="500000000"  
                 maxBufferSize="500000000"   
                 transferMode="Streamed">  
        </binding>  
      </webHttpBinding>***    </bindings>  
  </system.serviceModel>  

```


```
Feel free to get in touch with me if you have any further questions![Winking smile](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/32/26/metablogapi/1300.wlEmoticon-winkingsmile_5B761B64.png?raw=true)
```

