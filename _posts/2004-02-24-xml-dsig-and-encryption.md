---
layout: post
title: XML DSIG and Encryption
date: 2004-02-24 23:16:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Last week I had a workshop with a customer about XML Schema and XML Security as the core agenda topics. There where two funny things about this workshopxml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" /?


* First it was my first workshop I had to do completely in English (about 9 hours). It was very interesting experience for and showed, that being careful with things like jokes as a non native speaker is of essence because often you miss the last words to get it right and really funnyJ.
* Second there where some very interesting technical topics around XML security. The week before the workshop I started reading through the W3C documents for the XML Digital Signature and XML Encryption standards to get a good understanding of the concepts. But knowing the concepts alone is not all – it was our target to work out, how we can apply XML DSIG and Encryption in the customer’s solution. And then it all starts…


As you probably know, the .NET Framework has classes for creating digital signatures for XML documents using the classes provided in the *System.Security.Cryptography.Xml* namespace (located in *System.Security.dll*). In this namespace you will be able to find a class called **SignedXml** which can be used for the creation of the XML signature. But when it comes to encryption, you will search like hell and find nothing.


The reason for having no classes for XML Encryption included in the .NET Framework is – as I have found out – that the XML Encryption standard has not been finished yet when .NET Framework was released. So we have to use another vehicle for encrypting XML files according to the standard whereas I really don’t want to implement all by myselfJ.


No surprises, the vehicle we can use for getting that, are Web Services Enhancements (although I’d say that this is a workaround – but it’s nice). So what I want to show you now is, how you can leverage the security functionality of WSE without using Web Services at all.


 




***Encrypting and signing XML***


 




The first part of course is the encryption and signing part. To encrypt and sign an XML document you have to do the following things:


* Add a reference to you Microsoft.Web.Services.dll (Web Services Enhancements) to your project.
* Use WSE API to get an X509 Certificate used for signature and encryption or use any other security tokens.
* Load an XML document using DOM and import the node you want to encrypt and / or sign into a new instance of a SOAP envelope.
* Create and include the security header into the SOAP envelope.


 




Take a look at the following code snippet:


 




// Start encrypting the document


XmlDocument doc = new XmlDocument();


doc.Load(...);


 




// Create a soap envelope – WSE works with SOAP envelopes, only


SoapEnvelope env = new SoapEnvelope();


env.CreateBody();


env.CreateHeader();


XmlNode envBody = env.ImportNode(doc.DocumentElement, true);


env.Body.AppendChild(envBody);


 




// Start with the encryption


X509SecurityToken token = new X509SecurityToken(cert);


Microsoft.Web.Services.Security.Security sec = 


      new Microsoft.Web.Services.Security.Security("iaea");


 




sec.Tokens.Add(token);


sec.Elements.Add(new EncryptedData(token));


sec.Elements.Add(new Signature(token));


 




// Append the encrypted data to the header


env.Save("before.xml");


env.Header.AppendChild(***sec.GetXml(env)***);


env.Save(FileName);


 




What you need, when encrypting and signing with WSE is a security header which is bound to the SoapEnvelope. You initialize the parameters for the creation of the security header by adding tokens and elements. The token is nothing else than the security claim, that is used for signature creation as well as encryption – in my example it is an X509 Certificate but you can use any security token you want (custom or UsernameToken).


 




But where is the encryption and signature done here at all? There is no explicit method call and when adding the *EncryptedData* as well as *Signature* element to the security header, the header is not connected to the envelope at all.


 




The interesting line is the *sec.GetXml(env)*. The call to this method actually does the encryption and signature according to the elements and tokens added to the security header, before. Afterwards you can save the SOAP envelope.


 




***Decryption and Signature Validation***


 




Decryption and signature check is as easy as encryption and signing using WSE. All you need is to load the signed SOAP Envelope and create an instance of the security header out of the SOAP envelope:


 




// Load the soap envelope


SoapEnvelope env = new SoapEnvelope();


env.Load(FileName);


 




// Decrypt the soap envelope


Microsoft.Web.Services.Security.Security sec;


XmlNodeList secHeaders = env.Header.GetElementsByTagName(  
            @"Security",@"http://schemas.xmlsoap.org/ws/2002/07/secext");


try 


{


**sec = new Microsoft.Web.Services.Security.Security(  
                                   (XmlElement)secHeaders[0]);**


      


      XmlNode node = env.Body.ChildNodes[0];


      System.IO.StreamWriter writer =   
                  new System.IO.StreamWriter(DeFileName,   
                              false, System.Text.Encoding.UTF8);


      try 


      {


            writer.Write(node.OuterXml);


            System.Console.WriteLine("Successfully decrypted!");


      } 


      finally 


      {


            writer.Close();


      }


} 


catch(Exception ex) 


{


      System.Console.WriteLine("Unable to parse security header: " + ex.Message);


}


 




Again you can’t see any extra method calls like “decrypt” or “validate” because signature validation as well as decryption takes place when the security header is created based on the element. You can see that after the creation of the security header the XML that is written to the stream is decrypted. Just try removing the line where the security header is created and take a look at the output...


 




***Conclusion***


 




Although you need to work with a SoapEnvelope instance it is still easier using WSE for XML signature than using the SignedXml class of the .NET Framework. And of course it’s the only way of doing XML encryption if you don’t want programming it yourself. But I am quite sure that there will be native classes for doing both, encryption as well as signature, in future versions of the .NET Framework without any extra needs...


