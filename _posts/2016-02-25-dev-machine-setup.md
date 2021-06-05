---
layout: post
title:  Developer Machine Setup - Nearly Automated
date:   2016-02-25 12:00:00 +0100
categories: wordpressarchive
tags: Archive Infrastructure Automation DeveloperStuff
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

I know, I wanted to blog more often... but then a happy event came in between which was the birth of my little baby daughter - Reinhard's 2 year younger sister Linda. Now I am sitting here at the registry office for her documents. What could be a better thing to write a blog-post while waiting for the next steps!?

Since my brand-new Surface Book arrived, yesterday, one thing I have to do is setting it up for my daily use. That means installing all the handy tools that I use on a daily basis such as PDF readers, KeePass and the likes. It also means setting up the many tools and development environments I use (some of them more often, others less often).

Well, now the cool thing is that I've automated most of these setup procedures using [Chocolatey](<https://chocolatey.org/>) and [PowerShell](<https://technet.microsoft.com/en-us/library/bb978526.aspx>). And given the situation I am right now in (new daughter, waiting at the registry office, Surface Book arrived) I thought why not share my script with the rest of the world and explain it a bit...

<!--more-->

## Chocolatey and PowerShell Script

A while ago when one of my peers, Kristofer Liljeblad, pointed me to Chocolatey, I started building out a fairly comprehensive script that automates most of the steps for setting up of my machines. I've now published that script on my github repositiory:

[mszCool's Dev Machine Setup Script](<https://github.com/mszcool/devmachinesetup>)

Since I thought I don't want to install all tools on every machine, I added some switches to the script that allows me to install a group of tools, only. E.g. when I setup a machine for one of my relatives that typically is not used for development, I only install a bunch of end-user tools such as a PDF reader. On my developer machines I typically run all of the switches.

But in that case it turned out, that sometimes PowerShell needs to be restarted after certain install procedures. Therefore I added a few other switches to the script so that I can re-start PowerShell in between of those steps.

Finally, I still do install certain parts manually. E.g. for SQL Server Management Studio on Windows 10 I need to add the .NET Framework 3.5 Feature Set which I have not coded into PowerShell, yet (I know it does work).

All of this resulted into a workflow of several phases for which most of the times the script just does its work but sometimes I need to intervene and re-start PowerShell or do some manual installation steps. I know, there might be more elegant solutions to this. But remember, it's just for setting up my developer machines, so it's just quick and pragmatic.


## Using the Script

To use the script, as I said there are a few steps that need to be executed manually upfront. 

* Install the .NET Framework 3.5 Feature through the Windows Settings Panel
* Start an ***elevated*** PowerShell Window as
* Set the PowerShell Execution Policy to Unrestricted by executing `Set-ExecutionPolicy Unrestricted`

After that you can start the script the first time. For that purpose I typically download it into a temp-directory on my local machine since it downloads things (which it typically deletes afterwards, but not always:)). From there, just execute the following commands in the previously opened PowerShell Window:

`.\Install-WindowsMachine.ps1 -installChoco -tools -ittools -dev -data`

This installs the following items:

* `-installChoco` installs Chocolatey as per their homepage.
* `-tools` installs tools I commonly use such as Adobe Reader or KeePass.
* `-ittools`installs tools I categorized as OS tools such as SysInternals.
* `-dev` installs a set of independent tools I use for development such as Fiddler.
* `-data` installs tools to manage databases, but not the DB-engines themselves. Examples are SQL Server Management Studio or MySQL Workbench.

Since this phase installs many tools which are added to the environment path, it is now necessary to re-start PowerShell before moving on.

## Installing IDEs

The next step is installing Integrated Development Environments such as **Visual Studio**. If you are fine with the (free Community Edition of Visual Studio)[<https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx>], the script includes a switch for that. 

