---
layout: post
title: Visual Studio Tools For Office - 2003 and 2005 Side-by-Side
date: 2005-07-08 04:15:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Currently I am using both, Visual Studio 2003 and Visual Studio 2005 for creating Smart Document solutions. I have installed Visual Studio 2003 and 2005 side-by-side on one machine and when you want to debug solutions written with .NET 1.1 you have to bare in mind, that Office 2003 (winword.exe and excel.exe) always loads the newest version of the .NET Framework into it's process. Therefore if you want to debug solutions with Visual Studio 2003, you have to create a configuration files for winword.exe and excel.exe called **winword.exe.config** and **excel.exe.config** and put it into the program files directory of Office (<DRIVE>\Program Files\Microsoft Office\Office11). These configuration files specify the runtime that should be loaded for an executable as follows (contents of winword.exe.config and excel.exe.config):


<configuration>  
   <startup>  
      <requiredRuntime version="v1.1.4322" safemode="false" />  
   </startup>  
</configuration>


With this configuration files in place winword.exe and excel.exe load the v1.1.4322 of the .NET runtime and therefore you can debug the solution with Visual Studio 2003 as usual. As soon as you want to work with Visual Studio 2005 again you just need to rename these configuration files (for example to winword.exe.config.11 and excel.exe.config.11) and Office loads the 2.0 (or latest) version of the runtime again. If you then want to switch back to Visual Studio 2003 again, just rename them to winword.exe.config and excel.exe.config again - That's it...


