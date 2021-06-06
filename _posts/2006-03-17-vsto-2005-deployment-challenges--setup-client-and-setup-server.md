---
layout: post
title: VSTO 2005 Deployment Challenges - Setup Client and Setup Server
date: 2006-03-17 11:24:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

This is a little follow-up from the VSTO workshop I did in Denmark on February 23rd and 24th. The two big questions I got of course where about deploying VSTO 2005 solutions:


* How can we setup client-security for our solutions?
* When using Visual Studio 2005 to publish, in many cases developer have to prepare publishing directories on their developer machines and then just copy them to product servers for their customers. But when publishing with Visual Studio on the developer machine, all the manifests point to the location on the local machine. How can we fix that?


Finally I have created a sample project demonstrating the capabilities of the Code Access Security object model to perform client side deployment and the capabilities of the ServerDocument for deployment on the server-side with updating deployment manifest locations in the documents.


**WARNING:**


For simplicity I have implemented all in Windows Forms applications which need to be launched manually. Of course all I did in the Windows Forms application usually needs to be done in an MSI installer package (you simply create a setup project with Visual Studio 2005 and then create a custom Installer class just by selecting "Add New Item" - "Installer Class" which then will contain the code I have just used in the Windows Forms application). 


*So rather think of these Windows Forms tools as MSI packages you create for your solution and one MSI package needs to be installed on the server and the other one needs to be installed on the client.*


**Server Side Deployment Package**


To use the VSTO.ServerInstallTool you just publish the VSTO solution with Visual Studio 2005 to your local developer machine. Then you copy and paste the directory with the files created by the Visual Studio deployment wizard to the destination server in the destination directory and call my tool. The tool needs to be ran on the production server and wants you to enter the physical directory on the production server, the name of the virtual directory (only the relative name) and the file name of the application manifest (usually in the root directory of the published directory created by the Visual Studio 2005 publish wizard). Actually the tool does nothing else than iterating through the directory hiearchy and updating manifests for XLS and DOC files:


private

 void ProcessDirectory(string fullPath)  
{  
  try  
  {  
    // Update manifests in all word documents or excel   
    // workbooks  
    DirectoryInfo di = new DirectoryInfo(fullPath);  
    FileInfo[] AllFiles = di.GetFiles();  
    foreach (FileInfo fi in AllFiles)  
    {  
        if (fi.Extension.Contains("xls") || fi.Extension.Contains("doc"))  
        {  
            UpdateDocumentManifest(fi.FullName);  
        }  
    }  
  
    // Now process the other sub directories  
    DirectoryInfo[] SubDirs = di.GetDirectories();  
    foreach (DirectoryInfo sdi in SubDirs)  
    {  
        ProcessDirectory(sdi.FullName);  
    }  
  }  
  catch (Exception ex)  
  {  
    LogText.AppendText(string.Format("\r\n!! Error processing {0} --> {1}",   
                 fullPath, ex.Message));  
  }  
}
private

 void UpdateDocumentManifest(string documentPath)  
{  
  try  
  {  
    if (ServerDocument.IsCustomized(documentPath))  
    {  
        LogText.AppendText(string.Format("\r\n\r\nProcessing {0}...", documentPath));  
        using (ServerDocument doc = new ServerDocument(documentPath))  
        {  
            LogText.AppendText(string.Format("\r\n-) Old path: {0}",   
                 doc.AppManifest.DeployManifestPath));  
            doc.AppManifest.DeployManifestPath = string.Format("{0}/{1}",   
                 DestinationUrl, AppManifestText.Text);  
            LogText.AppendText(string.Format("\r\n-) New path: {0}",   
                 doc.AppManifest.DeployManifestPath));  
            doc.Save();  
            doc.Close();  
        }  
        LogText.AppendText(string.Format("\r\nSuccessfully processed {0}...", documentPath));  
    }  
    else  
    {  
        LogText.AppendText(string.Format("\r\nDocument '{0}' is not customized!", documentPath));  
    }  
  }  
  catch (Exception ex)  
  {  
      LogText.AppendText(string.Format("\r\n!! Error processing {0} --> {1}",   
                 documentPath, ex.Message));  
  }  
}
**Client Side Deployment Package**


On the client actually **only the Code Access Security Policy** needs to be setup. In environments with Active Directory I'd definitely recommend using Active Directory Group Policies for deploying these Code Access Security Policies and put them into the Enterprise Policy Level of Code Access Security configuration. For that purpose you can fire up the "Microsoft .NET Framework Configuration 2.0" in your Administrative Tools, move to the "Runtime Security Policy" node, rigth click it and select "Create Deployment Package". This creates an MSI that you then can deploy through Active Directory Group Policies across clients in your domain.


