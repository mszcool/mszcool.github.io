---
layout: post
title: VSTO 2005 Event in Denmark - and a new version of my SmartDoc Framework ... and a plan to work with object binding in the future
date: 2006-02-24 06:03:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Yesterday I did a VSTO 2005 event in Denmark - Aarhus for about 150 attendees there. It was really a great run and much fun for me. Actually for two reasons:


* It was the first technical workshop I did in a cinema. That was actually great fun because I was always waiting for King Kong or Darth Vader appearing behind me on the screen.
* Already, I have been very often to Denmark - but I never left the airport (when we go to Redmond, we almost everytime go through Copenhagen). This time - thanks to Niels (DE in Denmark) - I left the airport for the first time and have seen something of the country.


Also, the audience motivated me, to continue my work on the VSTO 2005 Smart Documents framework I have layered on-top of the VSTO 2005 classes. So the first little changes are updated on the workspace. Two cool features: the first one is that I have added the possibility to run Web Service calls asynchronously with letting the framework automatically show some sort of "Wait Window" allowing the user to cancel the call and secondly the Excel-Controller now supports specifying NamedRange and XmlMappedRange objects to be used as "Context" objects in the framework.


Here is the link to the workspace: 


<http://www.gotdotnet.com/Workspaces/Workspace.aspx?id=dacdee7f-6e99-4303-b743-4be141ff7710>


 


xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?**What's next? Object Binding Support**




 


Actually during my talk in Denmark I demonstrated object Binding with **VSTO 2005 and BindingSources**. This provides you with optimal possibilities of defining your data structures as classes and binding those to parts of your document. Therefore you define the things that are interesting for your application as class and bind the parts of the class to the document, that should be modified through editing the document. Suppose to have the following class:




 


[

Serializable]  
public class SimplePerson  
{

> 
> private string \_Firstname;  
> public string Firstname  
> {  
>    get { return \_Firstname; }  
>    set { \_Firstname = value; }  
> }
> 
> 



> 
> private string \_Lastname;  
> public string Lastname  
> {  
>    get { return \_Lastname; }  
>    set { \_Lastname = value; }  
> }
> 
> 
> private DateTime \_Birthdate;  
> public DateTime Birthdate  
> {  
>    get { return \_Birthdate; }  
>    set { \_Birthdate = value; }  
> }
> 
> 


} 


Assume that this class defines exactly the information that is relevant for you in your applications (on both - the server and the client). You can now use the VSTO 2005 data binding infrastructure to bind a list of these persons (List<SimplePerson>) to a list object in Microsoft Excel. Just add an object data source to your project and drag & drop it from the data sources window in Visual Studio 2005 to the surface of the Excel spread sheet. Afterwards you need to add the following code to bind the "object data source" to your list of persons:




private List<SimplePerson> \_Persons;
private void Sheet1\_Startup(object sender, System.EventArgs e)  
{  
   \_Persons = new List<SimplePerson>();  
   for (int i = 0; i < 10; i++)  
   {  
      SimplePerson p = new SimplePerson();  
      p.Firstname = "Person " + i.ToString();  
      p.Lastname = "Lastname " + i.ToString();  
      p.Birthdate = DateTime.Now.AddDays(i);  
      \_Persons.Add(p);  
   }  
   simplePersonBindingSource.SuspendBinding();  
   simplePersonBindingSource.DataSource = \_Persons;  
   simplePersonBindingSource.ResumeBinding();  
}




That's it! And the list even automatically updates the List<SimplePerson> collection when the user adds a new person by entering data in the spread sheet.


 


**If you take a look at the attachments I have added the Object-Binding sample to this post. So just download it an try it out:)... The only drawback is, that for simple NamedRange bindings you have to manually call the Databindings[index].WriteValue() method... but that's what I'll fix with my framework:))**


 


For me that's a perfect way of decoupling your code form the actuall document. And if you know mark the **\_Persons** member of the sample above with the **public** access modifier and the **[Cached]** attribute, you even can de-serialize the data on the server using the ServerDocument class.


 


Just great stuff. And I think I'll add some more "convenience support" for it to my framework.


 


**Also when you go this way you are on the perfect way to stay compatible with Office 12 and VSTO v3 - which is aboslutely essential...**


[ObjectBinding.zip](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Components.PostAttachments/00/00/53/85/62/ObjectBinding.zip?raw=true)


