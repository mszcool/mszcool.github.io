---
layout: post
title:  "Windows Azure – Combining PaaS & IaaS to get best of both worlds in your Architecture"
date:   2013-09-26 12:00:00 +0100
categories: wordpressarchive
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

While software vendors are looking to spend less time on infrastructure management by leveraging Platform Services (PaaS), this is not always possible. In this post, I'll talk about combining fully managed platform services with infrastructure services (IaaS) for which customers are having more responsibilities of managing them for an increased level of flexibility.

<!--more-->

Over the past 2 years I have been working with many ISVs (Independent Software Vendors) to get their products and platforms to the Public Cloud on Windows Azure. In almost all cases the requirements and motivations from those ISVs did include one or a combination of the following reasons and/or expectations:

- Expand beyond the own country, get global / international.
- Be able to scale faster and easier with less amount of effort.
- Reduce effort and costs for operations management.

Of course there are many more reasons and motivations why (or why not) an ISV or a company would consider (or not) cloud computing. But these are very common ones.

When looking at those requirements above there’s one piece they do have in common: the ISVs need to spend less time on managing your infrastructure, networking configurations and operating systems (e.g. patching etc.) to be able to be successful. **With such requirements in mind I’d definitely rather look into automatically managed service offerings from Cloud Platforms such as Azure (or in other words: Platform-as-a-Service and Software-as-a-Service). Because with those requirements above you will want to have as much automatic management & setup as possible to achieve your goals.**

**But in practice things are often more difficult...**

How far the goals above can be achieved requires looking detailed at the initial situation of the ISV and his application. In specific the application architecture and identification of which technologies are used in detail is of major relevance. Not all techniques, technologies and approaches might work well in Platform-as-a-Service runtimes such as Windows Azure Web Sites, Mobile Services or Cloud Services (often for a good reason, sometimes because some features are not available, yet). Let’s look at a typical example architecture we see most often with software vendors nowadays:

![image01](/images/posts2013/20130926 - VM-Cloud-Service-Mix (1).png)

As you can see, we do have an ASP.NET MVC web front-end, some services performing more complex computational or IO-intensive tasks in the background, a database cluster (for high-availability) and a storage-system for documents, videos and other binary data. Looking at it, the naive mapping for Azure could work as follows with pure Platform-as-a-Service and ready-to-use services (such as Azure storage). That way we would not have to deal with any kind of traditional operations management at all – a truly nice vision and in my opinion something that always should be on a long-term roadmap:

| **Component** | **Windows Azure Service** |
| ASP.NET MVC Application | Web Sites or Cloud Services |
| Computational background process | Cloud Services with Worker Roles |
| SQL Server Cluster | Azure SQL Database |
| Storage Cluster | Azure BLOB Storage |

Looks pretty simple and would be great if it would always be that easy. In practice we need to look at each component to see, if it is doing or making use of something that is not built for working in Platform-as-a-Service environments. If there’s nothing like that, definitely go for it because you’ll benefit most from the Cloud and Azure then. If you have challenges we need to consider alternatives: either adopt your product/code base or select another alternative.

And in case of Windows Azure that other alternative to PaaS definitely can be Windows Azure Virtual Machines, which is IaaS (Infrastructure-as-a-Service) on Azure. Let’s look a little bit deeper into the sample architecture above, look at some of the most important questions I typically ask and pick some assumptions for this post.

**Conclusion:** leverage BLOB storage as a ready-to-use service from Azure.Conclusion: Web Sites will not work because of 3rd-party components to be installed, but Cloud Services is a fit as stateless, file storage can be outsourced to Azure BLOB storage.Conclusion: Cloud Services worker are a perfect match since async processing possible and file storage can be easily replaced by BLOB storage.Conclusion: this is the only case where we cannot use the Platform-as-a-Service offering from Azure. We need to fall-back to Infrastructure-as-a-Service and run SQL Server in a Virtual Machine.

