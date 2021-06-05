---
layout: post
title:  SAP HANA Express Quickstart Template (working-draft)
date:   2017-05-14 12:00:00 +0100
categories: wordpressarchive
tags: Archive Azure VirtualMachines SAP SAPHana
excerpt_separator: <!--more-->
---

This is an exciting week for me... although I am usually not that much into attending Business-oriented conferences, over the past two years I did so by attending SAP Sapphire.

Up until know, that mainly is caused by the fact that I've contributed some aspects to what was announced with regards to the partnership between Microsoft and SAP at the conference. Last year, my part was mainly about [Office 365 with the work I've supported for Concur, Ariba, Fieldglass and SuccessFactors as well as the Sports Basement Demo](https://www.youtube.com/watch?v=fpCdD6e0sjM) which was shown in the Key Note to highlight the HANA One Certification for Azure in our DS14 VM-Series at that time.

While I cannot write about the major project I supported for this year, yet, here's a little nugget to get started with - **[a Quick Start Template for SAP HANA Express](https://www.sap.com/developer/topics/sap-hana-express.html)**!

<!--more-->

*Important Note:* While I am working for Microsoft, this blog summarizes my personal opinions and my personal understanding of topics. This means that all I am writing here is not related to Microsoft's official opinion, at all! If you want to get that view, look at the Azure Blog or Jason Zander's Blog for official announcements!

*Important Note:* The Pull-Request into the official Microsoft Azure Quick Start Templates GitHub repository is not completed, yet. Therefore, the link still redirects to the working branch in my GitHub repository. I'll update the blog-post as soon as the pull request is completed (there's currently a general issue with the Travis CI pipeline used for validation on the Azure Quick Start Templates GitHub repository that caused an unexpected delay for the pull request to go through in time for Sapphire).

SAP HANA Express
----------------

As you might know, already, SAP HANA is SAP's in-memory Database Engine which is supposed to back all of the major future releases of SAP's core business suites centered around S/4HANA. But, HANA can be used as a stand-alone database system for developing custom solutions, as well. The Sports Basement Demo on Azure DS14 Instances from last year's Sapphire Key Note is an example of that. It was a plain HANA Database fronted by a Java Web Application.

Now, within last year's Sapphire and now, SAP released a version of SAP HANA that is free for development and testing purposes up to 32GBs of RAM called HANA Express.

For more details, you should navigate to the SAP HANA Express Homepage to get the full picture and the official view:

<https://www.sap.com/developer/topics/sap-hana-express.html>

Azure Quick-Start-Templates
---------------------------

Now, shortly before Sapphire some folks from Microsoft and SAP approached me for creating an Azure Marketplace Image for HANA Express. That's something we're working on, but it is not something that can be done in just a few days. Too short... but since HANA Express clearly addresses developers, I thought a good solution that can be implemented within a few days is an **Azure Quick-Start-Template**.

For those of you who are new to Azure: Azure Quick-Start-Templates are open source based Azure Resource Manager Templates and Deployment Scripts which can be used to quickly spin-up Solutions on Azure. Many of those are used as a learning resource, but some of them can definitely be used for dev/test scenarios or even as a starting point for production scenarios.

All of these are available under the following two links:

* Browsing/Search:
  <https://azure.microsoft.com/en-us/resources/templates/>
* Source Code:
  <https://github.com/Azure/azure-quickstart-templates/>

Now, the main point with these quick start templates is, that they're automating most of the setup/provisioning procedure by using Scripts and Templates so you can get started, quickly.

SAP HANA Express Quick Start Template
--------------------------------------

I decided to build such a template for HANA Express and make it available as part of the Quick-Start-Templates. This Quick-Start works with SAP HANA Express 2.0 SPS1 and it also should work with other version, but I've only tested it with this one.

<http://aka.ms/sap-hana-express-quickstart>

There's just one caveat: since SAP requires you to accept the EULA for SAP HANA Express, you first need to go to SAP's HANA Express Home Page, register and download the SAP HANA Express Setup Images manually before being able to start using the Quick Start Template I've created. But from there on you can start using the template. The basic workings of the template are:

1. First you register with SAP and download the HANA Express Setup Packge.
2. Then you use the quick start template to upload the setup package into your private Azure Storage Account.
3. From that moment forward you can use the Azure Resource Manager Template included in the template to deploy as many HANA Express Instances into your own subscription as you want.

*Starting with Step 2, everything is automated with Scripts and Templates. That means, only the first step - downloading the setup packges and accepting the EULA at the SAP HANA Express Setup Homepage is something you need to do manually*. Sure, a Marketplace Image would be more convenient, but we'll work with SAP on that...

All the details are explained in the sections below in this blog post including how you can validate, that the installation really went well at the end of the entire process.

