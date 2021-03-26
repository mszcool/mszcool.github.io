---
layout: post
title:  "GeRes2 - An OSS Framework for Reliable Execution of Background Jobs on Azure"
date:   2014-06-02 12:00:00 +0100
categories: wordpressarchive
tags: Archive .NET ASP.NET Azure Batch Queue Scale Parallel
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

A few weeks ago, we published an open-source framework that covers a scenario required by many partners I’ve been working with on Azure over the course of the past 4 years: asynchronous execution of jobs on compute instances in the background – or simple batch processing. Finally, now I found the time (on the plane) to blog a bit about it and point you to that framework.

<!--more-->

Being one of the product owners of this OSS framework I am very proud of what we achieved. Note that there might be something way more sophisticated and complete coming as a platform Service in Microsoft Azure at some point in time in the future. Until that time, our OSS Framework fills this requirement of “batch-processing” in a more simplified way.

### Generic Resource Scheduler v2.0 (GeRes2) for Microsoft Azure

In a nutshell, GeRes2 is a framework for scalable, reliable and asynchronous execution of a large number of jobs on many Worker Machines in Windows Azure. It is built with Azure Cloud Services, provides a Web API for submitting jobs and querying data about jobs and executes Jobs in parallel across multiple worker roles. 

As such, GeRes2 implements a ready-to-use framework which you can use in your own solutions. It is all available as open source and you can use the code or the deployment packages to get GeRes2 running in your own subscriptions.

