---
layout: post
title: Windows Communication Foundation and SOAP with Attachments Message Encoder built in Interop-Lab with SVC (Sozialversicherungs-Chipkarten Betriebs- und Errichtungsgesellschaft m.b.H.))
date: 2009-10-19 00:00:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

As some of you know I am the founder and owner of the Austrian Microsoft Interoperability Council. The council itself is a group of experts and influencers on the Austrian market that is working together with us for identifying interoperability challenges between the Microsoft-platform and other platforms based on local, regional and global standards.

 One of the aforementioned local standards in Austria is the interface for programmatically accessing and using services offered by the Austrian National Insurance through their electronic health care card (e-card). These interfaces offer a variety of services that allows medical software to integrate with offerings from the Austrian National Insurance. Essentially these services are built as a set of web services that can be called from medical applications. Using these web services from the Microsoft-platform (and from other platforms) is essentially very easy - with two exceptions!

 Two services allow uploading and downloading electronic documents as attachments to the actual SOAP-message. Examples are electronic referrals or Medication Approval Service (= special medication has to be approved by controlling doctors of health insurance institutions). These web-services use [SOAP-With-Attachments](http://www.w3.org/TR/SOAP-attachments) for adding the electronic documents as attachments to the actual web service message. SOAP-With-Attachments is an older standard for attaching binary data to web service messages which is still valid. It gets replaced slowly by newer standards such as [MTOM](http://www.w3.org/TR/soap12-mtom/). Therefore the current version of WCF and future versions of WCF do not support SOAP-With-Attachments - WCF comes with an MTOM message encoder, only.

 The missing encoder affects every doctor software and hospital software vendor of the Austrian market that builds his software on top of the Microsoft-platform incl. .NET. And most of the vendors are building their doctor software on top of the Microsoft-platform; therefore they are all affected if they don't want to parse messages manually.

 Together with the company that is running and building the services for the Austrian National Insurance, [SVC Chipkarten Betriebs und Errichtungsgesellschaft](http://www.svc.co.at/) m.b.H., we organized an interoperability lab within our interoperability initiative to solve this problem for the Austrian doctor- and hospital-software vendors. For doing so, in this lab together we built a SOAP-With-Attachments message encoder sample for WCF and tested it against the affected services together with the e-card.

 Of course, based on the principles of our interoperability council and initiative, we published the results of this interoperability lab as open source samples on the Microsoft codeplex-platform. 

 [Click here to go to the Codeplex workspace and download the sources.](http://wcfswaencoder.codeplex.com/)

 Please note that these components are sample-components, therefore using and building on-top of these components is at one's own risk and there will be no official support by SVC or Microsoft.

 **Using the Encoder**

 Using the encoder is pretty simple. First of all you need to add a reference to the encoder project we're providing in this workspace. After you've done that you can configure your WCF-service to use the encoder. Note that both ends, the service and the client, need to use the same encoder! Here is some sample configuration

   
```
 1: <configuration>
```



```
 2:   <system.serviceModel>
```



```
 3:     <extensions>
```



```
 4:       <bindingElementExtensions>
```



```
 5:         <add name="swaMessageEncoding"
```



```
 6:              type="Microsoft.Austria.WcfHelpers.SoapWithAttachments.SwaMessageEncodingElement, 
```



```
 7:                    Microsoft.Austria.WcfHelpers.SoapWithAttachments" />
```



```
 8:       </bindingElementExtensions>
```



```
 9:     </extensions>
```



```
 10:     <bindings>
```



```
 11:       <customBinding>
```



```
 12:         <binding name="SwaTestBinding">
```



```
 13:           <swaMessageEncoding innerMessageEncoding="textMessageEncoding">
```



```
 14:           </swaMessageEncoding>
```



```
 15:           <httpTransport maxBufferSize="62914560"
```



```
 16:                          maxReceivedMessageSize="62914560"
```



```
 17:                          authenticationScheme="Anonymous"
```



```
 18:                          proxyAuthenticationScheme="Anonymous"
```



```
 19:                          useDefaultWebProxy="true" />
```



```
 20:         </binding>
```



```
 21:       </customBinding>
```



```
 22:     </bindings>
```



```
 23:     <client>
```



```
 24:       <endpoint address="http://localhost/fakeservice/"
```



```
 25:                 binding="customBinding"
```



```
 26:                 bindingConfiguration="SwaTestBinding"
```



```
 27:                 contract="IMyContract" name="MyServiceEndPointName" />
```



```
 28:     </client>
```



```
 29:   </system.serviceModel>
```



```
 30: </configuration>
```



After you have done that you can leverage the OperationContext of WCF to attach binaries to outgoing messages or retrieve binaries received by incoming messages. The most important **for clients** is, that you need to setup an operation-context before calling a service with your generated proxy as follows:





```
 1: using (OperationContextScope Scope = 
```



```
 2:         new OperationContextScope(Client.InnerChannel))
```



```
 3: {
```



```
 4: }
```



Note that these lines-of-code are necessary for clients, only, as in your services you usually have an OperationContext available, already. After you have your operation context, it is as simple as follows to attach binaries to outgoing messages or get binaries from incoming messages:





```
 1: // Add outoing attachment
```



```
 2: OperationContext.Current.OutgoingMessageProperties
```



```
 3:                          [SwaEncoderConstants.AttachmentProperty] = ZipContents;
```



```
 4:  
```



```
 5: // Receive incoming attachment
```



```
 6: byte[] b = (byte[])OperationContext.Current.IncomingMessageProperties[
```



```
 7:                                          SwaEncoderConstants.AttachmentProperty];
```



Note, that my implementation currently supports single attachments, only. But of course as this is an open source sample feel free to download the source code and extend it as you need it.


**Final Words**


In my opinion this is a great result that can be achieved simply through an open discussion and by exchanging experience, idea and knowledge in our interoperability council. This work was an exceptional partnership that (hopefully) helps hundreds of medical software vendors - this is the kind of impact I love to see out of the interop-initiative we're running in Austria - results that help a large part of the local software market!


