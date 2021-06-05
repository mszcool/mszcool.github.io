---
layout: post
title:  CloudFoundry on Azure in a Hybrid Multi-Cloud World
date:   2016-09-28 12:00:00 +0100
categories: wordpressarchive
tags: Archive Azure VirtualMachines Hybrid HybridCloud CloudNative PaaS
excerpt_separator: <!--more-->
---

This week I was presenting at the [CloudFoundry Summit 2016 Europe](https://www.cloudfoundry.org/community/summits/cfsummit/?summitId=11993) in Frankfurt, of course about running [CloudFoundry on Azure and Azure Stack](https://cfsummiteu2016.sched.org/event/7rVC/private-hybrid-public-cloud-cf-environments-on-microsoft-azure-mario-szpuszta-microsoft?iframe=yes&w=i:100;&sidebar=yes&bg=no#?iframe=yes&w=i:100;&sidebar=yes&bg=no). It was greate being here, especially because one of my two main Global ISV partners I am working with on the engineering side, have been here as well and are even a Gold-sponser of the event. It was indeed an honor and great pleasure for me to be part of this summit here ... and great to finally have a technical session at a non-Microsoft conference, again:) 

Indeed, one reason for that blog-post is because I ran out of time during my session and was able to show only small parts of the last demo.

Anyways, let's get to the more techncial part of this blog-post. My session was all about running CF in Public, Private as well as Hybrid Clouds with Azure being involved in some way. This is **highly relevant** since most enterprises are driving a multi-cloud strategy of some way:
* Either they are embracing Hybrid cloud and run deployments in the public cloud as well as in their own data centers for various reasons or
* they want to distribute and minimize risk by running their solutions across two (or more) public cloud providers.

Despite the fact my session was focused on running Cloud Foundy on Azure, a lot of the concepts and architectural insights presented, can be re-used for other kinds of deployments with other cloud vendors or private clouds, as well.

<!--more-->

#### The basics - Running Cloud Foundry on Azure and Pivotal
Microsoft has developed a [Bosh CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release) that enables bosh-based deployments of Cloud Foundry on Azure. The CPI is entirely developed as an Open Source Project and contributed to the [Cloud Foundry Incubator project](https://github.com/cloudfoundry-incubator) on GitHub.

Based on this CPI, there are two main ways for deploying deploying Cloud Foundry clusters on Microsoft Azure:
* Pivitoal Cloud Foundry via the [Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/) (please read Pivtal docs for updates on what is support and what not).
* Open Source Cloud Foundry either manually as per official [Bosh Documentation](https://bosh.io/docs/azure-resources.html#client) or via Azure Resource Manager Templates provided through the [Azure Quickstart Templates Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/bosh-setup).

There's a very detailed guidance on all of those GitHub repositories available that do explain all the details, I would suggest to follow this one since it is by far the easiest one: [Deploy Cloud Foundry on Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md) and always follow the **via ARM templates** suggestions of the docs.

Finally, in addition to Azure, to completly follow this post you need a 2nd CF cluster running in another cloud. The by far easiest way is to setup a trial account on Pivotal Cloud, which provides you with some sort of "CloudFoundry-as-a-Service". Follow [these steps here](https://run.pivotal.io/) for doing so...

#### A Multi-Cloud CF Architecture with Azure on one side
There are many reasons for multi-cloud environments. Some might include running parts in private clouds because of legal and compliance reasons while others including spreading risk across multiple cloud providers for disaster recovery reasons. The example in this post is focused exactly around the multi-cloud DR case since it covers two public cloud providers:

![architecture](https://raw.githubusercontent.com/mszcool/cfMultiCloudSample/master/images/Figure01-Architecture.png)

* [Azure Traffic Manager](https://azure.microsoft.com/en-us/documentation/articles/traffic-manager-overview/) acts as a DNS-based load balancer. We will configure traffic manager with a Priority-Policy, which essentially leads traffic based on priority and if one cloud has a failure, Traffic Manager will route traffic to the other cloud.
* The [Azure Load Balancer](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-overview/) is a component you get "for free" in Azure and don't really need to take care off. It balances traffic across the front-nodes of your CF cluster and is automatically configured for you if you follow the guidance above for deploying CF on Azure.
* Inside of each CF cluster, we need to make sure to register the DNS names used by Traffic Manager and configure the CF routers to route to our apps in the CF cluster, apropriately.

#### Setting up traffic manager
Let's start with setting up the Azure Traffic Manager since we'll need it's domain name for the configuration of the apps in both Cloud Foundry targets. You can just add Azure Traffic Manager as a Resource to the Resource Group of your Cloud Foundry deployment or any other resource group. In my case, I deployed the Traffic Manager in another resource group as shown in the following screen shot:

![Traffic Manager Setup](https://raw.githubusercontent.com/mszcool/cfMultiCloudSample/master/images/Figure02-TrafficManager.png)

The important piece to take for now is the **Domain Name** of your traffic manager end-points. The actual end-points for traffic manager do not need to be configured at this point in time - we will look at it later.

#### Deploying the sample app to Pivotal Web Services
As a next step, we need to deploy the sample application to Pivotal web services and need to take note of the (probably random) domain name it has associated ot the application.
```bash
$pivotalApiEndpoint="api.run.pivotal.io"
cf login -a $pivotalApiEndpoint
cf target -o $pivotalOrg -s $pivotalSpace
cf push -f ./sampleapp/manifest.yml -p ./sampleapp
cf set-env multicloudapp REGION "Pivotal Cloud"
cf restage multicloudapp
```
To get the domain name and IP, just execute a `cf app multicloudapp` and take note of the domain name as shown in the following figure:

![Pivotal App Domain Name](https://raw.githubusercontent.com/mszcool/cfMultiCloudSample/master/images/Figure03-PivotalDomainName.png)

#### Deploying the App into Cloud Foundry on Azure
The deployment of the sample app into Azure goes exactly the same way, except that we'll need to use different API end-points, organization names and spaces inside of Cloud Foundry:

```bash
$azureCfApiEndpoint="api.$azureCfPublicIp.xip.io"
cf login -a $azureCfApiEndpoint
cf target -o $azureOrg -s $azureSpace
cf push -f ./sampleapp/manifest.yml -p ./sampleapp
cf set-env multicloudapp REGION "Microsoft Azure"
cf restage multicloudapp
```
The **Cloud Foundry API end-point** I used above is the one that is registered by default when using the ARM-based deployment of open source Cloud Foundry with the Azure Quickstart Templates. The DNS-registration mechanism used there is documented [here](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-azuredns).

Also **note the environment variables** I am setting in the scripts above using `cf set-env multicloudapp REGION "xyz"`. Indeed, that is used by our sample application (which is written with Ruby in this case) to output, in which region we are running the app. That way, we can see, if we are directed to the app deployed in Azure or in Pivotal Web Services.

Finally, if you're new to Azure, the best way to find out the public IP which has been created for your CF cluster, is looking up a public IP address in the Azure Portal which has been created inside of the Resource Group for your Cloud Foundry cluster. Another way - if you are a Shell Scripter - would be to use the following command with the Azure Cross Platform CLI:

```bash
azure network public-ip show --resource-group YOUR-RESOURCE-GROUP YOUR-IP-NAME
info:    Executing command network public-ip show
+ Looking up the public ip "YOUR-IP-NAME"
data:    Id                              : /subscriptions/YOUR-SUBSCRIPTION-ID/resourceGroups/YOUR-RESOURCE-GROUP/providers/Microsoft.Network/publicIPAddresses/mszcfbasics-cf
data:    Name                            : YOUR-IP-NAME
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : northeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Static
data:    IP version                      : IPv4
data:    Idle timeout in minutes         : 4
data:    IP Address                      : 52.169.87.212
data:    IP configuration id             : /subscriptions/YOUR-SUBSCRIPTION-ID/resourceGroups/marioszpCfSimple/providers/Microsoft.Network/networkInterfaces/SOME-ID/ipConfigurations/ipconfig1
data:    Domain name label               : marioszpcfsimple
data:    FQDN                            : marioszpcfsimple.northeurope.cloudapp.azure.com
info:    network public-ip show command OK
```

#### Configuring Traffic Manager Endpoints
Next, we need to tell Azure Traffic Manager the endpoints it should direct request which do approach on the DNS record registered with Traffic Manager to. 

In our case, we use a simple Priority-based policy which means, Traffic Manager tries to always direct requests to an endpoint with the more important priority except that endpoint is not responsive. For a full documentation about policy routes, please refer to the [Azure Traffic Manager docs](https://azure.microsoft.com/en-us/documentation/articles/traffic-manager-routing-methods/).

![Traffic Manager Endpoints](https://raw.githubusercontent.com/mszcool/cfMultiCloudSample/master/images/Figure04-TrafficManagerConfig.png)

As you can see from the above, we have two endpoints:
* **Azure Endpoint** which goes against the Public IP that the scripts and Bosh deployed for us when we deployed Cloud Foundry on Azure at the beginning.
* **External Endpoint** which goes against the domain name for the app that Pivotal Web Services has registered for us (something like `multicloudapp-xyz-abc.cfapps.io`).

#### Let's give it a try...
Now, in the previous configuration for Traffic Manager, we defined that the Pivotal Deployment has priority #1 and therefore will be preferred by Traffic Manager for Traffic routing. So, let's open up a browser and navigate to the Traffic Manager DNS name for your deployment (in my screen shots and at my CF session that is `marioszpcfsummithybrid.trafficmanager.net`):

![not working](https://raw.githubusercontent.com/mszcool/cfMultiCloudSample/master/images/Figure05-ItDoesNotWork.png)

Of course, a Cloud Foundry veteran spots immediately, what that means. I am not a veteran in that area, so I was falling into the trap...

#### Configuring Routes in Cloud Foundry
What I forgot when setting this up, originally, was configuring routes for the Traffic Manager Domain in my Cloud Foundry clusters. Otherwise, Cloud Foundry will reject requests coming in through that domain as it does not know about it.

We need to configure the routes on both ends to make it working, as shown below, we're adding the traffic manager domain to the routes and ensure, CF routes traffic from those domains to our multi-cloud sample app:

```bash
$trafficMgrDomain=marioszpcfsummithybrid.trafficmanager.net

#
# First do this for Pivotal
#
cf login -a $pivotalApiEndpoint
cf target -o $pivotalOrg -s $pivotalSpace

cf create-domain $pivotalOrg $trafficMgrDomain
cf create-route $pivotalSpace $trafficMgrDomain
cf map-route multicloudapp $trafficMgrDomain

#
# Then do this for the CF Cluster on Azure
#
$azureCfApiEndpoint="api.$azureCfPublicIp.xip.io"
cf login -a $azureCfApiEndpoint
cf target -o $azureOrg -s $azureSpace

cf create-domain $azureOrg $trafficMgrDomain
cf create-route $azureSpace $trafficMgrDomain
cf map-route multicloudapp $trafficMgrDomain
```
Now let's give it a try, again, and see what happens. This time we should see our Ruby sample app running and showing that it runs in Pivotal since we defined the priority for the Pivotal-based deployment within Azure Traffic Manager.
![it works](https://raw.githubusercontent.com/mszcool/cfMultiCloudSample/master/images/Figure06-ItWorks.png)

#### Fixing Routes on Azure with Traffic Manager
After I indeed did the route mapping on Azure, Traffic Manager still claimed that the Azure-side of the house is **Degraded**, despite having the route configured. Initially, I didn't understand why.

I didn't have this problem when I initially tried this setup before. But when I initially tried this, I did **not have assigned a DNS name to the Cloud Foundry Public IP** in Azure. I've changed that because I tried something else in between and assigned a DNS name to the Azure Public IP for the CF Cluster. This lead traffic manager to route against that DNS name instead of the IP.

For troubleshooting that, I initated a fail-over and stopped the app on the Pivotal side (see next section) to make sure, Traffic Manager would try to route to Azure. A `tracert` finally told me, what was going on:

```powershell
C:\code\github\mszcool\cfMultiCloudSample [master ≡]> tracert marioszpcfsummithybrid.trafficmanager.net

Tracing route to marioszpcfsimple.northeurope.cloudapp.azure.com [52.169.87.212]
over a maximum of 30 hops:

  1     5 ms     5 ms     4 ms  10.10.16.4
  2     2 ms     1 ms     1 ms  80.146.218.2
  3     2 ms     1 ms     2 ms  62.156.233.185
  4     5 ms     5 ms     5 ms  87.190.232.17
  5     8 ms     7 ms     7 ms  f-ed1-i.F.DE.NET.DTAG.DE [62.154.14.118]
```

When looking at the selected routes, we immediately spot, that the traffic manager domain gets resolved to the *.cloudapp.net* domain of the Azure Public IP. So my route on the CF-side of the house was just wrong. The route for Azure should not go against the traffic manager, but rather on the custom domain assigned to the cloud foundry cluster's public IP in Azure:

```powershell
cf map-route multicloudapp marioszpcfsimple.northeurope.cloudapp.azure.com

C:\code\github\mszcool\cfMultiCloudSample [master ≡]> cf routes
Getting routes for org default_organization / space dev as admin ...

space   host   domain                                            port   path   type   apps            service
dev            52.169.87.212
dev            marioszpcfsimple.northeurope.cloudapp.azure.com                        multicloudapp
dev            marioszpcfsummithybrid.trafficmanager.net                              multicloudapp
```

This indeed fixed the situation and finally my Azure deployment was recognized as **Online** on the traffic manager side of the house, as well.

**Important Note:** this fix is needed, only, if you have a public DNS name assigned to your public IP address for the Cloud Foundry cluster in Microsoft Azure. If you just map the public IP address, itself (only do this for static IPs, if at all), then this step is not needed.

#### Testing a failover
Of course, we want to test if our failover strategy really works. For this purpose, we kill the App on the Pivotal-environment by executing the following commands:
```bash
cf login -a $pivotalApiEndpoint
cf target -o $pivotalOrg -s $pivotalSpace
cf stop multicloudapp
```
After that, we need to **wait a while** until traffic manager detects, that the application is not healthy. It then also might take a few seconds or minutes until the DNS record updates are propagated until we see the failover working (the smallest DNS TTL you can set, is 300s as of today).

So watch, what goes on, the simplest way is looking at the Azure Portal and opening up the Azure Traffic Manager configuration. At some point in time we should see, that one of the endpoints changes its status from **Online** to **Degraded**. When opening up a browser and trying to navigate to the traffic manager URL, we should no get redirected to the Azure-based deployment (which we see given our App is outputing the content of the environment variable we did set different for each of the deployments, before):

![failover test](https://raw.githubusercontent.com/mszcool/cfMultiCloudSample/master/images/Figure07-FailoverInAction.png)

#### Final Words
I hope this gives you a nice start in setting up a Multi-Cloud Cloud Foundry environment across Azure and a 3rd-party cloud or your own data center. I will try to continue this conversation on my blog, for sure. There are tons of other cool things to explore with Cloud Foundry in relationship to Azure, and I'll at least try to cover some of those. Let me know what you think by contacting me through [twitter.com/mszcool](http://twitter.com/mszcool)!