| **Component** |**Questions** | **Assumption** |
| Storage Cluster | How good is access to storage encapsulated? Is it spread across all source files or central implementation with e.g. repository pattern? | Let’s assume access to file system is centrally encapsulated in the code base in a repository class. This can be easily exchanged with a BLOB-storage-based implementation. |
| ASP.NET MVC Application | Stateless? Persistent local file storage? Installation of 3rd-party components needed? | For this assume, the app uses 3rd-party components, local file storage and is stateless (load-balancer ready with round-robin algorithm). |
| Computational background process | Windows? Linux? Asynchronous? Persistent local file storage? Installation of 3rd-party components? | Let’s assume the background job runs on Windows, can work asynchronous in the background and has no 3rd-party components needed. |
| SQL Server Cluster | SQL features used? Performance requirements? | Let’s assume our SQL Server database uses .NET CLR procedures and encryption functions. |

### The final architecture – Mixing Virtual Machines and Cloud Services

Since we would like to be as effective and efficient as possible I definitely recommend to use Platform-as-a-Service and Software-as-a-Service where possible. Given the above sample-analysis for this example that’s the case for all components except SQL Server. Finally that leads to the following architecture in Windows Azure:

![image02](/images/posts2013/20130926 - VM-Cloud-Service-Mix (2).png)

### Setting-up the infrastructure in Azure (basic steps)

To setup the architecture above in Windows Azure, you need to follow the subsequent steps in this order. Note that this is just a quick overview, in the next post I’ll give you a detailed step-by-step guide based on an example I’ll publish on my Codeplex workspace.

1. Create an affinity group.
   All networks, virtual machines and cloud services you want to combine through a virtual network MUST be placed into the SAME affinity group.

2. Setup a “Virtual Network” in Windows Azure.
   This network is used for having a private network with subnets in Azure that allows your Cloud Services and Virtual Machines to interact with each other. The nice thing is that as long as you don’t do VPN, this service is free of charge. Also note that the VMs (IaaS-Only, not PaaS) will remain the same IP-addresses assigned inside of the Virtual Network as long as you don’t DELETE the VMs.

3. Create a new Virtual Machine in the network and configure SQL Server.
   After the network is created, create a VM and make sure you add it to the virtual network. After the VM has been created, perform the following steps:
   1. Open up port 1433 in the VM. That enables 1433 communication ONLY INSIDE the Virtual Network. If you also want it available externally, you need to open the port in the endpoint-configuration on the management portal from Windows Azrue.
   2. Configure SQL Server using SQL Authentication (except you also have an AD deployed in a VM in Azure, then you can also use Windows Authentication).
   3. Import your database, create a login with SQL Authentication and make sure to provide it access to the database.
   4. Finally open up a command prompt, type ipconfig and write down the IP address. Note that the address will be constant as long as you don’t delete the VM. Please DO NOT assign a static address since this is not supported in Azure VMs!!

4. Create & deploy a Cloud Service Package for your web site and deploy.
   Finally for your ASP.NET web application (mentioned in the sample above) create a cloud service package, add the network configuration in your “ServiceConfiguration.Cloud.cscfg” XML configuration file. Before publishing make sure that your database connection string points to the IP address you’ve seen for your VM in step 2.

### Final words and more scenarios!!

Windows Azure supports “mixed deployments” that include Virtual Machines (IaaS), Cloud Services (PaaS) as well as other platform services (e.g. storage, media services etc). That enables you to get best of both worlds: the full efficiency, automatic scale and automatic management of PaaS where possible while gaining full control through VMs where needed.

Typical scenarios that are enabled by combining Virtual Machines and Cloud Services on Azure where you run most of your workloads in automatically managed Platform-as-a-Service while running other pieces on VMs where you need full control include:

- Combining your app with Linux-based work-loads because Linux runs in Azure Virtual Machines.
- Special SQL Server requirements that lead to situations where you cannot leverage Azure SQL Database.
- You need to run legacy components in your app that just don’t work inside of PaaS runtimes such as Cloud Services, Web Sites & Co.

With such principles and thoughts you definitely can move much faster to the public Cloud and Windows Azure when you need to! You don’t need to re-write your whole app and use VMs where applicable while moving to PaaS where you think you can benefit most out of it!!
