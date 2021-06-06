---
layout: post
title: Nice Browser HTML/JavaScript Trick based on yesterday’s expert chat on derStandard.at – avoid “Unknown Runtime Error” in IE while setting innerHTML of a <tr>-tag...
date: 2010-01-21 00:25:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Yesterday someone during our Cloud-Computing chat at derstandard.at (see [here](http://derstandard.at/1262209524950/Chat-Nachlese-Wir-machen-uns-gern-selbst-das-Leben-schwer)) someone asked me why his JavaScript and HTML work in any browser but not IE incl. IE8... See the problem in his code and a solution below (you can download the solution [here](http://cid-d37c9d7bfbce8418.skydrive.live.com/self.aspx/Public/Various/20100120%5e_derStandard%5e_Chat%5e_HtmlJavaScriptTip.zip))… 


**Problem – He posted the following code below:**When launching his code, IE fails while executing the JavaScript below when trying to set the **innerHMTL** of the **<tr ="q"/>** tag. Unfortunately IE's error message isn't really helpful because it just gives us a stupid "Unknown Runtime Error" which is really annoying. 


Unfortunately the IE-engine below is right with saying that what we're doing is incorrect, unfortunately it doesn't tell us the reason. So what is the reason? Whenever setting the innerHTML of an element with JavaScript DOM, IE calls an internal method called ValidateObject which validates the current HTML element DOM-tree whether is right or not. The method implements a **very strong validation** and this validation says, that a **<tr>-tag can contain only child-nodes (more exactly td or th) but not any text or HTML. Therefore the method says, that calling innerHTML on a <tr>-element is not valid – you need to add new "elements" to a tr-element (as it can contain elements, only) by using createChildElement() and appendChild().** Scroll down to the bottom of this post to see, which code is working for both, IE and Firefox (I've tested it with IE8 and Firefox 3.5.7!!!). 


HTML file: 


<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"   
"http://www.w3.org/TR/xhtml1... t.dtd">  
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">   
<head>   
<title>Test</title>   
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />   
<script type="text/javascript" src="test.js" mce\_src="test.js"></script>   
</head>   
<body>   
<table>   
<tr id="q"><td> </td></tr>   
</table>   
<p><input type="hidden" id="c" value="ab cd e fg " /></p>   
</body>   
</html> 


Javascript file (test.js) 


var i = 0;   
var j = 0;   
var itv;   
function init() {   
document.getElementById("q").innerHTML = "";   
itv = window.setInterval("displayText()", 200);   
} 


function displayText() {   
var c = document.getElementById("c").value;   
var q = "";   
while (c.substring(i, (i + 1)) != " ") {   
q = q + c.substring(i, (i + 1));   
i++;   
}   
i++; j++;   
if (j == 4) { window.clearInterval(itv); }   
document.getElementById("q").innerHTML = document.getElementById("q").innerHTML + "<td>" + q + "</td>";   
}   
window.onload=init; 


**The real Cross-Browser Solution for the Problem:** 


Okay, as setting innerHTML on a <tr>-element doesn't work, the alternative approach would be using createElement() and appendChild(). I did exactly that and tested it in Firefox 3.5.7 and IE8… and guess what, it works:) see the correct JavaScript-code below (I didn't change anything on the HTML): 


var i = 0; 


var j = 0; 


var itv; 


function init() { 


var element = document.createElement("td"); 


var tr = document.getElementById("q"); 


if (tr.childNodes.length > 0) { 


// Remove all childs - we know we have just one 


// so just remove the first one 


tr.removeChild(tr.childNodes.item(0)); 


} 


// !!! Following line is Wrong, 


// !!! because TR can only have "child-Nodes" 


// !!! in its object model and not any text or HTML 


// !!! therefore --> innerHTML cannot be set!!! 


//document.getElementById("q").innerHTML = ""; 


// Correct way works in both, Firefox and IE 


var emptyTd = document.createElement("td"); 


// Here works because TD can include any text and HTML 


emptyTd.innerHTML = ""; 


tr.appendChild(emptyTd); 


itv = window.setInterval("displayText()", 200); 


} 


function displayText() { 


var c = document.getElementById("c").value; 


var q = ""; 


while (c.substring(i, (i + 1)) != " ") { 


q = q + c.substring(i, (i + 1)); 


i++; 


} 


i++; j++; 


if (j == 4) { window.clearInterval(itv); } 


// What are you doing below, you're adding 


// invalid HTML to a table row element (TR) which is supposed to have 


// <td> or <th> elements, only - so don't wonder that this 


// won't work 


//document.getElementById("q").innerHTML = 


// document.getElementById("q").innerHTML + "<td>" + q + "</td>"; 


var tr = document.getElementById("q"); 


// Append a new TD-node 


var newTd = document.createElement("td"); 


newTd.innerHTML = q; 


tr.appendChild(newTd); 


} 


window.onload = init; 


I hope I answered the question from the chat with this posting and I hope this posting helps others as well;) have a nice day...