[http://geres2.codeplex.com](https://github.com/mszcool/codeplex-archive/tree/master/geres2)

GeRes2 essentially implements an all-so well-known pattern from the Azure-world as shown in the sketch below:

![sketch](/images/posts2014/20140602 - GeRes2 - 01.png)

Classic **scenarios** partners are implementing through a pattern like this are:

* Generating documents in the background (e.g. massive amount of documents)
* Image or Video post processing (although for Video you should consider WAMS)
* Complex, distributed calculations
* Asynchronous provisioning jobs (e.g. when provisioning new tenants for your system)
* anything that needs to run multiple jobs in parallel reliably and asynchronously…

Of course, this outlines just the generic pattern and it looks fairly simple. But when digging into details there are a whole lot of challenges that need to be solved when implementing such an architecture such as:

* Execution of long-running and short-running jobs across multiple instances
* Status tracking & status-querying of jobs
* Notifications (status & progress) to consumers and clients
* Prioritization of jobs
* Reliability features (e.g. retry logic, dead-letter handling etc.)
* AutoScaling that is aware of which instances are not executing long-running jobs

Exactly those are the features GeRes2 implements for you – so you can take care of the implementation and scheduling of your jobs. For the while GeRes2 fills a gap that might be filled through a native Azure platform service later (as of today no details are available).

![sketch](/images/posts2014/20140602 - GeRes2 - 02.png)

**Side Note:** We built GeRes2 because we do have partners that needed the functionality outlined below right now. Note that some for you obvious features might be missing since partners we’ve worked with did not immediately require those features, today.

**Side Note:** if you have HPC-background than you might think about a separation of jobs and tasks (jobs as compositions of more fine-granular tasks). In GeRes2 we don’t make that separation, so for GeRes2 Jobs are equal to Tasks.

### Backend Work: Implementing a Job for GeRes2

Creating jobs is fairly easy (whereas we focused on .NET developers, first): You need to create a library that implements an Interface which contains the logic of your job. The interface is part of the server-SDK from GeRes2 that defines the IJobImplementation-interface.

``` csharp
public class FinancialYearEnd : IJobImplementation
{
    // make the property volatile as it may be set by more than one thread
    private volatile bool _cancellationToken = false;


    // default constructor (required by MEF)
    public FinancialYearEnd()
    {
    }

    public JobProcessResult DoWork(Job job,
        string jobPackagePath, string jobWorkingPath, 
        Action<string> progressCallback)
    {
        // Your Job-logic goes here
    }

    public string JobType
    {
        get { return "FinancialYearEnd"; }
    }


    public void CancelProcessCallback()
    {
        _cancellationToken = true;
    }
}
```

Every job gets executed on a single Worker Role instance reserved by GeRes2 for job-execution. The number of jobs executed in parallel is defined by the number of workers you reserve for the job execution. On each worker. every job gets his own, isolated directory where the job files (executables, anything required by a job) are temporarily stored for execution as well as a temporary folder available for local processing.

Job files are deployed automatically by GeRes2 from Azure Blob storage to the worker VMs on job execution. So all you need to do is implement your job, package it in a ZIP-archive and push it into a container called “jobprocessorfiles” in Blob Storage (in a sub-folder matching your TenantName). For more details look at the documentation Wiki from the GeRes2 OSS project workspace.

### Front-End Work: Submission of Jobs for Execution and Notifications

When GeRes2 is deployed in your subscription it provides a WebAPI secured by Azure Active Directory. More details are also available on the documentation wiki from GeRes2. We also provide a client/consumer-SDK for applications and services that want to use GeRes2 for submitting jobs.

With that SDK the submission of a Job is as simple as shown in the following lines of code:

``` csharp
var geresServiceClient = new GeresAzureAdServiceClient
                            (
                                baseUrl,
                                azureAdTenant,
                                azureAdWebApiId
                            );

geresServiceClient.Authenticate(clientId, clientSecret).Wait();

var newJob = new Job
{
    JobType = "FinancialYearEnd",
    JobName = "Job Display Name",
    TenantName = "YourTenant",
    JobProcessorPackageName = "yourjobpackage.zip",
    Parameters = "any parameters you need to pass in"
};

string jobId = geresServiceClient.Management.SubmitJob(newJob, batchId).Result;
```

This leads to the execution of a job in the GeRes2 system on a worker. The status of a job can be tracked in two ways:

* Through “near-real-time” notifications via SignalR and/or Service Bus.
* By polling the monitoring API of GeRes2.

Setting up notifications with SignalR can happen through the client-SDK, directly, as shown in the following code-snippet:

```csharp
//
// Setup notification handlers
//
geresServiceClient.Notifications.JobStarted += (sender, args) =>
{
    // ...
};
geresServiceClient.Notifications.JobProgressed += (sender, args) =>
{
    // ...
};
geresServiceClient.Notifications.JobCompleted += (sender, args) =>
{
    // ...
};

//
// Connect to the SignalR Hub
//
geresServiceClient.Notifications.Connect().Wait();

//
// Schedule jobs and subscribe to job notifications
//
string jobId = geresServiceClient.Management.SubmitJob(newJob, batchId).Result;
geresServiceClient.Notifications.SubscribeToJob(jobId);
```

As mentioned, there’s also the option to query the status of jobs manually. The status of jobs is kept in an Azure-table. Of course you can query that table, directly since you are in full control of your GeRes2 deployment. But we’ve created a WebAPI and a corresponding client-API that should make it a bit easier for you as shown below:

``` csharp
var batchesTask = geresServiceClient.Monitoring.GetBatches();

geresServiceClient.Monitoring.PageSize = pageSize;
var jobsList = geresServiceClient.Monitoring.GetJobsFromBatch(batchIdToGetJobs);

var jobDetails = geresServiceClient.Monitoring.GetJobDetails(j.JobId);
```

### Final Remarks

We’ve created GeRes2 based on concrete partner projects we’ve been involved into across the world. They all started implementing the pattern outlined above manually and it always turned out it is a whole lot of work that needs to get done.

GeRes2 implements a simple set of batch-processing features we’ve seen a lot with partners across the world. Of course it is not a full-blown HPC or batch-processing/job-scheduling platform. It covers simple yet often required scenarios. Nevertheless, what I can confirm is that we’ve partners who are using GeRes2 with > 100 job execution instances and tested way above 100 instances (to be precise: with 800 instances; but note that the built-in AutoScaler works up to 100 instances, only, but is not required to be used, either). 

If you’re interested, then go to our project workspace, download and play the source code, dig into our docs and share feedback if you have.

[http://geres2.codeplex.com](https://github.com/mszcool/codeplex-archive/tree/master/geres2)

is where you can look for further details.

Have much fun and thanks for your interest!
