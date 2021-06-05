---
layout: post
title:  Detecting if a Virtual Machine runs in Microsoft Azure (Linux and Windows)
date:   2015-04-16 12:00:00 +0100
categories: wordpressarchive
tags: Archive Azure VirtualMachines Marketplace
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

Our team started working more and more with software vendors we categorize as “Enablers” at a global scale. Such companies are providing building block services which can be used to build finished software services that do run in the cloud (or on-premises).

For such “Enablers” the [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/) is a key-instrument to gain visibility and traction as well as for instantiating their services in their customer’s Microsoft Azure Subscriptions.

At the moment most of the partners are working with us to deploy offerings based on templates with single or multiple Virtual Machines that do run their software. Later down the path we will also enable on-boarding of “Application Services” where customers do not have to instantiate and manage Virtual Machines, anymore.

One of the main challenges our partners do face when putting their software into Virtual Machine templates which can be instantiated and/or purchased through the Azure Marketplace is protecting their software from being operated outside of Azure since this would enable malicious people to operate the software without charging for it.

<!--more-->

## Customers have full control to VMs provisioned via the Marketplace

Since when end-customers create Virtual Machines via the Azure Marketplace they have full control of the resulting, instantiated VMs after they provisioned them, many of our partners start asking the following obvious question: **How can I detect if a Virtual Machine runs in Azure so that my software can block itself from being started when not running in Azure?**

Unfortunately, as of today there’s no good and simple answer to that. There are various approaches out there which I would like to summarize below. **I think the best possible way as of today (April 2015) is a combination of all of these approaches to make it as hard as possible running your software outside of an Azure VM.**

## Query for DHCP Option 245

The first option is one that originally came up by a fellow peer from our Azure support engineering team. It has been provided for [Windows Virtual Machines as a PowerShell script](https:/gallery.technet.microsoft.com/scriptcenter/Detect-Windows-Azure-aed06d51) and essentially performs the following two actions:

1. Check if the VMBus driver from Hyper-V is active.
2. If so, check the DHCP lease attributes for option “unknown-245”

The option “unknown-245” is an Azure-proprietary option which only gets issued by an Azure DHCP server. Since in Azure you always will get an address via DHCP ([static IPs are also managed by the DHCP and with the REST management API](https://azure.microsoft.com/en-us/blog/static-internal-ip-address-for-virtual-machines/)) you will always (and in theory only) get this option as part of the DHCP lease attributes when your machine runs in Azure.

**For Windows** there is a ready-made PowerShell CmdLet that allows you to detect if a VM runs in Azure: <https://gallery.technet.microsoft.com/scriptcenter/Detect-Windows-Azure-aed06d51>.

**For Linux** you can create a bash-script such as the following one to detect if the option unknown-245 is available to have a first indicator of whether you run in Azure or not:

```bash
if `grep -q unknown-245 /var/lib/dhcp/dhclient.eth0.leases`; then
    echo “Running in an Azure VM”
fi
```

This is currently considered to be the most used and simplest approach to detect if you’re running on Azure that is “good enough”. But for some partners it is understandably not enough, yet…

## Use the Azure Agent as Detection-Strategy

On **Linux VMs in specific**, another approach is reading the configuration from the Microsoft Azure Agent which is always installed on a Linux VM and try to reach it’s ping-counterpart on the host-agent side. If a VM does not run in Azure, trying to reach the host-agent end-point would always result in a timeout. Here’s a sample script for doing so:

```bash
curl –connect-timeout 1 `grep FullConfig /var/lib/waagent/GoalState.1.xml | perl -pe ‘s/<.?FullConfig>//g; s/\s//g’` && echo azure || echo no-azure
```

On **Windows VMs** there’s only an agent available when you explicitly select the VM Agent for VM Extensions to be installed. Some partners are checking if that agent is available and explicitly document for their customers that they MUST install the VM Agent when provisioning a Marketplace Image from the Azure Marketplace for their software to work correctly.

## Checking your external IP Address

If you Virtual Machine has a public endpoint attached, you can also verify the public IP address your VM is using when trying to access other services and compare it against the IP address ranges that are reserved for Azure data centers.

The Azure data center IP address ranges can be downloaded from the Microsoft Download Center here: <http://www.microsoft.com/en-us/download/details.aspx?id=41653>.

Valuable services to get your publicly visible IP address are services such as the http://ifconfig.me/ip which can also be used in a PowerShell script or bash-script easily:

```powershell
function Get-ExternalIP {
    (Invoke-WebRequest ifconfig.me/ip).Content
}
Get-ExternalIP
```

A more complex script can then even automatically download the Azure IP ranges from the Microsoft Download center via the direct URL (which are stored as XML) and try to check if the ranges match.

## Leveraging the Azure REST Management API or CLI-interfaces

Finally, you could also ship your VM with either Azure PowerShell CmdLets or the Azure Cross-Platform CLI shipped and query details about your VM through the rest management API.

But – that has one big step you need to take upfront: this requires you to force the user somehow to provide credentials or a management certificate that gives the VM access to the customer’s subscription in which the VM is deployed so that you can query the details about the VM (which belongs to the customer’s subscription and is owned by the customer… and not you as the provider/creator of the marketplace VM template and offering).

To get this done you need to do e.g. one of the following things:

* Write a very explicit documentation for your customers that explains what they need to do after they provisioned the VM from the Azure Marketplace into their subscription before they can use your software in that VM or VMs.
* Or e.g. write a little provisioning web application which is shipped as part of the VM image that the user needs to browse to immediately after provisioning the VM from the marketplace to enter the remaining details that enable your software or “bootstrapping-scripts” to use the Azure Service Management API or CLI to query additional information about your VM and use that for detecting if you run in Azure or not (e.g. query your public and internal IP and compare with what your VM reports etc.).Of course you need to make sure that this “provisioning-app” you need to build is only active in the provisioned instance after the initial creation from the marketplace to avoid any kind of security issues.

At some point in time in the near future, the Azure Marketplace service will enable publishers of images to require the user providing additional details through the Azure portal as part of the provisioning/creation-process, already. But as long as that’s not possible you need to look at approaches such as the ones I’ve outlined above.

## Final Words

The approaches outlined above are all used by publishers of VM templates in the marketplace today and they work. I know they are not optimal, but we also know that the product group is aware of the challenges and will work on better solutions in the future. For now, the approaches I outlined above are easy and pragmatic ways that at least give you some level of guarantee for detecting of whether a VM and your software runs in Microsoft Azure (public cloud) or not…
