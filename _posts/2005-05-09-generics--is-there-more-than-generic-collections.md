---
layout: post
title: Generics - Is there more than Generic Collections??
date: 2005-05-09 01:19:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

I am currently preparing my developer track talks for the [local Big>Days 2005 road show](http://www.microsoft.com/austria/bigdays) and - oh what a surprise - the key topic for the dev-track will of course be a lap around important new things in Visual Studio 2005 and .NET 2.0.  
Well and currently I am thinking about a useful demo for Generics ... and guess what ... I don't want to tell the people that they should create their own generic collections. That's not only boring, I think Generics exist that people don't have to write their own collections anymore because they already exist in the framework and, due to the fact they are generic, they can be used in any different situation. Okay, so when writing my own collection for my "Person" class or what so ever is over, what are some other additional usecases.


I mean I have found several really cool blog entries and articles for this topic:


[http://blogs.msdn.com/grantri/archive/2004/12/31/344967.aspx](https://blogs.msdn.com:443/grantri/archive/2004/12/31/344967.aspx)  
[http://blogs.msdn.com/kcwalina/archive/2004/03/15/89860.aspx](https://blogs.msdn.com:443/kcwalina/archive/2004/03/15/89860.aspx)  
<http://weblogs.asp.net/cnagel/archive/2005/03/10/391616.aspx>  
<http://weblogs.asp.net/cnagel/archive/2005/03/06/386147.aspx>  
<http://weblogs.asp.net/cnagel/archive/2005/03/04/385077.aspx>  
[http://blogs.msdn.com/joelpob/archive/2004/11/17/259224.aspx](https://blogs.msdn.com:443/joelpob/archive/2004/11/17/259224.aspx)  
<http://www.codeguru.com/Csharp/.NET/net_framework/microsoftnamespace/article.php/c9661/>  
<http://www.artima.com/intv/generics.html>  
<http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dnvs05/html/csharp_generics.asp>


Wow, just a huge repertoire of information. But what the hack do all these things tell me for real world line-of-business applications? Nothing! A Stack is not a problem I want to solve while creating business-applications! And a binary tree is something I don't want to create in a business application as well! These are just things that should exist in the framework. Most, oh, no, all of these examples always write about utility classes or base-framework classes – something that you might use in LOB-applications but don’t write yourself. Honestly I don't believe that my SuperBestGenericList class will be better than the one implemented in the framework. So why are we all talking about generics if just a minority will really find them useful? I mean the generic collections are definitely great but from the "value"-standpoint I think it's not ***the huge*** improvement, isn't it?


So what are line-of-business use cases? Where are real advantages of generics for LOB applications that are more than just "self-evident"? When started thinking about that I had the following idea: generic inheritance if you want to create a base-class with “façade-functionality” - another thing that makes life just easier. Just think about a ***simple*** example: you have a distributed application and you middle-tier consists of a couple of business classes implementing a typical CRUD pattern. I just want to ensure that in every of the methods a security-checks are performed before the actual operation takes place. This check may not be forgotten accidentally: therefore the base class calls them automatically in the CreateDocument, ReadDocument, DeleteDocument and UpdateDocument and afterwards it calls a CreateDocumentInternal, ReadDocumentInternal,… which must be implemented by the derived classes. With the current version of .NET I’d implement this as follows:


public abstract class BusinessBase  
{  
    public object CreateDocument()  
    {  
        if (CheckSecurity("Create"))  
        {  
            return CreateDocumentInternal();  
        }  
        else  
        {  
            throw new System.Security.SecurityException("...");  
        }   
    }  



    public object ReadDocument(object key)  
    {  
        if (CheckSecurity("Read"))  
        {  
            return ReadDocumentInternal(key);  
        }  
        else  
        {  
            throw new System.Security.SecurityException("...");  
        }  
    }  



    public void UpdateDocument(object document) { /*...*/ }  
    public void DeleteDocument(object key) { /*...*/ }  
 




    public bool CheckSecurity(string opCode)  
    {  
        // Your logic goes here...  
        return false;  
    }  
 




    public abstract object CreateDocumentInternal();  
    public abstract object ReadDocumentInternal(object key);  
    public abstract void UpdateDocumentInternal(object document);  
    public abstract void DeleteDocumentInternal(object key);  
}



What I don’t like with that are many type-checks and type-casts in both – the inherited classes as well as the classes using this object model. It leads to unreadable code and adds potential type-mistakes to your code. As soon as it comes to concrete implementations I don’t want to have things like this:



public class MyCustomerService : BusinessBase  
{  
    public override object CreateDocumentInternal() { /* ... */ }  
    public override object ReadDocumentInternal(object key) { /* ... */ }   
    public override void DeleteDocumentInternal(object key) { /* ... */ } 




    public override void UpdateDocumentInternal(object document)  
    {


**// Argh!! Type checks and casts more and more...**  
        if (**document is MyCustomer**)  
        {  
            **MyCustomer customer = (MyCustomer)document;**  
 

  
            // ...  
        }  
        else  
        {  
            throw new ArgumentException("Invalid business document!");  
        }  
    }  
}


Oh yeah, of course the same applies to the usage of this **MyCustomerService** class. And if you pass the wrong type you don’t get a compiler-error but your application will result in a runtime error. With generics you actually can solve this problem quite nice – with having both: the advantage of type-safe code and of a generic base class that automatically calls a security check function so that this cannot be forgotten accidentally: 




/// <summary>  
/// Business service base class  
/// </summary>  
/// <typeparam name="D">Document type</typeparam>  
/// <typeparam name="K">Document-identifier type</typeparam>  
public abstract class BusinessBase**<D, K>**  
{  
    public D CreateDocument()  
    {  
        if (CheckSecurity("Create"))


        {  
            return CreateDocumentInternal();  
        }  
        else  
        {  
            throw new System.Security.SecurityException("...");  
        }   
    }  
 

  
    public **D** ReadDocument(**K** key)  
    {  
        if (CheckSecurity("Read"))  
        {  
            return ReadDocumentInternal(key);  
        }  
        else  
        {  
            throw new System.Security.SecurityException("...");  
        }  
    }  
 

  
    public void UpdateDocument(**D** document) { /*...*/ }  
    public void DeleteDocument(**K** key) { /*...*/ }  
 

  
    public bool CheckSecurity(string opCode)  
    {  
        // Your logic goes here...  
        return true;  
    }  
 

  
    public abstract **D** CreateDocumentInternal();  
    public abstract **D** ReadDocumentInternal(**K** key);  
    public abstract void UpdateDocumentInternal(**D** document);  
    public abstract void DeleteDocumentInternal(**K** key);  
}  
 

  
public class MyCustomerService : BusinessBase**<MyCustomer, string>**  
{  
    public override **MyCustomer** CreateDocumentInternal() { /* ... */ }  
    public override **MyCustomer** ReadDocumentInternal(**string** key) { /* ... */ }   
    public override void DeleteDocumentInternal(**string** key) { /* ... */ }


    public override void UpdateDocumentInternal(**MyCustomer** document)


    {  
**// Cool, no type-checks and casts  
        // Now the compiler is doing that for me  
        // Hey, that means no runtime errors anymore :)**  
        string id = document.Id;  
        // Do something...  
    }  
}



I think this is a useful use case even for LOB applications. But my problem is – I think this is too complicated for a road show with the title “lap around Visual Studio 2005”. If anyone out there has some other useful use cases (and I am not talking about Stack, Tree or something like that so please don’t bother me with them, I can’t here that anymore) feel free posting them here... I am really happy for any suggestion. Oh well, and if you have a similar useful use case for anonymous methods as well, tell me so... Thanks a lot ;-)