If no Active Directory is available, you need to provide your own MSI package. Actually using the package created the way above overwrites the complete configuration tree on the target machine. For single-machine installs or for "small" customers this is definitely not what you want. Therefore you must create a tool or MSI package that just creates (and when uninstalling, deletes) the nodes necessary for your single application (in this case VSTO application).


This is exactly what my VSTO.ClientInstallTool in this example is doing. It takes the strong name identifier of the VSTO customization assembly you have created and adds it based on this strong name as full trust assembly to your local configuration. Furthermore it adds a trust entry for the document's location (which is the URL to the server where the document has been deployed to).


The code leverages the Code Access Security Policy object model, as you can see:


private void CreateCodeGroupsCommand\_Click(object sender, EventArgs e)  
{  
  try  
  {  
    // First of all extract the strong name from the assembly  
    // WARNING: this should only be executed on trusted assemblies  
    // This code is really supposed to be used in setup package creation, not in WinForms,   
    // this is really just a sample to demonstrate code, that you would use in a setup project  
    // within Visual Studio in a trusted environment  
    Assembly Asm = Assembly.LoadFrom(AssemblyPathText.Text);  
    AssemblyName AsmName = Asm.GetName();  
    byte[] StrongNameToken = AsmName.GetPublicKey();  
  
    // No create the new code groups  
    // Get the user's node in code access security configuration  
    System.Collections.IEnumerator PolicyEnumerator =   
                                SecurityManager.PolicyHierarchy();  
    while (PolicyEnumerator.MoveNext())  
    {  
        // Get the current policy  
        PolicyLevel pl = (PolicyLevel)PolicyEnumerator.Current;  
        if (pl.Type == PolicyLevelType.Machine)  
        {  
            // Add the code groups to the user's level  
            CreatePolicyLevel(pl, StrongNameToken);  
            // Inform the user  
            MessageBox.Show(this,  
                  "Successfully created security configuration!",  
                  "Security Configuration Completed",  
                  MessageBoxButtons.OK, MessageBoxIcon.Information);  
        }  
    }  
  }  
  catch (Exception ex)  
  {  
    MessageBox.Show("Unable to create code groups on client: " + ex.Message);  
  }  
}
private void CreatePolicyLevel(PolicyLevel pl, byte[] publicKey)  
{  
    // Get the full trust permission set  
    NamedPermissionSet FullTrustSet = null;  
    foreach (NamedPermissionSet nsp in pl.NamedPermissionSets)  
    {  
      if (nsp.Name.Equals("FullTrust"))  
      {  
        FullTrustSet = nsp;  
        break;  
      }  
    }  
    if (FullTrustSet == null)  
      throw new Exception("Unable to find FullTrust permission set!");  
  
    // Create the code group for the strong name assembly  
    StrongNameMembershipCondition SNMembership =   
                    new StrongNameMembershipCondition(  
                        new StrongNamePublicKeyBlob(publicKey), null, null  
                    );  
    PolicyStatement PStmt = new PolicyStatement(FullTrustSet,  
    PolicyStatementAttribute.Exclusive);  
    UnionCodeGroup AssemblyCodeGroup =  
          new UnionCodeGroup(SNMembership, PStmt);  
    AssemblyCodeGroup.Name = "mszCool\_" + Guid.NewGuid().ToString();  
  
    // Create the code group for the document url  
    UrlMembershipCondition UrlMembership =  
            new UrlMembershipCondition(ServerDocumentURL.Text);  
    PolicyStatement DocPStmt = new PolicyStatement(FullTrustSet,   
                                                PolicyStatementAttribute.Exclusive);  
    UnionCodeGroup DocumentCodeGroup =   
                  new UnionCodeGroup(UrlMembership, DocPStmt);  
    DocumentCodeGroup.Name = "mszCool\_" + Guid.NewGuid().ToString();  
  
    // Add the code groups to the policy level  
    pl.RootCodeGroup.AddChild(AssemblyCodeGroup);  
    pl.RootCodeGroup.AddChild(DocumentCodeGroup);  
  
    // Save the configuration  
    SecurityManager.SavePolicyLevel(pl);  
}


Again, I'd really recommend putting this code into MSI packages which at the end is the exactly the same except that it is called from within an Installer class and that it either gets information from the user during setup or gets the information automatically within the build process of your application.


The ServerDocument actually can be used for another nice use case: it is a really good mechanism for updating deployment locations in existing documents of your users. Finally with the two object models introduced in this blog post, you are ready to got for deploying VSTO based solutions. 


**I have added the samples in the ZIP file in the ATTACHMENT OF THIS POST. So if you want to take a closer look at the code, just download, extract and go for it.**


[VSTOTestDeploy.zip](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Components.PostAttachments/00/00/55/38/15/VSTOTestDeploy.zip?raw=true)


