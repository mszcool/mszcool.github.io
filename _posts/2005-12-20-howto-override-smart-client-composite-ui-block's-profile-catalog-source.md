---
layout: post
title: How-To - Override Smart Client Composite UI Block's Profile Catalog Source
date: 2005-12-20 12:00:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Wow, my last blog is sometime ago. Therefore you even don't know that one week after my first flight experience (my last blog entry) I had the second one:). They just canceled my flight to Netherlands for the Office 12 Ascend Dry Run. But finally KLM saved my life. Okay, let's get back to something technical. The [Composite UI Block (CAB)](http://msdn.microsoft.com/practices/default.aspx?pull=/library/en-us/dnpag2/html/cab.asp) is finally released and after that I built a little sample to modify the behavior for loading Modules and Module security information.


Actually the profile is a very powerful capability of CAB. It allows you to profiles for your smart client applications to define which groups of users will have which Modules (and therefore which Workitems, Views and Controllers) available in their application. CAB then automatically does load or doesn't load the WorkItems of a Module which means it automatically displays or hides UI elements as well as functionality of the WorkItems associated with a Module. By default CAB takes this information from a file called ProfileCatalog.xml which looks similar to the following:


<?

xml version="1.0" encoding="utf-8" ?>  
<SolutionProfile xmlns="http://schemas.microsoft.com/pag/cab-profile" >  
    <Modules>  
        <ModuleInfo AssemblyFile="BankTellerModule.dll" />  
        <ModuleInfo AssemblyFile="CustomerMapExtensionModule.dll" />  
    </Modules>  
</SolutionProfile>
The ProfileCatalog.xml above is the sample catalog from the BankTeller Quickstart sample. But of course having the profile stored in a file on the client is not really what we want for real-world scenarios. The configuration might be stored on a backend server so that administrators can configure the profiles for the different users centrally. Everytime when the application starts-up running with a specific user it goes to the server to retrieve information telling it, which modules needs to be loaded for the currently logged in user. Therefore the desktop of the Smart Client application appears differently based on the user currently logged on and on the permissions a user has. To tell CAB on the client, that it should retrieve this information from another server, you just need to understand the basic relationship between the CAB framework's classes.


Basically the part of CAB that reads the profile information is a CAB service component registered by default. This CAB service component implements the interface IModuleEnumerator. Therefore all you need to do is creating a class that implements the interface as well as follows:


public

 class WebServiceModuleEnumerator : IModuleEnumerator  
{  
  private ModuleInfoProxy.ModuleInfo Proxy = new BankShell.ModuleInfoProxy.ModuleInfo();
  public Microsoft.Practices.CompositeUI.Configuration.IModuleInfo[] EnumerateModules()  
  {  
    // Do some work here  
  }  
}
Within the EnumerateModules all you have to do is returning all the modules including detailled information about each module like AssemblyName, UpdateLocation and the AllowedRoles string array. The allowed roles string array specifies, which groups of users are allowed to use a module. By default you have to specify Windows groups for the allowed groups. I'll explain in a later blog entry, how you can customize security related information as well. For the while suppose you have a web service delivering the module info which looks similar to the following:


using System;xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


using System.Web;


using System.Collections;


using System.Web.Services;


using System.Web.Services.Protocols;


 




/// <summary>


/// Configured Module Response Structure


/// </summary>


public class ConfiguredModuleInfo


{


    public string AssemblyFile;


    public string UpdateLocation;


    public string[] AllowedRoles;


}


 




/// <summary>


/// Summary description for ModuleInfo


/// </summary>


[WebService(Namespace = "http://tempuri.org/")]


[WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1\_1)]


public class ModuleInfo : System.Web.Services.WebService


{


 




    public ModuleInfo()


    {


 




        //Uncomment the following line if using designed components 


        //InitializeComponent(); 


    }


 




    [WebMethod]


    public ConfiguredModuleInfo[] GetRoleConfiguration()


    {


        ConfiguredModuleInfo[] m = new ConfiguredModuleInfo[1];


 




        m[0] = new ConfiguredModuleInfo();


        m[0].AssemblyFile = "BankTellerModule.dll";


        m[0].UpdateLocation = "";


 




        if (m.Length == 2)


        {


            m[1] = new ConfiguredModuleInfo();


            m[1].AssemblyFile = "CustomerMapExtensionModule.dll";


            m[1].UpdateLocation = "";


        }


 




        return m;


    }


 




}


This web service just creates a response message containing an array of modules returned to the client. In your IModuleEnumerator class on the client you can now call the web service to retrieve the information from the server as follows:


public class WebServiceModuleEnumerator : IModuleEnumerator


{


    private ModuleInfoProxy.ModuleInfo Proxy = new BankShell.ModuleInfoProxy.ModuleInfo();


 




    #region IModuleEnumerator Members


 




    public Microsoft.Practices.CompositeUI.Configuration.IModuleInfo[] EnumerateModules()


    {


        // Get the configured module information


        Proxy.Credentials = System.Net.CredentialCache.DefaultCredentials;


        ModuleInfoProxy.ConfiguredModuleInfo[] m = Proxy.GetRoleConfiguration();


 




        // Create the return type structure


        int i=0;


        ModuleInfo[] info = new ModuleInfo[m.Length];


        foreach (ModuleInfoProxy.ConfiguredModuleInfo SingleInfo in m)


        {


            info[i] = new ModuleInfo(SingleInfo.AssemblyFile);


            info[i].SetUpdateLocation(SingleInfo.UpdateLocation);


            if (SingleInfo.AllowedRoles != null)


            {


                foreach (string s in SingleInfo.AllowedRoles)


                {


                    info[i].AllowedRoles.Add(s);


                }


            }


 




            i++;


        }


 




        return info;


    }


 




    #endregion


}


Last but not least to make sure that CAB calls the whole thing, you have to register your IModuleEnumerator in the application's configuration as follows.


<?xml version="1.0" encoding="utf-8" ?>


<configuration>


   ...


   <CompositeUI>


      <!--<visualizer>


         <add type="SampleVisualizations.WorkItemVisualization, SampleVisualizations" />


      </visualizer>-->


      <services>


         <add


            serviceType="Microsoft.Practices.CompositeUI.Services.IStatePersistenceService, Microsoft.Practices.CompositeUI"


            instanceType="Microsoft.Practices.CompositeUI.Services.IsolatedStorageStatePersistenceService, Microsoft.Practices.CompositeUI" />


 




***<******add******serviceType******=******"******Microsoft.Practices.CompositeUI.Services.IModuleEnumerator, Microsoft.Practices.CompositeUI******"***


***instanceType******=******"******BankShell.WebServiceModuleEnumerator, BankShell******"*** ***/>***


      </services>


   </CompositeUI>        ...


</configuration>



Finally that's it. With these steps you have customized CAB's behavior of reading profile information from another location than ProfileCatalog.xml. In one of my next blog entries I'll take a look at the abilities of CAB for overriding authenticated user information and role information...


