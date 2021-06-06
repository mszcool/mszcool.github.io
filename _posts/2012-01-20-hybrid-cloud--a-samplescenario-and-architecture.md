---
layout: post
title: Hybrid Cloud - a sample-scenario and architecture
date: 2012-01-20 14:41:38
categories: msdnblogarchive
tags: Archive MSDNBlog
---

In my [previous posting](http://blogs.msdn.com/b/mszcool/archive/2011/12/30/hybrid-cloud-integrated-with-on-premise-value-thoughts-technical-options.aspx) I outlined a number of thoughts, considerations and technical options for building hybrid-cloud solutions. This types of applications and services operate and run assets on public cloud platforms such as Windows Azure while keeping other assets in private data centers on-premises. That way such kinds of solutions enable you to leverage the benefits of public cloud platforms while at the same time keeping assets you cannot or do not want to move to a public cloud on-premises in your own data center.

 The Windows Azure  Platform offers a strong set of services and capabilities that do enable hybrid cloud scenarios. These platform-services do enable an effective and efficient integration of assets running in the public cloud with assets you keep on-premises. Some of these options do even enable this integration without storing data you cannot or do not want to store in the cloud, at all. In the [mind-map](https://skydrive.live.com/?cid=d37c9d7bfbce8418&id=D37C9D7BFBCE8418%21295&sff=1) I’ve published through [my last posting](http://blogs.msdn.com/b/mszcool/archive/2011/12/30/hybrid-cloud-integrated-with-on-premise-value-thoughts-technical-options.aspx), the most important of these services are summarized and outlined.

 With this posting it’s time to get more specific. Together with one of my peers from Microsoft consulting services Western Europe, [Andy Rynes](http://blogs.technet.com/b/arynes/), I’ve been thinking about an easy-to-understand yet comprehensive scenario to outline the possibilities, options and applications of the concepts I’ve outlined [in the previous posting](http://blogs.msdn.com/b/mszcool/archive/2011/12/30/hybrid-cloud-integrated-with-on-premise-value-thoughts-technical-options.aspx) supplemented by dynamic private cloud management concepts. Explaining the architecture and application of hybrid cloud integration-points is subject of this posting so that you get a practical understanding based on a true scenario.

 **Online Shop with integrated Payment and Account Management**

 Consider a typical architecture for an online-shopping system if you would design it for being operated in your own data center on-premises! Typically (and simplified) such a system includes the following components and services:

 * Databases typically used for such a system
	+ Credentials store (with user names and passwords)
	+ Payment accounts (with credit card data, billing data etc.)
	+ Products and orders (containing a list of products and orders placed by customers)
* Services that do encapsulate the business logic
	+ Authentication services and components
	+ Payment services and payment account services
	+ Order processing integrated with the shop-front-end
* Front-end services typically offered on a shop-system
	+ Account and credentials management
	+ Payment account management to update billing and credit card data
	+ The online shop front-end

 The following figure summarizes, how these assets are combined in the simplified architecture I use for ongoing discussions in this and future posts.

 [![2012-01-20 Hybrid Cloud Scenario 01](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/32/26/metablogapi/7026.2012-01-20-Hybrid-Cloud-Scenario-01_71975C2B.png?raw=true "2012-01-20 Hybrid Cloud Scenario 01")](https://skydrive.live.com/redir.aspx?cid=d37c9d7bfbce8418&resid=D37C9D7BFBCE8418!298&parid=D37C9D7BFBCE8418!297)

 Note: the figure above does not specify the means of communication between the the web front-ends accessed through the browser and the services. But let’s assume the following practical and realistic assumptions:

 * **Asynchronous processing via Queues:**
+ Order processing: processing orders definitely can and will happen asynchronously; therefore calls between the web shop application and the order processing service will be rather asynchronous through e.g. queues.
+ Payment processing: also payment processing will be rater asynchronous, therefore typically for interacting with such services you will choose asynchronous mechanisms such as queues there, as well.

* **Request/response based service calls:**
+ Managing the accounts with credit card information and billing-data is an interactive process with the user implemented through the account management web front-end.
+ As these are interactive, the calls from the account management web to the account management service will be rather request/response-based service calls.

 **Hybrid-scenario – Assets to be moved to the cloud and to keep on-premises**

 It is obvious that a web shop including its order processing can benefit from characteristics of public cloud platforms such as elastic scalability. These assets of such a solution can definitely benefit from time-bound increase of resources during busy shopping seasons such as Christmas while at the same time do not need a lot of resources during seasons with less shopping-activity such as summer holiday season.

 On the other hand in many countries personal billing and payment information are treated as very sensitive. Therefore in some scenarios you might want or have to keep them on-premises in your own data center.

 Finally this situation leads to a hybrid cloud scenario where you definitely benefit from putting the assets of the front-end web shop including the order processing in the cloud while leaving your payment and billing account management services on-premises in your own data center. Based on the assumption above this simple scenario leads to two different integration steps from assets hosted in the public cloud to on-premises operated assets:

 * **Integration with the payment service:**   
Typically payments to not get processed immediately. All information a user gets when placing a payment as part of the check-out process is, whether the placement of the payment has been accepted or not. The actual processing happens asynchronously and at a later point-in-time. Therefore the choice for integrating the shop-services and –application hosted in the public cloud with the on-premises payment-service are:
+ use Azure Queues (or Service Bus Queues) as integration mechanisms
+ if you thing data is really sensitive, encrypt the information
+ use a public/private key pair whereas the public key for encryption resides in the public cloud and the private key for decryption resides in your on-premises data center.   


* **Integration with the on-premises account management service**In this case you are faced with an interactive scenario. The user actively requests, reads and modifies information on his billing-account through a user interface and needs immediate response whether changes have been accepted or not. Therefore the options for integration between the web-shop applications- and services hosted in the public cloud and the on-premises payment- and billing-account-management service are:
+ Option #1: open the firewall to your data center and enable access to the on-premises service from the public cloud. If applying IP address restrictions on your firewall you need to be aware, that whenever you re-deploy in Windows Azure the IP address changes. As long as you just do in-place upgrades no IP address changes for your cloud service.
+ Option #2: use the Windows Azure Service Bus Relays to relay the communication to your on-premises service through the service bus. That enables you to call the web service without opening ports on the firewall in a targeted and secure way. More details will come within subsequent posts when digging into some of the details of this scenario.

 Updating the architecture diagram based on the knowledge and approaches outlined above will result in the following refined architecture for our hybrid solution:

 [![2012-01-20 Hybrid Cloud Scenario 02](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/32/26/metablogapi/8422.2012-01-20-Hybrid-Cloud-Scenario-02_762D66E5.png?raw=true "2012-01-20 Hybrid Cloud Scenario 02")](https://skydrive.live.com/redir.aspx?cid=d37c9d7bfbce8418&resid=D37C9D7BFBCE8418!299&parid=D37C9D7BFBCE8418!297)

 As you can see, the (apparently) “sensitive” information and data such as credit cards, billing etc. stays on-premises in your own data centers. For the interactive use case the connection from the public cloud is relayed through Azure service bus relay bindings to the on-premises hosted account management service. In this case the account management service connects from inside out to the service bus to offer its endpoint to other parties authenticated to the same service bus namespace. If you provide those service bus namespace and credentials to the shop-front-end as the only party, no one else will be able to connect to this account management service. 

 Furthermore payments are just stored as encrypted messages on Azure queue. A little adapter application / service that runs on-premises queries this Azure queue in regular intervals to retrieve payment messages and forward them to the actual payment service. A public key is used in the cloud to encrypt messages before they’re put onto the queue. The corresponding private queue is used by the application that monitors the Azure queue for new payment messages to decrypt the payment messages and forward them to the payment service. That way even the payment message that might contain sensitive data is stored in the cloud without having the option of decrypting the message in the cloud. In addition the data is stored temporarily, only, as the messages are deleted from the queue as soon as they’ve been processed on-premises.

 **Unleash the full power – on-premises and public cloud unified**

 Of course it would be really neat if some of the concepts such as dynamic provisioning in case of increased load could also be applied to your on-premises world. In addition having a management solution that allows you to manage both, the cloud and on-premises assets through one, unified tool and infrastructure is an important aspect. That is were you need to take aspects of private cloud into consideration to treat the scenario above as comprehensive and really understand your full potential of possibilities and opportunities.

 **Next steps…**

 I’ll definitely will demonstrate the concepts outlined above with a practical example in one of the next posts in February-timeframe to equip you with a practical example on hybrid cloud solutions.

 In addition I think [Andy](http://blogs.technet.com/b/arynes/) will pick up my post to equip you with details with regards to the architecture of a private cloud infrastructures. This will finally fill the gap and shows you, how to implement dynamic operations efficiency across public and private cloud using the Windows Azure Platform and the on-premises Windows Server and System Center 2012 platform.

 Stay tuned…


