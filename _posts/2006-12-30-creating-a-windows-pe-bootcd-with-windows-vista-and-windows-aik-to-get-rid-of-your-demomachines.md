---
layout: post
title: Creating a Windows PE Boot-CD with Windows Vista and Windows AIK to get rid of your Demo-Machines
date: 2006-12-30 16:53:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

About two months ago I discovered the [Windows Automated Installation Toolkit (Windows AIK or just WAIK)](http://www.microsoft.com/downloads/details.aspx?FamilyID=c7d4bc6d-15f3-4284-9123-679830d629f2&DisplayLang=en "WAIK") to be able to work with different demo images for the sessions I did at [TechEd Europe](http://www.mseventseurope.com/TechEd/06/pre/defaultDev.aspx "TechEd") with physical machines instead of the (still much slower:)) Virtual PC images. The WAIK includes [imagex.exe](http://technet.microsoft.com/en-us/windowsvista/aa905116.aspx "imagex"), which is a tool that allows you to create images of a partition of your machine and package them into the new [Windows Imaging Files (WIM)](http://technet.microsoft.com/en-us/windowsvista/aa905116.aspx "WIM").


***The first steps using WAIK and imagex.exe***


These files can be used for centralized deployment via the [Windows Deployment Services](http://technet.microsoft.com/en-us/windowsvista/aa905118.aspx "Windows Deployment Services") (successor of Remote Installation Services). But in my case I used it to image and restore different types of demo images for my physical machines as I did not want to work with the slower VPC counterparts at a conference such as TechEd Europe. Therefore I created two partitions on my developer machine, one with my primary OS-instance (Vista joined into our working domain for email etc.) and a second partition for my different demo images. On the second partition I installed Windows Server 2003 with everything I needed for my demo sessions (different images, detailled steps see below) and then I created an WIM-image from the Vista OS instance using imagex.exe from the WAIK as follows:


*imagex /capture D: M:\WindowsImages\Windows2003\_OfficeDev.wim "Windows Server 2003 Office Development"*


This command captures everything on drive "D:" into a WIM file stored on my external hard disk "M:". Then I tried the demos for the session and to get the original situation again I just restored the image as follows: 


*format D: /q  
...  
imagex /apply M:\WindowsImages\Windows2003\_OfficeDev.wim 1 D:\*


To get the complete picture, here is what I did for [TechEd](http://www.mseventseurope.com/TechEd/06/pre/defaultDev.aspx "TechEd") where I required for example one image for SharePoint 2007 Office Development, one image for Windows Vista for WPF and Composite UI Application Block Development and a third one for plain, old Office 2003 development (during the beta stage of Office 2007 and especially VSTO 2005 SE installing Office 2003 and Office 2007 side-by-side was not supported... right now fortunately this is supported and works really fine, therefore I have tried the imaging stuff on several machines, already):


1. First I created a basic install of Windows Server 2003 on my second partition D:
2. Then I installed my main Vista partition for regular work (email, development in our office etc.).
3. On my main Vista instance I installed the WAIK.
4. Then I have created the first image of the Windows Server 2003 partition using imagex /capture.
5. Afterwards I installed everything for Office 2003 development on this Windows Server 2003.
6. Now I captured this Office 2003 development workstation using imagex /capture.
7. Next I restored the original Windows Server 2003 instance using imagex /apply.
8. On the restored instance of Windows Server 2003 I installed everything for Office 2007 development.
9. I imaged the Office 2007 development workstation using imagex /capture, again.
10. Now I had my demo images as WIM-images available and could switch between Office 2003 and Office 2007 (beta at that point of time;)) within 15 min. by restoring the appropriate image via imagex /apply.


With that, management of my demo partitions became really fairly easy and I was not forced to use Virtual PCs but still remain with the same advantages such as restoring an original stage of my machines very quickly.


***Creating a Windows PE image using WAIK***


The next step was fairly obvious. At home I used the days between Christmas and New Year to get my IT infrastructure done, at home. And of course I wanted to have a very smooth way of restoring my test servers and workstations quickly if something goes wrong. So my idea was creating WIM images for each of the servers and either deploying them via [Windows Deployment Services](http://technet.microsoft.com/en-us/windowsvista/aa905118.aspx "Windows Deployment Services") from my PDC or just from external hard disks. But I don't want to have two OS instances on each machine (one for imaging and image restore and the other one for "productive work"). So I required a CD-bootable version of Windows to be used as imaging and restore OS-instance with imagex.exe installed. Finally the WAIK includes all the tools for creating Windows PE instances which are bootable from either CDs, DVDs or USB sticks (!!).


I thought before the new year starts right now I need to try something risky with my home machine:) After searching a while I've found to blog entries which are uncovering some details for creating Windows PE images ([first](http://blogs.technet.com/jamesone/archive/2006/11/10/getting-started-with-windows-pe.aspx "Windows PE Getting Started"), [second](http://blogs.technet.com/jamesone/archive/2006/11/11/customizing-windows-pe.aspx "Windows PE Customizing")... thanks to [James O'Neill](http://blogs.technet.com/jamesone "James O'Neill"))... but frankly the steps are so simple that I was completly surprised:


1. Download and install [WAIK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c7d4bc6d-15f3-4284-9123-679830d629f2&DisplayLang=en "WAIK") (obvious:))
2. Open the *"Windows PE Tools Command Prompt"*
3. Create a directory for the template of your Windows PE image (e.g. *C:\WinPE* as I use it here)
4. Next switch to *"C:\Program Files\Windows AIK\Tools\PETools"*
5. Execute *"copype x86 C:\WinPE"*
6. Now copy anything (tools, programs etc.) you want into the ISO directory of your Windows PE template directory (in my case "C:\WinPE"). You can create any sub directory you want within the ISO sub-directory such as "Imaging" in the example below where I copy the imagex.exe tool to the Imaging directory of my ISO template directory as follows:  
*copy "C:\Program Files\Windows AIK\Tools\x86\imagex.exe" c:\WinPE\ISO\Imaging*
7. Then switch to *"C:\Program Files\Windows AIK\Tools\PETools\x86"*
8. Now apply the original Windows PE boot image:  
*imagex /apply winpe.wim 1 c:\WinPE\mount*
9. Using the peimg.exe tool you can install either additional drivers by specifying an INF-file or pre-built packages such as MDAC, MSXML or Windows Scripting support. Examples:  
*peimg /install=WinPE-XML-Package C:\WinPE\mount  
  peimg /install=WinPE-Scripting-Package C:\WinPE\mount*
10. After the "mount" directory has been prepared (using peimg.exe as shown above) you can create a WIM image for your ISO-template directory as follows:  
*imagex /capture /boot /compress max "C:\WinPE\mount" "C:\winpe\iso\sources\boot.wim" "mszCool PE"*
11. Next you need to create the ISO-image that you can burn onto a CD using any tool you want (such as Nero).  
*Oscdimg -n c:\winpe\ISO c:\winpe\mszCool\_winpe.iso -n -bc:\winpe\etfsboot.com*
12. Using the OSCDIMG.EXE tool you have created a ready-to-use ISO image that you can burn onto a CD. That CD is then a bootable version of Windows PE. From within this CD-booted instance you can image and restore any partition on your computer you want - for free:)


As I wanted to have some fun and risk on the second-last day of this year, I just tried this on my home workstation by imaging and restoring my primary OS-instance partition (which is the boot-partition as well;))... and it just rocks... work of about 40 min. including all the Internet search on how-to create such a Windows PE images.


