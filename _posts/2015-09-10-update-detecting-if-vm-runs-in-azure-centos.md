---
layout: post
title:  Update - Detecting if a CentOS Virtual Machine runs in Azure for Azure Marketplace
date:   2015-09-10 12:00:00 +0100
categories: wordpressarchive
tags: Archive Azure VirtualMachines Marketplace
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

A few months ago [I did blog-post about how-to detect whether a virtual machine runs in Azure or not](<http://blog.mszcool.com/index.php/2015/04/detecting-if-a-virtual-machine-runs-in-microsoft-azure-linux-windows-to-protect-your-software-when-distributed-via-the-azure-marketplace/>). This is vital for many independent software vendors who are planning to offer their own software through the [Azure Marketplace for Virtual Machines](<https://azure.microsoft.com/en-us/marketplace/virtual-machines/>).

<!--more-->

## The main detection strategy (Windows, Ubuntu)

In the post I did explain a few tricks on detecting whether the VM runs in Azure or not for both, Windows and Linux. Still the most reliable check known as of today is to check if the DHCP option "unknown-245" is set for in the DHCP-lease options for a virtual machine. 

* **Windows**: there's a great post by a premier field engineer showing how that works with PowerShell CmdLet: <https://gallery.technet.microsoft.com/scriptcenter/Detect-Windows-Azure-aed06d51>


* **Ubuntu** Linux: I've posted a bash script in my previous blog. I generally stated that this works for Linux all up without considering that other Linux distributions might have different configuration files for storing DHCP lease details. Hence the following script works on **Ubuntu**-Linux based flavors, only:

```bash  
if `grep -q unknown-245 /var/lib/dhcp/dhclient.eth0.leases`; then
   echo “Running in an Azure VM”
fi

## Detecting if a CentOS VM runs on Azure
```

My peer and colleague Arsen Vladimirskiy pointed out that on CentOS the file for DHCP leases is stored on a different location. Hence the detection strategy for the DHCP-lease option I've explained in [my original post](<http://blog.mszcool.com/index.php/2015/04/detecting-if-a-virtual-machine-runs-in-microsoft-azure-linux-windows-to-protect-your-software-when-distributed-via-the-azure-marketplace/>) does not work in CentOS-based virtual machines. 

For CentOS based virtual machines the DHCP lease options are indeed stored in the path `/var/lib/dhclient/dhclient.leases` (or in case of multiple network interfaces `dhclient-eth0.leases` whereas the part `eth0` needs to be replaced with the networking interface device you're going to check against).

Therefore in a default configuration with just one ethernet adapter the script needs to be updated as follows to work inside of a CentOS virtual machine:

```bash
# manually start dhclient (seems to be a workaround)
dhclient

# then check against the lease files
if `grep -q unknown-245 /var/lib/dhclient/dhclient.leases`; then
   echo "Running in Azure VM"
fi
```

**Note:** There was one weird issue I ran into when trying the approach above, hence the script starts with launching dhclient. On a fresh deployed CentOS 7 VM in Azure from the marketplace stock image `dhclient` is not started by default. Therefore files such as  `dhclient.leases` or `dhclient-*.leases` do not exist by default under `/var/lib/dhclient/`. 

Only after manually executing the command `sudo dhclient` for starting the DHCP-client the files where created successfully and the check works. Well, now someone could think that this might be related to static IP addresses - but in Azure that's not correct since IP addresses are always assigned by the Azure DHCP server. In case you want to have static IPs you configure those through the Azure Portal or Management APIs so that the Azure DHCP server always assigns the same, static IP address to the VM in the private, virtual network. So that cannot be the reason.

## A more Complete Story for detecting DHCP unknown-245 in Linux

Well, now the distributions above are very common ones but are by var not all of the supported ones on Azure. The [source code for the Azure Linux Agent](<https://github.com/Azure/WALinuxAgent/blob/2.0/waagent>) contains all the secrets currently valid. If you really want to be on the save side across multiple Linux distributions. A few hints in the Python-based source code are:

* Line 99-100 do show the directories you should consider for your detection strategy
   ```bash
   VarLibDhcpDirectories = 
      ["/var/lib/dhclient", "/var/lib/dhcpcd", "/var/lib/dhcp"]
   EtcDhcpClientConfFiles = 
      ["/etc/dhcp/dhclient.conf", "/etc/dhcp3/dhclient.conf"]
   ```

* Further down in the code starting at line 5107 there is a section that makes use of option 245 as well:
  ```bash
  # ... other code before
  elif option == 3 or option == 245:
      # ...
  else:
      # ...
  # ... more code goes here
  fi
  ```

This code has been updated to version 2.0.15 24 days before writing/publishing this post. So it should still be safe to leverage option 245 for your detection strategy. As soon as there's something better available, I'll definitely post another update for this blog-post!

## Final Disclaimer

The approaches outlined above did work on both, Ubuntu and CentOS 7 based VMs in Resource Manager based deployments (using the new [ARM-template approach](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/) introduced by the Azure teams earlier this year) at the time of publishing this post (2015-09) during my tests. When I published the original post I did test them with classic service management based VMs, of course. 

Therefore and as there is still no better way introduced at the time of publishing this post, yet, the options outlined in this and [my original post](http://blog.mszcool.com/index.php/2015/04/detecting-if-a-virtual-machine-runs-in-microsoft-azure-linux-windows-to-protect-your-software-when-distributed-via-the-azure-marketplace/) are still valid and eventually the best you can get so far for detecting if your VM runs inside of Microsoft Azure or not...

If you found better options don't hesitate to contact me via [my twitter feed](http://twitter.com/mszcool)...