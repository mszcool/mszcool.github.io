---
layout: post
title: ASP.NET MVC Demo Application – Austrian Re-MIX Conference from October 1st 2009 – Updating (complex) object hierarchies using ModelBinder
date: 2009-10-02 12:17:16
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Yesterday we had our Austrian version of the Re-MIX conference in the Hilton Hotel at Stadtpark in Vienna. It was a great conference combined with a superb architect forum together with Simon Guest, who unveiled his thoughts on cloud computing… but more on that in a separate post, this one is about the ASP.NET MVC session I delivered in the late afternoon in the web dev track!

 **An ASP.NET MVC Walkthrough & Updating object hierarchies…**

 In my session “Introducing the ASP.NET MVC Web Development Framework” inspired by [Phil Haack’s](http://haacked.com/) session at the last MIX-conference I demonstrated, what development with the ASP.NET MVC framework looks like.

 **[Download the sample here](http://www.mszcool.at/blog/2009/20091001-RemixDemoApp.zip)**

 **[Download the slides here](http://www.mszcool.at/blog/2009/20091001-Remix_ASPNET_MVC_Session.pptx)**

 In the session I created a simple application for displaying, modifying and adding events with event deliveries, from scratch. The following graphic shows the entity data model we’ve been working with:

 [![image](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/ASP.NETMVCDemoApplicationAustrianReMIXCo_FBA7/image_thumb.png?raw=true?raw=true "image")](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/ASP.NETMVCDemoApplicationAustrianReMIXCo_FBA7/image_2.png?raw=true?raw=true) 

 As you can see, a single Event has multiple deliveries at different locations. While implementing the controller actions for adding new events and modifying existing events I was challenged by the question, “how the model binder updates object hierarchies”. And the point is – it doesn’t, automatically…

 Based on [Phil Haack’s tip on binding collections on his blog](http://haacked.com/archive/2008/10/23/model-binding-to-a-list.aspx) and based on [a posting from Graham O’Neale here](http://goneale.com/2009/07/27/updating-multiple-child-objects-and-or-collections-in-asp-net-mvc-views/) I completed my idea in this example. Below are the steps for combining the concepts from the previously mentioned blog entries:

 **1.) In my views for Editing and Adding events, add HTML and script - a table that will be dynamically expanded with new items through JavaScript for each new child-item I want to create (event delivery in my case – the parts in red are the important ones in the view):**

 <fieldset>   
    <legend>Fields</legend>   
    <p>   
     <label for="Title">Title:</label>   
     <%= Html.TextBox("Title") %>   
     <%= Html.ValidationMessage("Title", "*") %>   
    </p>   
    <p>   
     <label for="Description">Description:</label>   
     <%= Html.TextBox("Description") %>   
     <%= Html.ValidationMessage("Description", "*") %>   
    </p>   
</fieldset> 

 **<table id="DeliveryTable">   
    <tr>   
        <th>   
            Delivery Begin Date   
        </th>   
        <th>   
            Delivery End Date   
        </th>   
    </tr>   
</table>** 

 <p>   
**<input type="button" value="Add Delivery"** **onclick="javascript:AddDelivery()" />** <br />   
    <input type="submit" value="Create" />   
</p> 

 **<script type="text/javascript" language="javascript">** 

 **function AddDelivery() {** 

 **// Add a row to the table   
    var DeliveryTableBody =    
     document.getElementById****(   
      "DeliveryTable").getElementsByTagName(   
        "tbody").item(0);   
    var ItemCount =   
       DeliveryTableBody.childNodes.length - 1;** 

 **var newChild = document.createElement("tr");   
    var col1 = document.createElement("td");   
    col1.innerHTML =    
        "<input type='text'    
         name='Event.Deliveries["    
          + ItemCount + "].BeginDate' />";   
    var col2 = document.createElement("td");   
    col2.innerHTML = "<input type='text'    
         name='Event.Deliveries[" + ItemCount +    
         "].EndDate' />";   
    newChild.appendChild(col1);   
    newChild.appendChild(col2);   
    DeliveryTableBody.appendChild(newChild);   
}** 

 **</script>**

 **2.) In my action method for adding events in the EventController I simply was required to update the signature of the action-method with the Form-postback-collection and a separate call to update model as shown below:**

 [Authorize]   
[AcceptVerbs(HttpVerbs.Post)]   
public ActionResult Add(   
       [Bind(Exclude="EventId")] Event ev,  **FormCollection postValues**)   
{   
    //   
    // Validate incoming stuff   
    //   
    if (string.IsNullOrEmpty(ev.Title))   
        ModelState.AddModelError("Title",   
                    "Title cannot be empty!");   
    if (string.IsNullOrEmpty(ev.Description))   
        ModelState.AddModelError("Description",   
        "Please enter a description for your event!");   
    //   
    // Validation succeeded?   
    // If yes, add the event, otherwise   
    // return to the view   
    //   
    if (ModelState.IsValid)   
    {   
        DataContext.AddToEvent(ev);   
 **UpdateModel<IEnumerable<EventDelivery>>(   
             ev.Deliveries, "Event.Deliveries");**        DataContext.SaveChanges(); 

         return RedirectToAction("Index");   
    }   
    else   
    {   
        return View();   
    }   
}

 **3.) When modifying existing items, the whole thing gets more tricky when working with the ADO.NET Entity Framework. Why? Simply because the ASP.NET MVC default model binder, when updating collections, unfortunately clears them and re-fills them with the items posted back in the Form postback-collection. That breaks the ADO.NET Entity Framework association set (the ASP.NET MVC model binder does not know about associations). Now you have two possibilities, either modify the source code of ASP.NET MVC (if possible I would stay with the thing as it gets delivered) or apply a little trick as I did. I just created a temporary Event, filled it from scratch with the postback-parameters and then copied the values to the existing and to new items as below. Of course the whole thing needs refinement (especially when mapping to existing items in collections), but I think it’s at least a starting point!**

 [Authorize]   
[AcceptVerbs(HttpVerbs.Post)]   
public ActionResult Edit(int eventId,   
                 **FormCollection postValues**)   
{   
    Event CurrentEvent =    
    (from e in DataContext.Event.Include("Deliveries")   
     where e.EventId == eventId   
     select e).FirstOrDefault(); 

     if (CurrentEvent != null)   
    {   
**Event Temp = new Event();   
        UpdateModel(Temp, "Event",    
           new string[] { "Title",   
                          "Description" });   
        UpdateModel(Temp.Deliveries,    
          "Event.Deliveries",    
          new string[] { "BeginDate",   
                         "EndDate" });   
        CopyEvent(CurrentEvent, Temp);** 

         DataContext.SaveChanges(); 

         return RedirectToAction("Index");   
    }   
    else   
    {   
        ModelState.AddModelError("General", "...");   
        return View();   
    }   
}

 I hope that’s a helpful posting for anyone who’s facing the same challenges when working with MVC:) Nevertheless, for those who want to have full control over HTML and want to have better testing possibilities, this framework is the right choice. If you’re rather the “productivity-kind-of-developer”, sticking with web forms might be the more convenient solution...