As **Eclipse-based** IDE I decided to stick with [Spring Tool Suite](<http://spring.io/tools>). The main reasons for that are one, because Spring is one of the most popular Java-frameworks and two, because it comes with a whole lot of add-ins (e.g. Maven and many more) pre-packaged. That makes it convenient! Sometimes for Java I also play around with [IntelliJ IDEA](<https://www.jetbrains.com/idea/>), so I also included that. 

For all the rest I am using [Visual Studio Code](<https://code.visualstudio.com/>) and [Sublime](<http://www.sublimetext.com/>), although I haven't VS Code included in the script, yet (didn't have the time to update:)), and still install it manually.

If you don't like those choices, now is the time to manually install your favorite versions of Visual Stuido, Eclipse and co. If you like it, the command below does the job and installs all the aforementioned environments - for Visual Studio Versions I support both, 2013 and 2015:

`.\Install-WindowsMachine.ps1 -installVs -vsVersion 2015 -installOtherIDE`

After that phase it's needed to re-start PowerShell, again, because IDEs and especially Visual Studio adds a few things to the environment path.

## Visual Studio Extensions, Web PI and Database Engines

Yes, I also got bored by installing Visual Studio Extensions over and over again. So, what I did is writing a little PowerShell function which downloads Visual Studio Extensions and installs them using the Visual Studio Extensions Installer.  Also there are a few more tools which I typically need in Visual Studio which are available in the [Web Platform Installer (WebPI)](<https://www.microsoft.com/web/downloads/platform.aspx>). These are things such as the Azure SDKs and tools or Azure PowerShell and the likes.

Simply call the script with the following switches after you've Visual Studio installed (note that `-dev2` and `-vsext` are not dependent on each other):

`.\Install-WindowsMachine.ps1 -dev2 -vsext -vsVersion 2015`

To install database engines such as SQL Server Express, Cassandra (based on Datastax's distribution), I've included an additional switch:

`.\Install-WindowsMachine.ps1 -dataSrv`

## Cloning Repositories

Finally, on every developer machine I need to clone many repositories from GitHub or private TFS and Bitbucket environments. I've also added that to the script, although I did remove the ones from private environments for this publication. For this I've added the following switch, which can be combined with ANY of the above AFTER `-dev` has ran (because that installs git):

`.\Install-WindowsMachine.ps1 -cloneRepos`

## What's missing & Caveats

This script really saves me a lot of time whenever setting up machines on a regular basis (i.e. development VMs for trying out something or re-setting up my machines because of Windows Insider Build stuff etc.). 

A few things are missing which I typically install. I plan to add those pieces to the script at some point in time in the future as soon as I have some spare time. But for now I do install them manually:

* [Git-Posh](<https://github.com/dahlbyk/posh-git>), a PowerShell Add-In for Git which is awesome.
* [Node.js Tools for Visual Studio](<https://visualstudiogallery.msdn.microsoft.com/7f82bb38-ce36-4004-bf36-6d783e2ee9d2>) which are handy since Node becomes more and more popular.
* [SQL Server Data Tools](<https://msdn.microsoft.com/de-at/mt186501>) as it helps with a few Azure SQL DB / Standard SQL Server tasks.

For those I need to update the script to be able to download and run MSIs in a silent mode. Also Git-Posh requires cloning a git-repo and executing a few statements. All of that is simple, but I haven't had the time to do it, yet. But I also do accept pull-requests if you find the time to work on it and think it's a useful extension to this work.

## Final Words

This script is a simple and pragmatic thing. It installs most of the things in an automated way and typically with it and with a good Internet connection I have a fully working developer machine after a few hours without really doing a lot. The things I install manually (e.g. Visual Studio 2015 Enterprise instead of Community) also do run a bit longer. So they don't distract me while I do some other work on one of my other machines. And that's the point - I can install new images while working in parallel nearly end-2-end and it won't hold me off from other work (as opposed to continuously clicking through some installer wizards:)).

I hope you like the work and as mentioned, I am happy about any feedback and pull-requests for it. Just go, look, download and use it [from my github repository](<https://github.com/mszcool/devmachinesetup>)