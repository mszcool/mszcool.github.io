---
layout: post
title: Cool IIS 6.0 Management
date: 2004-03-09 21:25:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

While researching for the new manageability enhancements I downloaded the IIS 6.0 resource kit tools … and believe me … they include some really cool command line tools and manageability objects.xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


The first cool thing I discovered was **SelfSSL** – a great tool for developers, too. With that tool you can configure SSL on your IIS 6.0 developer instance with just one statement in a command prompt: selfssl /T.   
You won’t believe, but this simple command generates a certificate for SSL, adds it to the certificate store (/T means add the local machine’s certificate to the trusted root authorities, too) and completely configures SSL on IIS. Simply great!!


The second cool thing is the **logparser** which allows you to parse IIS 6.0 log of each format (W3C, database…) as well as converting logs. Just take a look at the following command shell:


C:\Program Files\IIS Resources\Log Parser>logparser "SELECT time, cs-method, c-ip FROM C:\windows\system32\logfiles\W3SVC1\ex040309.log WHERE time >= TO\_TIMESTAMP('20:15:00', 'hh:mm:ss')"


 




time     cs-method c-ip


-------- --------- ---------


20:15:02 DEBUG     127.0.0.1


20:15:05 DEBUG     127.0.0.1


20:15:13 GET       127.0.0.1


20:15:14 POST      127.0.0.1


20:15:19 DEBUG     127.0.0.1


20:15:19 DEBUG     127.0.0.1


 




Statistics:


-----------


Elements processed: 39


Elements output:    6


Execution time:     0.04 seconds


 




C:\Program Files\IIS Resources\Log Parser>


But now it gets really cool for developersJ! The same thing I have done above can easily be done through the WMI provider or the COM objects included with the log parser tool (which actually encapsulate calls to WMI). Simply create a new ASP.NET project, give Network Service read access rights to the IIS logs (just for testing), and add a reference to the COM library named “MS Utility 1.0 … - LogParser Interfaces Collection”. Then run the following code in your ASP.NET page (for example in Page\_Load):


        'Create a SQL statement


        'Of course you can query logs stored in any location!!


        Dim strSql As String = "SELECT time, cs-method, c-ip " & \_


            "FROM C:\windows\system32\logfiles\W3SVC1\ex040304.log"


 




        'Create the log query class and execute the query


        Dim log As MSUtil.ILogQuery = New MSUtil.LogQueryClassClass


        Dim recSet As MSUtil.ILogRecordset = log.Execute(strSql)


 




        'Go through the results and write them to the screen


        While Not recSet.atEnd()


 




            Dim rec As MSUtil.ILogRecord = recSet.getRecord()


            Response.Write(String.Format( \_


                           "Found Record: {0} {1} {2}<BR>", \_


                           rec.getValue(0), rec.getValue(1), \_


                           rec.getValue(2)))


 




            recSet.moveNext()


 




        End While


*Resources to IIS 6.0 manageability:*


Download IIS 6.0 Resource Kit Tools: <http://www.microsoft.com/downloads/details.aspx?FamilyID=56fc92ee-a71a-4c73-b628-ade629c89499&DisplayLang=en> 


Download IIS 6.0 Resource Kit:   
<http://www.microsoft.com/downloads/details.aspx?FamilyID=80a1b6e6-829e-49b7-8c02-333d9c148e69&DisplayLang=en>


IIS 6.0 Command Line Tools:   
<http://msdn.microsoft.com/library/default.asp?url=/library/en-us/iissdk/iis/command__line_tools_included_in_iis.asp> 


