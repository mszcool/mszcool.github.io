---
layout: post
title: Generating Word Documents on the Server (Part 2) - Dynamically Adding Content Controls / Structured Document Tags (SDT) using System.IO.Packaging
date: 2007-07-31 09:34:37
categories: msdnblogarchive
tags: Archive MSDNBlog
---

*This blog entry is all about dynamically adding content controls on the server to a word document and binding them to content of a custom XML data island which is generated on the server as well! You can download the sample code [here](http://mszcool.members.winisp.net/Demos/072007_OpenXmlBlog/OfficeOpenXmlBlog_v2.zip).*

 With my last [blog entry](http://blogs.msdn.com/mszcool/archive/2007/07/09/ecma-office-open-xml-options-for-generating-word-documents-on-the-server.aspx) I started a little series about generating Word documents on the server using the [ECMA Office Open XML File Formats](http://www.ecma-international.org/memento/TC45.htm) and the System.IO.Packaging API. I mentioned a couple of options for generating documents through code and the one I presented in the [first post](http://blogs.msdn.com/mszcool/archive/2007/07/09/ecma-office-open-xml-options-for-generating-word-documents-on-the-server.aspx) used the simplest approach available - embedding a custom XML document using the [Word Content Control toolkit](http://www.codeplex.com/dbe) into the Word document and using content controls to bind parts of the document to elements of this custom XML.

 The biggest advantage of the solution I presented was its simplicity: generating the document on the server was not more than creating object instances of classes based on the XML schema of the embedded custom XML and serializing it into a package part of the Open XML package. As the content controls where databound to the parts of the custom XML the contents of the document were updated automatically when the user opened the document with Word. On the other hand the biggest drawback finally was, that we had a fixed structure of our document which was not extensible. We embedded a custom XML with a limited number of invoice items and bound them to content controls - in our example we used 5 invoice items. For many cases this might be enough, but for other cases this can be an inaceptable limitation.

 ***The obvious extension to our project***

 Of course we build on top of the project we started creating [in the last post](http://blogs.msdn.com/mszcool/archive/2007/07/09/ecma-office-open-xml-options-for-generating-word-documents-on-the-server.aspx). The obvious extension to the approach of course is modifying our code so that it dynamically extends the Word table used for displaying the invoice items for each new invoice item which is dynamically added to the custom XML of the document. That means our template is not bound to exactly 5 invoice items which are hard-bound to the appropriate 5 invoice items in our custom XML as it was the case in our last version. Basically that means we can work with any number of invoice items serialized into our custom XML and that we need to dynamically add new rows to the Word table of our template and dynamically add content controls (structured document tags) to our Word table rows which are bound to the appropriate InvoiceItem element in our custom XML.

 [![clip_image003[3][1][1][1]](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/GeneratingWordDocumentsonTheServerPart2u_CA40/clip_image003%5B3%5D%5B1%5D%5B1%5D%5B1%5D_thumb.gif?raw=true?raw=true)](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/TNBlogsFS/BlogFileStorage/blogs_msdn/mszcool/WindowsLiveWriter/GeneratingWordDocumentsonTheServerPart2u_CA40/clip_image003%5B3%5D%5B1%5D%5B1%5D%5B1%5D.gif?raw=true?raw=true)

 *What are typical scenarios for this approach*

 Again you have a fixed structure of your document which needs to be extended based on content you get from some sources such as databases, services etc. on the server side or from within your application. The structure of the parts which are extended does not change, but it needs to be extended with something like repeating sections. Most important: ***you need to keep the document surface (UI) in sync with the underlying data source and the underlying data source needs to be updated whenever the user changes the document as this datasource will be processed on the server or by your application programmatically afterwards.***

 *How does this approach work?*

 Basically this approach is an extension of the approach introduced in the [last blog posting](http://blogs.msdn.com/mszcool/archive/2007/07/09/ecma-office-open-xml-options-for-generating-word-documents-on-the-server.aspx) of this series. Again you serialize a custom XML data island into your package using XmlSerializer but this time you do not limit the number of invoice items in your custom XML. Therefore it adds a second step: after you have generated the custom XML on the server and serialized it into the package, you need to process the Word Processing ML on the server as well as you need to clone table row nodes in your Word table and dynamically add content controls (structured document tags) where you then modify the dataBinding attribute to point to the invoice item in the custom XML for which the table row has been created.

 *What are the advantages of this approach?*

 * You maintain a clear separation of the document UI (document content in Word ML) and your actual business data through custom XML as with the approach demonstrated in the [previous posting](http://blogs.msdn.com/mszcool/archive/2007/07/09/ecma-office-open-xml-options-for-generating-word-documents-on-the-server.aspx) of this article series.
* Therefore you still can write program logic that works directly with your business-data structure based on an XML schema.
* With content control, business data is automatically updated in both, the document and the custom XML data island without writing additional, client sode code.

 *What are the disadvantages of this approach?*

 * It works with Word 2007, only. Although you can open the files with older versions of Office without any problems, older versions of Office don't have the functionality of Content Controls available as this was not implemented in previous Office versions, yet.
* This approach is harder to implement than the one introduced in the [previous posting](http://blogs.msdn.com/mszcool/archive/2007/07/09/ecma-office-open-xml-options-for-generating-word-documents-on-the-server.aspx) and it requires you having much deeper know-how about the Word Processing Markup language (Word ML).
* You need to work with some sort of XML API - the one I used is XML DOM with the XmlDocument object which might lead to performance problems on heavy load. This disadvantage can be mitigated by using XmlReader / XmlWriter technologies. But a switch to XmlReader / XmlWriter does not come without a drawback: the logic gets a little harder to implement compared to XmlDocument.

 
 *Extending our Sample Implementation*

 The first step for extending the sample implementation we started creating in [the last posting](http://blogs.msdn.com/mszcool/archive/2007/07/09/ecma-office-open-xml-options-for-generating-word-documents-on-the-server.aspx) is adding a new button to the ASP.NET application for dynamically adding new Invoice Items to our Invoice - as shown here:


```
protected void Button4\_Click(object sender, EventArgs e)
{
    // Add a new invoice node
    InvoiceElement Invoice =
        Session[InvoiceAdapter.CurrentInvoiceTemplateKey] as InvoiceElement;

    InvoiceElementInvoiceItem[] NewArray = 
        new InvoiceElementInvoiceItem[Invoice.InvoiceItems.Length + 1];
    Invoice.InvoiceItems.CopyTo(NewArray, 0);

    NewArray[NewArray.Length - 1] = new InvoiceElementInvoiceItem();
    NewArray[NewArray.Length - 1].ItemNumber = NewArray.Length;
    NewArray[NewArray.Length - 1].ItemName = "[new item]";

    Invoice.InvoiceItems = NewArray;

    InvoiceItemSource.Select();
    GridView1.DataBind();
}
```

This event procedure gets the current Invoice from the ASP.NET session and adds a new InvoiceItem. As XSD.EXE did not generate nice, handy List<T> properties we need to extend the array for our invoice items manually. Nice, isn't it:) Anyway - for each invoice item which we add this way to our object graph we need to generate a separate table row in the word table with - and that's important now - ***new content controls bound to the appropriate row***. Okay, that means our event handling routine of the button used for generating the document now needs to update the custom XML with the invoice we've created in our ASP.NET page as well as update the Word document itself to add new content controls as follows.


```
protected void Button2\_Click(object sender, EventArgs e)
{
    // First of all we retrieve the invoice
    InvoiceAdapter.CalculateTotals();
    InvoiceElement Invoice =
        Session[InvoiceAdapter.CurrentInvoiceTemplateKey] as InvoiceElement;

    // First of all copy the template for this approach
    string templatePath = Server.MapPath("~/InvoiceTemplate\_Hard.docx");
    string generatedPath = Server.MapPath
                           (
                                string.Format("~/Generated/Invoice{0}.docx",
                                              Invoice.InvoiceNumber)
                           );
    if (File.Exists(generatedPath))
        File.Delete(generatedPath);
    File.Copy(templatePath, generatedPath);

    // Now open the package using the packaging API and generating content
    using (Package p = Package.Open(generatedPath))
    {
 ***UpdateCustomXml(p, Invoice);
 UpdateWordDocument(p, Invoice);***
        p.Flush();
    }

    StatusLabel.Text = 
        string.Format("Successfully generated invoice Invoice{0}.docx!!", 
                      Invoice.InvoiceNumber);
}
```

Again we copy a template (this time called InvoiceTemplate\_Hard.docx) and copy it to our folder for generated invoices. Before that we calculate the totals of our invoice using the business object adapter I've included and then we retrieve the Invoice from the current ASP.NET session. Afterwards we simply open the package, but this time just serializing the invoice object as custom XML part as we did it last time is not enough. This is just one of two steps which I've encapsulated into a method called ***UpdateCustomXml(package, InvoiceElement)*** as follows:


```
private void UpdateCustomXml(Package p, InvoiceElement Invoice)
{
    // Here we open up the custom XML part included in the word document
    // and append new invoice items to our invoice
    Uri XmlPartUri = new Uri("/customXml/item1.xml", UriKind.Relative);
    PackagePart XmlPart = p.GetPart(XmlPartUri);

    // Now we just serialize the invoice into the custom XML
    // This time we can create as many invoice items as we want.
    using (Stream s = XmlPart.GetStream(FileMode.Create))
    {
        XmlSerializer serializer = new XmlSerializer(typeof(InvoiceElement));
        serializer.Serialize(s, Invoice);
    }
}
```

Still this very simple, but now we are getting to the much harder part - updating the Word document for extending the Word table with new content controls bound to the appropriate parts of our custom XML serialized into the package in the UpdateCustomXml method. The steps for doing this are the following:


1. First we load the Word document into an XmlDocument instance.
2. Then we create an XmlNamespaceManager with the Word namespace as we will execute some XPath queries while processing the document.
3. Next we will retrieve the <w:tbl /> node through an XPath expression.
4. Then we will retrieve the second row (!!) of the table as this is the first real content row (the first row contains the headers and the last row contains the summary.
5. For each invoice item in our invoice we clone the previously selected row and update the databindings of the content controls, update their aliases and IDs as they need to be unique.
6. Last but not least we save the modified XmlDocument back to the package part to update the Word document (Word ML part of the document).


This logic is encapsulated into the ***UpdateWordDocument(Package, InvoiceElement)*** method as you can see below:


```
private void UpdateWordDocument(Package p, InvoiceElement Invoice)
{
    // Step 1: loading the Word document into an XmlDocument  
    Uri WordDocUri = new Uri("/word/document.xml", UriKind.Relative);
    PackagePart WordDocPart = p.GetPart(WordDocUri);
    XmlDocument WordDoc = new XmlDocument();
    using (Stream s = WordDocPart.GetStream())
    {
        WordDoc.Load(s);
    }

    // Step 2: Create an XmlNamespaceManager for XPath queries
    XmlNamespaceManager NsMgr = new XmlNamespaceManager(WordDoc.NameTable);
    NsMgr.AddNamespace("w", "http://schemas.openxmlformats.org/wordprocessingml/2006/main");

    // Step 3 and step 4: Selecting the table node and the first row  
    XmlNode TableNode = WordDoc.SelectSingleNode("//w:tbl", NsMgr);
    XmlNode FirstRowNode = TableNode.SelectSingleNode(".//w:tr[2]", NsMgr);
    XmlNode NewNode = null, PreviousNode = null;
```

```
    // Step 5: Creating a new row in the row for each item in the invoice  
 // and update the content control databindings to bind to the   
 // appropriate element in the custom XML part
    for (int i = 0; i < Invoice.InvoiceItems.Length; i++)
    {
        // Current item
        InvoiceElementInvoiceItem Item = Invoice.InvoiceItems[i];

        // The first node can be modified directly
        if (NewNode == null)
        {
            PreviousNode = NewNode = FirstRowNode;
        }
        else
        {
            // Keep the previous node to be able to 
            // insert the node at the right position
            NewNode = FirstRowNode.CloneNode(true);
            TableNode.InsertAfter(NewNode, PreviousNode);
            PreviousNode = NewNode;
        }

        // Update the SDT tags in the document
 **XmlNodeList SdtNodes = NewNode.SelectNodes(".//w:sdt", NsMgr);
 UpdateSdtNode(SdtNodes[0], i + 1, 1,
 string.Format("ItemNr\_{0}", i), NsMgr);
 UpdateSdtNode(SdtNodes[1], i + 1, 2,
 string.Format("ItemDescription\_{0}", i), NsMgr);
 UpdateSdtNode(SdtNodes[2], i + 1, 3,
 string.Format("ItemAmount\_{0}", i), NsMgr);
 UpdateSdtNode(SdtNodes[3], i + 1, 4,
 string.Format("ItemPrice\_{0}", i), NsMgr);
 UpdateSdtNode(SdtNodes[4], i + 1, 5,
 string.Format("ItemTotals\_{0}", i), NsMgr);**
    }

    // Step 6: Save the XmlDocument back to the document.xml part  
    using (Stream s = WordDocPart.GetStream(FileMode.Create))
    {
        WordDoc.Save(s);
    }
}
```

Most of the logic in this method is plain XML processing and should be nothing new for a .NET developer. The most interesting part is encapsulated in the UpdateSdtNode() method highlighted in bold in the previous code snippet. SDT is the abbreviation for ***Structured Document Tags*** and SDTs are actually the reflection of content controls in the XML part of the document. Taking a look at the following XML excerpt of a typicall Word document with a content control bound to parts of a custom XML will explain the most important parts of an SDT:


```
<w:tr w:rsidR="00204484" w:rsidTr="00204484">
 <w:trPr>
 <w:cnfStyle w:val="000000100000" />
 </w:trPr>
 <w:sdt>
 <w:sdtPr>
 <w:dataBinding   
 w:prefixMappings="xmlns:ns0='blogs.msdn.com/mszcool/officeopenxml/generatingdocumentsseries/2007/07'"  
 **w:xpath="/ns0:InvoiceElement[1]/ns0:InvoiceItems[1]/ns0:InvoiceItem[1]/ns0:ItemNumber[1]"**  
 w:storeItemID="{C941DEB9-29BE-4021-950D-34892903E6EE}" />
 <w:rPr>
 <w:lang w:val="en-US" />
 </w:rPr>
 <w:alias w:val="ItemNr\_01" />
 <w:tag w:val="ItemNr\_01" />
 <w:id w:val="17763868" />
 <w:placeholder>
 <w:docPart w:val="E5C03C5D0D364E298E9CFE332768F056" />
 </w:placeholder>
 <w:showingPlcHdr />
 <w:text />
 </w:sdtPr>
 <w:sdtContent>
 <w:tc>
 <w:tcPr>
 <w:cnfStyle w:val="001000000000" />
 <w:tcW w:w="675" w:type="dxa" />
 </w:tcPr>
 <w:p w:rsidR="00204484" w:rsidRDefault="00864DE0" w:rsidP="00204484">
 <w:pPr>
 <w:rPr>
 <w:lang w:val="en-US" />
 </w:rPr>
 </w:pPr>
 <w:r>
 <w:t>0</w:t>
 </w:r>
 </w:p>
 </w:tc>
 </w:sdtContent>
 </w:sdt>  
 ***........ other tags .........***
</w:tr>
```

The XML is just an excerpt of one table row showing the SDT tag for the content control which is bound to the ItemNumber element of the first InvoiceItem stored in the custom XML of our package. You can see this when taking a look at the **dataBinding** element of the SDT tag where you find an XPath expression mapping to an element in our custom XML:


w:xpath="/ns0:InvoiceElement[1]/ns0:InvoiceItems[1]/ns0:InvoiceItem[1]/ns0:ItemNumber[1]"


As you can see, this XPath maps to the first ItemNumber element in the first InvoiceItem element of the first InvoiceItems element within the first InvoiceElement of our custom XML (based on the indexes [1] used in the XPath). If we want to update the databinding of a content control we need to update this XPath expression to map to the invoice item in our custom XML we want to display in a certain table row of our Word table - e.g. if we want to map to the third invoice item the XPath would look as follows:


w:xpath="/ns0:InvoiceElement[1]/ns0:InvoiceItems[1]/ns0:InvoiceItem***[3]***/ns0:ItemNumber[1]"


Everything in our XPath stays the same, we just update the index of the invoice item to map to the third InvoiceItem element of the custom XML part we serialized into our package with the UpdateCustomXml() method before. Furthermore we need to update attribute **w:val** for the **w:alias** and the **w:id** tags as they need to be unique for each content control. The value of the **w:alias** can be any value you want whereas the **w:id** value needs to be a integer. These updates are performed all together in the ***UpdateSdtNode()*** method used in our previous code snippet (UpdateWordDocument() method). It sounds complicated but it is fairly simple:


```
private void UpdateSdtNode(XmlNode xmlNode, int index, int elementIndex, string alias, XmlNamespaceManager nsMgr)
{
    XmlNode HelperNode = null;

    // Update the databinding node
    HelperNode = xmlNode.SelectSingleNode(".//w:sdtPr/w:dataBinding", nsMgr);
    HelperNode.Attributes["w:xpath"].Value =
        HelperNode.Attributes["w:xpath"].Value.Replace
        (
            "ns0:InvoiceItem[1]",
            string.Format("ns0:InvoiceItem[{0}]", index)
        );

    // Update the alias and tag nodes
    HelperNode = xmlNode.SelectSingleNode(".//w:sdtPr/w:alias", nsMgr);
    HelperNode.Attributes["w:val"].Value = alias;
    HelperNode = xmlNode.SelectSingleNode(".//w:sdtPr/w:tag", nsMgr);
    HelperNode.Attributes["w:val"].Value = alias;

    // Update the ID node
    HelperNode = xmlNode.SelectSingleNode(".//w:sdtPr/w:id", nsMgr);
    HelperNode.Attributes["w:val"].Value =
        (1000000 + (elementIndex * 1000) + (index * 10)).ToString();

}
```

That's it, now we have generated a Word document by dynamically extending the Word table <w:tbl/> and generating new Structured Document Tags (<w:sdt />) for each row which are in turn bound to content of our custom XML structure.


You can download the most recent version of application including the code introduced in this blog post by clicking on the link below:


[Download sample code](http://mszcool.members.winisp.net/Demos/072007_OpenXmlBlog/OfficeOpenXmlBlog_v2.zip)