*Important Note*: Please don't forget, that quick start templates are not something that is backed by Microsoft Support of any means. They are here to help you getting started on your own, they are full open source and maintained at a best effort basis!

Requirements
------------

Before moving on, all you need on your local machine are the following assets/tools:

* A Linux or Mac with Bash, or Windows with [Bash on Ubuntu on Windows](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide)
* The [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) installed in your Bash environment
* An [Azure Subscription](https://azure.microsoft.com/en-us/free/)
* Optionally HANA Tools or HANA Developer Studio

Register Download HANA Express Setup from SAP
---------------------------------------------

Yes, that's the first step. It needs to be done for two reasons: first, you need to accept SAP's EULA and second SAP will inform you about important updates and service releases for HANA when registering at the registration page.

That said, the first thing you do is [navigating to the SAP HANA Express Home Page](https://www.sap.com/developer/topics/sap-hana-express.html) to register with SAP and Accept the EULA:

![HANA Express]((https://raw.githubusercontent.com/mszcool/azure-quickstart-templates/original/mszcool-hanaexpress/sap-hana-express/images/Figure01.png))

![HANA Express Download Manager Option](https://raw.githubusercontent.com/mszcool/azure-quickstart-templates/original/mszcool-hanaexpress/sap-hana-express/images/Figure02.png)

Next, you'll need to use the SAP Download Manager to download the SAP HANA Express Setup packages. The Download Manager exists as native version for Linux or Windows or as a Cross-Platform Version for Java (distributed as JAR-package). I've used the JAR-package version, but it should not matter at all.

What matters is selecting the right type of setup packages. The quick-start-template I've built is tested for the Server-Version, only, without XA Advanced services. So you should select the following for download when using the SAP HANA Express Download Manager:

![HANA Express Server Only Download Option](https://raw.githubusercontent.com/mszcool/azure-quickstart-templates/original/mszcool-hanaexpress/sap-hana-express/images/Figure03.png)

The downloaded setup package will appear as TAR-Archive in your local Downloads-folder (or wherever you downloaded it to). It should be called something along the lines of `hxe.tgz`.

Upload the HANA Express to your Azure Storage Account
-----------------------------------------------------

For automating the setup procedure of SAP HANA Express inside of an Azure Virtual Machine, the HANA Express Setup packages need to be available for download through an automation script! To avoid uploading the setup package (~ 1.6GBs of data) with each new VM, the best approach is uploading that into an [Azure storage account](https://docs.microsoft.com/en-us/azure/storage/storage-introduction) and using a [shared access signature](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1) to feed the setup files into the provisioning script for Virtual Machines.

Now, performing those steps can be done manually by using a tool such as the Azure Storage Explorer. But I decided to automate the procedure using the [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Assuming that you have your SAP HANA Express Setup packages downloaded to the folder /mnt/c/temp/hxe.tgz, you can execute the following command:

``` bash
# Login into your Azure Subscription using the Azure CLI 2.0
az login

# Create a resource group for the storage account
az group create --name "sampleresourcegroupname" --location "westeurope"

# Upload the HANA Express Setup Files to your Azure Storage Account
./prepare-hxe-setup-files.sh sampleresourcegroupname samplestorageaccountname samplecontainer westeurope /home/mydirectory/hxe.tgz
```

Now, as I mentioned before, the automated setup of HANA Express happens in a script that runs in the post-provisioning phase of the Azure VM. That means, this script needs to have access to those setup files for an automated download without user interaction. To enable that scenario, the `prepare-hxe-setup-files.sh`-Script of my Quick Start uploads the setup packages for HANA Express to an Azure Storage Account and generates a Shared Access Signature URL which allows to simply download the packages using that Signature as a means of authentication by using wget or any similar shell-tool.

The following Screen-Shot shows the output of the `prepare-hxe-setup-files-sh`-script. You **should especially take note of the Shared Access Signature URL the script outputs** at the end!

![Output of prepare-hxe-setup-files.sh](https://raw.githubusercontent.com/mszcool/azure-quickstart-templates/original/mszcool-hanaexpress/sap-hana-express/images/Figure04.png)

Deploy an SAP HANA Express using the templates
----------------------------------------------

With the Setup packages for SAP HANA Express uploaded to an Azure Storage Account and the Storage Shared Access Signature generated as mentioned above, you can deploy as many SAP HANA Express Virtual Machines as you need to.

*Important Note*: the script `prepare-hxe-setup-files.sh` above generates Shared Access Signatures that are valid for a year. That means, after a year, you need to run the script, again, to generate a new Shared Access Signature. Note that the script is smart in detecting, if the files have been uploaded, already, and if so it generates the signature for the existing blob instead of uploading, again!

When using the quick-start-template, you can either use the "Deploy-To-Azure"-Button presented on the [landing page of the Quick Start](http://aka.ms/sap-hana-express-quickstart) or you fill out the parameters in the `azuredeploy.parameters.json`-file as shown below and deploy the template via PowerShell or the Azure CLI:

![Parameters filled in the azuredeploy.parameters.json](https://raw.githubusercontent.com/mszcool/azure-quickstart-templates/original/mszcool-hanaexpress/sap-hana-express/images/Figure05.png)

After you've filled out the parameters - the screen shot above shows the minimum ones you need to fill out - you can move ahead and deploy the template using code similar to the following:

``` bash
# Create a resource group for your HANA Express VM Resources
az group create --name "samplehanaexpressgroup" --location "westeurope"

# Deploy the template with the filled parameters file
az group deployment create --resource-group="samplehanaexpressgroup" \
                           --template-file="azuredeploy.json" \
                           --parameters="@azuredeploy.sample.parameters.json" \
                           --name="samplehanaexpress"
```

The output of that script should look similar to the following screen shot:
![Parameters filled in the azuredeploy.parameters.json](https://raw.githubusercontent.com/mszcool/azure-quickstart-templates/original/mszcool-hanaexpress/sap-hana-express/images/Figure06.png)


Validating the Installation
---------------------------

So far so good, if the output looks similar to the screen shot above, then you should be all set! But you can of course validate your installation - in two ways: using regular HANA tools to see if your instance is responsive or look at the installation logs from the provisioning process.

For that, you need to understand some background. I am using [Azure Custom Script Extensions for Linux](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/extensions-customscript) to automatically execute the HANA Installation with all required pre-requisites during the post-VM provisioning phase of the Azure Virtual Machine. That is expressed in the Azure Resource Manager Template with the following code:

``` JSON
... REST OF THE TEMPLATE ...

    {
        "type": "extensions",
        "name": "hxeinstallextension",
        "apiVersion": "2016-04-30-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmNamePrefix'))]"
        ],
        "properties": {
            "publisher": "Microsoft.Azure.Extensions",
            "type": "CustomScript",
            "typeHandlerVersion": "2.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "fileUris": [
                    "[parameters('hxeInstallScriptUrl')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sudo ./', parameters('hxeInstallScriptName'), ' \"', parameters('hxeSetupFileUrl'), '\" \"', parameters('hxeMasterPwd'), '\" && exit 0')]"
            }
        }
    }

... REST OF THE TEMPLATE ...
```

This part of the template shows, that after the Virtual Machine Resource has been provisionied, it uses the Azure Virtual Machine Agent to run the script specified in the template. This script is downloaded from the Quick-Start-Templates GitHub repository, directly. So no further steps needed to enable this.

If you now want to validate, whether the installation script from SAP HANA Express ran, successfully, you first should review the deployment logs within the Azure Portal similar to what's shown in the following screen shot:

![Azure Portal Deployment Log](https://raw.githubusercontent.com/mszcool/azure-quickstart-templates/original/mszcool-hanaexpress/sap-hana-express/images/Figure07.png)

If you still need to see more, then you just need to SSH into the virtual machine created (refer to the DNS name specified in the `azuredeploy.parameters.json` as per the screen shots above) and output the content of the `stdout` and `stderr` files within the `/var/lib/waagent/custom-script/download/0` directory similar to what's shown in the following screen shot:

![Virtual Machine Deployment Log](https://raw.githubusercontent.com/mszcool/azure-quickstart-templates/original/mszcool-hanaexpress/sap-hana-express/images/Figure08.png)

When you look at the output, you'll quickly realize, how much this really accelerates you. The setup script automatically performs the following steps for you:

* Install the needed Oracle JDK on your machine
* Install required library packages using zypper
* Download and Extract the HANA Express Setup Packages to the VM
* Install HANA Express on the VM

Final Words
-----------
The post above went quite into the details of every step of how the quick-start works. But the essence can literally be done within about 30min. depending on how fast your Internet connection is:

* Register for an Azure Subscription if you don't have one, yet
* Setup the Azure CLI 2.0 on your machine if not done so, yet
* Register with SAP
* Download the SAP HANA Express Setup Packages
* Execute the script `prepare-hxe-setup-files.sh` and note the generated Shared Access Signature
* Click the "Deploy-to-Azure Button" or update the parameters file and execute `az group deployment create`

All of the needed pre-requisites and of course SAP HANA Express itself gets set up for you and within about 30min. you have an instance of it running in Microsoft Azure. I hope you find this more valuable and helps you to accelerate the setup of dev/test environments with SAP HANA Express since you don't need to walk through all of the needed setup steps, manually.