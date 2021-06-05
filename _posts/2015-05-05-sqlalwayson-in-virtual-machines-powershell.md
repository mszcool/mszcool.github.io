---
layout: post
title:  SQL Server Always-On Setup across two Azure regions (PowerShell, Classic)
date:   2015-05-05 12:00:00 +0100
categories: wordpressarchive
tags: Archive Azure VirtualMachines SQLServer Databases
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

Last December I started working with two of my peers, Max Knor and Igor Pagliai, with a partner in Madrid on implementing a Cross-Data Center [SQL Server AlwaysOn availability group](https:/technet.microsoft.com/en-us/library/hh510230(v=sql.110).aspx) setup for a financial services solution which is supposed to be provided to 1000s of banks across the world running in Azure. Igor posted about our setup experience which we partially automated with Azure PowerShell and Windows PowerShell – see [here](https://docs.microsoft.com/en-us/archive/blogs/igorpag/sql-server-2014-high-availability-and-multi-datacenter-disaster-recovery-with-multiple-azure-ilbs).

<!--more-->

At the time of writing this article, the partner’s software still requires SQL Server in VMs as opposed to Azure SQL Databases because of some legacy functions they use from full SQL Server – therefore this decision. One of the bold goals was to fully enable the partner and their customers to embrace DevOps and continuous delivery across multiple environments. For this purpose we wanted to FULLY AUTOMATE the setup of their application together with an entire cross-data-center SQL Server AlwaysOn environment as outlined in the following picture:

![SqlAlwaysInVmsOnArchitectureOverview](/images/posts2015/20150505-SQLAlwaysOnAzurePowerShell-01.png)

In December we did a one-week hackfest to start these efforts. We successfully did setup the environment, but partially automated, only. Over the past weeks we went through the final effort to fully automate the process. I’ve published the result on my github repository here:

<https:/github.com/mszcool/SqlAlwaysOnAzurePowerShellClassic>

**Note:** Not Azure Resource Manager, yet

Since Azure Resource Manager, which would allow us to dramatically improve the performance and reduce the complexity of the basic Azure VM environment setup, is still in Preview (at the time of writing) , we were forced to use traditional Azure Service Management.

But about 50%-60% of the efforts we have done are re-usable given the way we built up the scripts. E.g. all the database setup and custom service account setup which is primarily built on-top of [Azure Custom Script VM Extensions](https://azure.microsoft.com/blog/2014/04/24/automating-vm-customization-tasks-using-custom-script-extension/) can be re-used after the basic VM setup is completed. We are planning to create a next version of the scripts that does the fundamental setup using Azure Resource Groups because we clearly see the advantages.

## Basic Architecture of the Scripts

Essentially the scripts are structured into the following main parts which you would need to touch if you want to leverage them or understand them for learning purposes as shown below:

![SqlAlwaysOnScriptsDesign](/images/posts2015/20150505-SQLAlwaysOnAzurePowerShell-02.png)

1. `Prep-ProvisionMachine.ps1` (prepare deployment machine)
   A basic script you should execute on a machine before starting first automated deployments. It installs certificates for encrypting passwords used as parameters to Custom Script VM Extensions as well as copying the basic PowerShell modules into the local PowerShell module directories so they can be found. 
2. `Main-ProvisionConfig.psd1` (primary configuration)
   A nice little trick by Max which is nice to provide at least some sort of declarative configuration was to build a separate script file that creates an object-tree with all the configuration data typically used for building up the cluster. It contains cluster configuration settings, node configuration settings and default subscription selection data. 
3. `Main-ProvisionCrossRegionAlwaysOn.ps1` (main script for automation)
   This is the main deployment script. It performs all the actions to setup the entire cross-region cluster including the following setups: 
   * Setup your subscription if requested 
   * Setup storage accounts if they do not exist, yet 
   * Upload scripts required for setup inside of the VMs to storage 
   * Setup cloud services if requested 
   * Create Virtual Networks in both regions (Primary/Secondary) 
   * Connect the Virtual Networks by creating VPN Gateways 
   * Set the primary AD Forest VM and the Forest inside of the VM 
   * Setup secondary AD DC VMs including installing AD 
   * Provision SQL Server VMs 
   * Setup the Internal Load Balancer for the AlwaysOn Listener 
   * Configure all SQL VMs to have AlwaysOn enabled 
   * Configure the Primary AlwaysOn node with the initial database setup 
   * Join secondary AlwaysOn nodes and restore databases for sync 
   * Configure a file-share based witness in the cluster 
4. `VmSetupScripts` Folder
   This is essentially a folder with a series of PowerShell scripts that do perform single installation/configuration steps inside of the Virtual Machines. They are downloaded with a Custom Script VM Extension into the Virtual Machines and executed through VM Extensions, as well.

## Executing the Script and Looking at the Results

Before executing the main command make sure to execute `.\Prep-ProvisionMachine.ps1` to setup certificates or import the default certificate which I provide as part of the sample. If you plan to seriously use those scripts, please create your own certificate. `Prep-ProvisionMachine.ps1` provides you with that capability assuming you have `makecert.exe` somewhere on your machines installed (please check Util-CertsPasswords for the paths in which I look for `makecert.exe`).

```powershell

# To install a new certificate
.\Prep-ProvisionMachine.ps1

# To install a new certificate (overwriting existing ones with same Subject Names)
.\Prep-ProvisionMachine.ps1 -overwriteExistingCerts

# Or to install the sample certificate I deliver as part of the sample:
.\Prep-ProvisionMachine.ps1 -importDefaultCertificate

# Then everything should be fine to execute the main script.
# If you don’t specify the certificate-related parameters as shown below I assume you use my sample 
# default certificate I include in the repository to encrypt secrets pushed into VM Custom Script Extensions.

# Enter the Domain Admin Credentials
$domainCreds = Get-Credential

# Perform the main provisioning
.\Main-ProvisionCrossRegionAlwaysOn.ps1 -SetupNetwork -SetupADDCForest -SetupSecondaryADDCs -SetupSQLVMs -SetupSQLAG `
                                        -UploadSetupScripts -ServiceName "mszsqlagustest" -StorageAccountNamePrimaryRegion "mszsqlagusprim" ` -StorageAccountNameSecondaryRegion "mszsqlagussec" -RegionPrimary "East US" -RegionSecondary "East US 2" `
                                        -DomainAdminCreds $domainCreds -DomainName "msztest.local" -DomainNameShort "msztest" -Verbose
```

The following image shows several aspects in action such as the failover cluster resources which are part of the AlwaysOn availability group as well as SQL Server Management Studio accessing the AlwaysOn Availability Group Listener as well as SQL Nodes, directly. Click on the image to enlarge it and see all details.

![SqlAlwaysOnResultsAfterExecution](/images/posts2015/20150505-SQLAlwaysOnAzurePowerShell-03.png)

Please note that the failover in the secondary region needs to happen MANUALLY by executing either a [planned manual failover](https://technet.microsoft.com/en-us/library/hh231018(v=sql.110).aspx) or a [forced manual failover](https://technet.microsoft.com/en-us/library/ff877957(v=sql.110).aspx) as documented on MSDN. Failover in the primary region (from the first to the second SQL Server) is configured to happen automatically. In addition on Azure it means to take the IP cluster resource for the secondary region online which by default is offline in the cluster setup as you can see on the previous image.

## Customizing the parts you should customize

As you can see in the image above, the script creates sample databases which it sets up for the AlwaysOn Availability Group to be synchronized across two nodes in the main. This happens based on *.sql scripts you can add to your configuration. To customize the SQL Scripts and Databases affected, you need to perform the following steps:

* Create *.sql scripts with T-SQL code that creates the databases you want to create as part of your AlwaysOn Availability Group. 
* Copy the *.sql Files into the VmSetupScripts directory BEFORE starting the execution of the main script. That leads to have them included into the package that gets pushed to the SQL Server VMs 
* Open up the main configuration file and customize the database list based on the databases created with your SQL scripts as well as the list of SQL Scripts that should be pushed into osql.exe/sqlcmd.exe as part of the setup process for creating the databases. 
* Also don’t forget to customize the subscription name if you plan to not override it through the script-parameters (as it happens with the example above).

The following image shows those configuration settings highlighted (in our newly released [Visual Studio Code editor](https://code.visualstudio.com/) which also has basic support for PowerShell):

![SqlAlwaysOnScriptsCustomizingWithVsCode](/images/posts2015/20150505-SQLAlwaysOnAzurePowerShell-04.png)

## Fundamental challenges

The main script can primarily be seen as a PowerShell workflow (we didn’t have the time to really implement it as a Workflow, but that would be a logical next step after applying Azure Resource Groups). It creates one set of Azure VMs after another and joins them to the virtual networks it has created before. It then executes scripts on the Virtual Machines locally which are doing the setup by using Azure VM Custom Script Extensions. Although custom script extensions are cool, you have two main challenges with them for which the overall package I published provides re-usable solutions:

* Passing “Secrets” as Parameters to VM Custom Script Extensions such as passwords or storage account keys in a more secure way as opposed to clear-text. 
* Running Scripts under a Domain User Account as part of Custom Script Extensions that require full process level access to the target VMs and Domains (which means PowerShell Remoting does not work in most cases even with CredSSP enabled … such as for Cluster setups).

For these two purposes the overall script package ships with some additional PowerShell Modules I have written, e.g. based on a blog-post from my colleague Haishi Bai [here](https://azure.microsoft.com/blog/2014/07/15/automating-sql-server-vm-configuration-using-custom-script-extension/).

## Running Azure VM Custom Script Extensions under a different User

`Util-PowerShellRunAs.psm1` includes a function called Invoke-PoSHRunAs which allows you to run a target script with its parameters under a different user account as part of a custom script VM Extension. A basic invocation of that script looks as follows:

```powershell
$scriptName = [System.IO.Path]::Combine($scriptsBaseDirectory, "Sql-Basic01-SqlBasic.ps1") 
Write-Host "Calling into $scriptName"
try {
    $arguments = "-domainNameShort $domainNameShort " + `
                 "-domainNameLong $domainNameLong " +  `
                 "-domainAdminUser $usrDom " +  `
                 "-dataDriveLetter $dataDriveLetter " +  `
                 "-dataDirectoryName $dataDirectoryName " +  `
                 "-logDirectoryName $logDirectoryName " +  `
                 "-backupDirectoryName $backupDirectoryName " 
    Invoke-PoSHRunAs -FileName $scriptName -Arguments $arguments -Credential $credsLocal -Verbose:($IsVerbosePresent) -LogPath ".\LogFiles" -NeedsToRunAsProcess
} catch {
    Write-Error $_.Exception.Message
    Write-Error $_.Exception.ItemName
    Write-Error ("Failed executing script " + $scriptName + "! Stopping Execution!")
    Exit
}
```

This function allows you to either run through PowerShell remoting or in a separate process. Many setup steps of the environment we setup do actually not work through PowerShell remoting because they rely on impersonation/delegation or do PowerShell Remoting on their own which imposes several limitations. Therefore, the second option this script provides is executing as a full-blown process. Since Custom Script Extensions to run as local system, it is nevertheless not as simple as just doing a Start-Process with credentials being passed in (or a System.Diagnostics.Process.Start() with different credentials). Local System does not have those permissions, unfortunately. So the work-around is to use the Windows Task Scheduler. For such cases the function performs the following actions:

* Schedule a task in the Windows Task Scheduler with the credentials needed to run the process as. 
* Manually start the task using PowerShell cmdLets 
* (Start-ScheduledTask -TaskName $taskName)
* Wait for the task to be finished from running 
* Look at the exit code 
* Throw an Exception if the exit code is non-zero, otherwise assume success 
* Delete the task again from the task scheduler

This “work-around” helped us to completely execute the entire setup steps successfully. We were also discussing with the engineers building the SQL AlwaysOn single-data-center Azure Resource Group template that is available for single-data-center deployments in the new Azure Portal, today. They are indeed doing the same thing, details are just a bit different.

## Encrypting Secrets Passed to Custom Script VM Extensions

Sometimes we were just required to pass secret information to custom script extensions such as storage account keys. Since Azure VM Custom Script Extensions are logged very verbose, it would be a piece of cake to get to that secret information by doing a Get-AzureVM and looking at the ResourceExtensionStatusList member which contains the status and detailed call information for all VM Extensions. Therefore, we wanted to encrypt secrets as they are passed to Azure VM Extensions. The basic (yet not perfect) approach works based on some guidance from a [blog post from Haishi Bai](https://azure.microsoft.com/blog/2014/07/15/automating-sql-server-vm-configuration-using-custom-script-extension/) as mentioned earlier. I’ve essentially written another PowerShell module (Util-CertsPasswords) which can perform the following actions:

* Create a self-signed [certificate as per guidance on MSDN for Azure](https://msdn.microsoft.com/en-us/library/azure/gg551722.aspx). 
* Encrypt Passwords using such a certificate and return a base64-encoded, encrypted version. 
* Decrypt Passwords using such a certificate and return the clear-text password.

In our overall workflow all secrets including passwords and storage account keys which are passed to VM Custom Script Extensions as parameters are passed as encrypted values using this module. Using Azure CmdLets we make sure that the certificates are published with the VM as part of our main provisioning script as per [Michael Washams guidance](https://michaelwasham.com/2012/08/23/deploying-certificates-with-windows-azure-virtual-machines-and-powershell/) from the Azure Product group. Every script that gets executed as part of a custom VM Script Extension receives an encrypted password and uses the module I’ve written to decrypt it and use it for the remaining script such as follows:

```powershell
#
# Import the module that allows running PowerShell scripts easily as different user
#
Import-Module .\Util-PowerShellRunAs.psm1 -Force
Import-Module .\Util-CertsPasswords.psm1 -Force

#
# Decrypt encrypted passwords using the passed certificate
#
Write-Verbose "Decrypting Password with Password Utility Module..."
$localAdminPwd = Get-DecryptedPassword -certName $certNamePwdEnc -encryptedBase64Password $localAdminPwdEnc 
$domainAdminPwd = Get-DecryptedPassword -certName $certNamePwdEnc -encryptedBase64Password $domainAdminPwdEnc 
Write-Verbose "Successfully decrypted VM Extension passed password"
```

The main provisioning script encrypts the passwords and secrets using that very same module before being passed into VM Custom Script Extensions as follows:

```powershell
$vmExtParamStorageAccountKeyEnc = `
    Get-EncryptedPassword -certName $certNameForPwdEncryption `
                           -passwordToEncrypt ($StorageAccountPrimaryRegionKey.Primary)
```

## Final words and more...

As I said, there are lots of other re-usable parts in the package I’ve just published on my Github Repository which even can be used to apply further setup and configuration steps on VM environments which have entirely been provisioned with Azure Resource Groups and Azure Resource Manager. A few examples:

* Execute additional Custom Script VM Extensions on running VMs. 
* Wait for Custom Script VM Extensions to complete on running VMs. 
* A ready-to-use PowerShell function that makes it easier to Remote PowerShell into provisioned VMs.

We also make use of an [AzureNetworking PowerShell module](https://archive.is/o/VsJuo/https:/gallery.technet.microsoft.com/scriptcenter/Azure-Networking-e52cbf92) published on the Technet Gallery. But note that we also made some bug-fixes in that module (such as dealing with “totally empty VNET configuration XML files”). Generally the experience of building these ~2500 lines of PowerShell code was super-hard but a great learning experience. I am really keen to publish the follow-up post on this that demonstrates how much easier Azure Resource Group templates to make such a complex setup.

Also I do hope that we will have such a multi-data-center template in the default gallery soon since it is highly valuable for all partners and customers that do need to provide high-availability across multiple data centers using SQL Server Virtual Machines. In the meantime we will try to provide a sample based on this work above as soon as we can have time/resources for implementation.

Finally – thanks to [Max Knor](http://blog.knor.net/) and [Igor Pagliai](https://docs.microsoft.com/en-us/archive/blogs/igorpag) – without their help we would not have achieved these goals at this level of completeness!
