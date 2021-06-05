---
layout: post
title:  NServiceBus for Hybrid and Portable Cloud Solutions using Messaging
date:   2016-04-01 12:00:00 +0100
categories: wordpressarchive
tags: Archive Azure VirtualMachines Hybrid HybridCloud Messaging .NET
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

[NServiceBus](http://docs.particular.net/nservicebus/) is a very popular messaging and workflow framework for .NET developers across the globe. This week a few peers of mine and I were working for one of our global ISV partners to evaluate, if NServiceBus can be used for Hybrid Cloud and portable solutions, that can be moved seamless from On-Premises to the Public Cloud and vice versa.

My task was to evaluate, if NServiceBus can be used with both, Microsoft Azure Service Bus in the public cloud as well as Service Bus 1.1 for Windows Server in the private cloud. It was a very interesting collaboration and finally I got to write some prototype code for one of our partners, again. How cool is that - doing interesting stuff and at the same time it helps a partner. That's how it should be!

<!--more-->

Part #1: On-Premises Service Bus 1.1 Environment
------------------------------------------------
The journey and prototyping began with setting up an On-Premises Service Bus 1.1 environment in my home lab. Fortunately there are some good instructions out there, but of course nothing goes without any pitfalls. Here's a good set of instructions to start with - note that I did setup an entire Azure Pack express setup, which is clearly optional. But it makes things more convenient, especially for presentations, since it provides the nice, good old Azure Management Portal experience for your Service Bus on-premises. Here's where you should look at how-to setup things:

- [Install the Azure Pack Express Setup](https://technet.microsoft.com/en-us/library/dn296439.aspx>)
    * This shows, how-to setup a basic Azure Pack environment using Web Platform Installer.
    * I did not run into any problems installing it on a Hyper-V Box on my Home Lab. So it should be fairly straigth forward.
    * You can install all on a single machine. What you need is SQL Server (Express is sufficient) pre-installed.
- [Install Service Bus for Windows Server](https://msdn.microsoft.com/en-us/library/dn441412.aspx)
    * Again this happens via Web Platform Installer. With this I had a little challenge. Unfortunately as of writing this article, the link to the required version of the Windows Fabric in the Web Platform Installer was broken. I've uploaded it on my public OneDrive for convenience. You find it [here](https://onedrive.live.com/redir?resid=D37C9D7BFBCE8418!449&authkey=!ABMFFxRSyhq0XAc&ithint=folder%2cmsi). But I've had conversations with the product team, they will fix the broken link. So when you try it, it might work, already.
- [Configure Service Bus using the Wizard](https://msdn.microsoft.com/en-us/library/dn441425.aspx).
    * After installing you need to configure Service Bus for Windows Server. That happens through a Wizard. It essentially allows you to configure endpoints, ports and certificates used for security purposes for Service Bus 1.1 for Windows Server.
    * The configuration failed on the first attempt because something went wrong with installing the Service Bus patch. Uninstall and re-install did solve the problem.
- [Configure Service Bus for Windows Server for the Azure Pack Portal](https://msdn.microsoft.com/en-us/library/dn440945.aspx)
    * That's the final step to get the Azure Pack management portal experience for Service Bus 1.1 for Windows Server.
    * If you are fine with managing Service Bus through PowerShell, you can skip the entire Azure Pack Express stuff, start with Service Bus 1.1 right away and [manage it through PowerShell](https://msdn.microsoft.com/en-us/library/dn441434.aspx).
    
At the end of this journey, which took me about half a day overall starting from scratch and figuring out the little gotchas mentioned above, I had a lab environment to test against. I am a fan of using [Royal TS](https://www.royalapplications.com/ts/win/features), hence the screen shot with my on-premises Service Bus as web pages embedded in Royal TS:

![RoyalTsAzureOnPremSetup](/images/posts2016/20160401-figure01.png)

Part #2: NServiceBus and Azure Service Bus
------------------------------------------
The second part of the challenge was easy - figure out if [NServiceBus](http://docs.particular.net/nservicebus/) supports Azure Service Bus, already. Because that would give us a good starting point, wouldn't it!? Here are the docs for the NServiceBus transport extension for the Azure Serivce Bus: [source code](http://docs.particular.net/nservicebus/azure/azure-servicebus-transport).

But the point is, the earliest version that supports Azure seems to be NServiceBus v5.0.0 and they started with a Microsoft.ServiceBus.dll above 3.x in the code-base. This version of the library is not compatible with Service Bus 1.1 for Windows Server. So I had to dig into the source code and back-port the library. Fortunately, Particular is open sourcing most of the framework's bits and pieces on GitHub - so it does for the [NServiceBus.AzureServiceBus connector here](https://github.com/Particular/NServiceBus.AzureServiceBus/tree/support-6.2). Note that I am referring to the version 6.2 of the implementation, directly, since that works with NServiceBus 5.0.0 which our global ISV partner is using at this point in time. I also tried back-porting the current development branch, but that turned out to be way more complex and risky. And it was not needed for the partner, either:)

Part #3: Back-Porting to Microsoft.ServiceBus.dll v2.1
------------------------------------------------------
So the needed step is to back-port to a Service Bus SDK library that also works with Service Bus 1.1. Service Bus for Windows Server recently received a patch to work with .NET 4.6.1, but it has not received any major updates since its original release. So it is behind with regards to its APIs compared to Service Bus in Azure.

I've done all of the steps below on my GitHub repository in a fork of the original implementation. Note that you should only look at my work in the branch 'support-6.2' which is the one that works with NServiceBus 5.0.0. The rest is considered to be experiments as we speak right now:)

[Here is the link to my GitHub repo and the fork!!](https://github.com/mszcool/NServiceBus.AzureServiceBus-SB1.1-WinSrv/tree/support-6.2)

The first step for doing so was to remove the NuGet package and replace it with one that works with Service Bus for Windows Server. Microsoft fortunately released a separate NuGet package that contains the version that is compatible with Service Bus for Windows Server:

![NugetPackageSelectionTrick](/images/posts2016/20160401-figure02.png)

The rest was all about looking where the NServiceBus-implementation is using features that are not available in version 2.1 of the Service Bus SDK and testing it against my Service Bus 1.1 for Windows Server lab setup. I think the best place to look at what actually changed is by looking at the change-logs on my GitHub-Repository:

* [Initial back-port with most code changes (click here to open details on GitHub)](<https://github.com/mszcool/NServiceBus.AzureServiceBus-SB1.1-WinSrv/commit/81a84866cb12898d95a2954fbf42793b23136346>)
    * Update the NuGet Package to "ServiceBus.v1_1" instead of "WindowsAzure.ServiceBus".
    * Remove `EnablePartitioning` because that's not supported on SB 1.1.
    * Use `MessagingFactory.CreateFromConnectionString()` instead of `MessagingFactory.Create()` because the latter one does not assume different ports on different EndPoints for different APIs Service Bus is exposing. But that's typically the case on default-setups of Service Bus for Windows Server (see my first ScreenShot).
    * I also added some regular expressions to detect, if a Service Bus Connection String is one for on-premises or the public cloud to keep most of the default behaviors when connecting against the public cloud true. See the code-snippet below. It might not be complete or perfect, but it fulfills the basic needs.
* [Added some samples (click here to open details on GitHub)](https://github.com/mszcool/NServiceBus.AzureServiceBus-SB1.1-WinSrv/commit/5d12b00cac4471881c4e529be36eaed1567349b3)
    * This contains a basic Sender and Receiver implementation that uses the transport.
    * You need to set the Environment Variable `AzureServiceBus.ConnectionString` in a command prompt and start Visual Studio from that one to successfully execute. Btw. that's also needed if you need to run the tests. In that case you also need to set `AzureServiceBus.ConnectionString.Fallback` with an alternate Service Bus connection string.

Here is the little code-snippet that checks if the code is used for on-premises Service Bus services or for Azure Service Bus instances:

    class CreatesMessagingFactories : ICreateMessagingFactories
    {
        #region mszcool 2016-04-01

        // mszcool - Added Connection String parsing to detect whether a public or private cloud Service Bus is addressed!
        public static readonly string Sample = "Endpoint=sb://[namespace name].servicebus.windows.net;SharedAccessKeyName=[shared access key name];SharedAccessKey=[shared access key]";
        private static readonly string Pattern =
            "^Endpoint=sb://(?<namespaceName>[A-Za-z][A-Za-z0-9-]{4,48}[A-Za-z0-9]).servicebus.windows.net/?;SharedAccessKeyName=(?<sharedAccessPolicyName>[\\w\\W]+);SharedAccessKey=(?<sharedAccessPolicyValue>[\\w\\W]+)$";

        public static readonly string OnPremSample = "Endpoint=[namespace name];StsEndpoint=[sts endpoint address];RuntimePort=[port];ManagementPort=[port];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[shared access key]";
        private static readonly string OnPremPattern =
            "^Endpoint=sb\\://(?<serverName>[A-Za-z][A-Za-z0-9\\-\\.]+)/(?<namespaceName>[A-Za-z][A-Za-z0-9]{4,48}[A-Za-z0-9])/?;" +
            "StsEndPoint=(?<stsEndpoint>https\\://[A-Za-z][A-Za-z0-9\\-\\.]+\\:[0-9]{2,5}/[A-Za-z][A-Za-z0-9]+)/?;" +
            "RuntimePort=[0-9]{2,5};ManagementPort=[0-9]{2,5};" +
            "SharedAccessKeyName=(?<sharedAccessPolicyName>[\\w\\W]+);" +
            "SharedAccessKey=(?<sharedAccessPolicyValue>[\\w\\W]+)$";

        private bool DetectPrivateCloudConnectionString(string connectionString)
        {
            if (Regex.IsMatch(OnPremPattern, connectionString, RegexOptions.IgnoreCase))
                return true;
            else if (Regex.IsMatch(Pattern, connectionString, RegexOptions.IgnoreCase))
                return false;
            else {
                throw new ArgumentException($"Invalid Azure Service Bus connection string configured. " +
                                            $"Valid examples: {Environment.NewLine}" +
                                            $"public cloud: {Pattern} {Environment.NewLine}", 
                                            $"private cloud (SB 1.1): {OnPremPattern}");
            }
        }

        #endregion

        ICreateNamespaceManagers createNamespaceManagers;
        // ... rest of the implementation ...
    }
    
The part where I needed this detection most was to decide, how-to instantiate the MessagingFactory. This is the relevant piece of code - note that `MessagingFactory.Create()` with the NamespaceManager-Address passed in does only work in the public cloud, not with Service Bus 1.1 on Windows Server:

    class CreatesMessagingFactories : ICreateMessagingFactories
    {
        // ... earlier stuff in that class including 'DetectPrivateCloudConnectionString' ...

        ICreateNamespaceManagers createNamespaceManagers;

        public CreatesMessagingFactories(ICreateNamespaceManagers createNamespaceManagers)
        {
            this.createNamespaceManagers = createNamespaceManagers;
        }

        public MessagingFactory Create(Address address)
        {
            var potentialConnectionString = address.Machine;
            var namespaceManager = createNamespaceManagers.Create(potentialConnectionString);

            // mszcool - Updated to detect if Service Bus 1.1 for Windows Server is used
            if (DetectPrivateCloudConnectionString(potentialConnectionString))
            {
                // mszcool - Need to use this approach because different ports for control and transport endpoints are used
                return MessagingFactory.CreateFromConnectionString(potentialConnectionString);
            }
            else {
                var settings = new MessagingFactorySettings
                {
                    TokenProvider = namespaceManager.Settings.TokenProvider,
                    NetMessagingTransportSettings =
                {
                    BatchFlushInterval = TimeSpan.FromSeconds(0.1)
                }
                };
                return MessagingFactory.Create(namespaceManager.Address, settings);
            }
        }
    }
    
Finally with those fixes incorporated, I was able to get almost all things working and all except two tests passing for now. For the proof-of-concept that is successful for now, since it proofs that the partner could achieve what they need to achieve.

Part #4: See it in Action
-------------------------
Now comes the cool part - testing it out and seeing it in Action. The Samples I've added to the git-repository are simple messaging examples which I've modified from the [NServiceBus Samples repository](http://docs.particular.net/samples/non-durable-messaging/) as well. Note that I've taken the Non-Durable MSMQ sample to proof since starting-point for the partner was MSMQ and I wanted something super-simple to start with. That is just how far I got, eventually I try other samples (but no promise at this time:)). Below the code-snippet of the sender - the receiver looks nearly identical and you can look it up on my repository on GitHub:

    static void Main()
    {
        string connStr = System.Environment.GetEnvironmentVariable("AzureServiceBus.ConnectionString");

        Console.Title = "Samples.MessageDurability.Sender";
        #region non-transactional
        BusConfiguration busConfiguration = new BusConfiguration();
        busConfiguration.Transactions()
            .Disable();
        #endregion
        busConfiguration.EndpointName("Samples.MessageDurability.Sender");
        busConfiguration.ScaleOut().UseSingleBrokerQueue();
        busConfiguration.UseTransport<AzureServiceBusTransport>()
            .ConnectionString(connStr);
        busConfiguration.UseSerialization<JsonSerializer>();
        busConfiguration.EnableInstallers();
        busConfiguration.UsePersistence<InMemoryPersistence>();

        using (IBus bus = Bus.Create(busConfiguration).Start())
        {
            bus.Send("Samples.MessageDurability.Receiver", new MyMessage());
            Console.WriteLine("Press any key to exit");
            Console.ReadKey();
        }
    }

Here's the code actually in action and working. You see what it produced on my on-premises Service Bus as well as the log output from the console windows. Note that the message handler part of the receiver outputs that it received a message. 

![RoyalTsAzureOnPremSetupTestSuccessful](/images/posts2016/20160401-figure03.png)

One thing I played around with was having two receivers, that's why you see two output-lines for one single message in my receiver. To clarify, here's the code of MyHandler.cs from the Receiver project which outputs those lines:

    public class MyHandler : IHandleMessages<MyMessage>
    {
        static ILog logger = LogManager.GetLogger<MyHandler>();
    
        public void Handle(MyMessage message)
        {
            logger.Info("Hello from MyHandler");
        }
    }
    
    public class MyHandler2 : IHandleMessages<MyMessage>
    {
        static ILog logger = LogManager.GetLogger<MyHandler2>();
    
        public void Handle(MyMessage message)
        {
            logger.Info("Hello from MyHandler2!");
        }
    }

Final Words
-----------
I think this Proof-Of-Concept we've built alongside with other aspects we covered for that Global Software Vendor partner in UK demonstrates several aspects:
* That it is possible to have a solution that works (nearly) seamless on-premises and in the public cloud with largely the same code base.
   * The situation should DRAMATICALLY improve once Microsoft has released the [Azure Stack](https://azure.microsoft.com/en-us/overview/azure-stack/), which is the successor of what I've used here (which was the [Azure Pack](https://www.microsoft.com/en-us/server-cloud/products/windows-azure-pack/)).
   * We can expect that the [Azure Stack](https://azure.microsoft.com/en-us/overview/azure-stack/) will deliver a much more up2date and consistent experience with Azure in the public cloud once it is fully available incl. Service Bus.
* That with [NServiceBus](<http://docs.particular.net/nservicebus/azure/azure-servicebus-transport>) one of the most important 3rd-party middle-ware frameworks plays very well together with Azure and that it also can be used for Service Bus On-Premises with some caveats (like back-porting the transport-library).
    * An alternative, which I also tried to demonstrate with the simple sample, would be to use the MSMQ NServiceBus transport on-premises and use the AzureServiceBus Transport from NServiceBus for public cloud deployments. As long as only features supported on both sides are used, that might be the preferred way since with that you can fully rely in code delivered for you by NServiceBus without any changes.
    
Note that my attempts are meant to be a Proof-of-Concept, only. You can look at them, try them and even apply them for your solutions fully at your own risk:)

I think it was a great experience working with the team in UK on this part of a larger Proof-of-Concept (which also included e.g. [Azure Service Fabric](https://azure.microsoft.com/en-us/documentation/services/service-fabric/) for software that needs to be portable between on-premises and the public cloud but wants to make use of a true Platform-as-a-Service foundation).

I hope you enjoyed reading this and found it interesting and useful.
