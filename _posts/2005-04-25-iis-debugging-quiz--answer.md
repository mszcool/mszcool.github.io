---
layout: post
title: IIS Debugging Quiz - Answer
date: 2005-04-25 21:22:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Okay, it's time to enlighten things about my IIS debugging experience. Jeff was quite near at the solution when thinking about some issues with the worker process. Actually the day before I did this demonstration I played around a little bit with the process model of IIS 6.0. You probably know that IIS 6 supports web gardens. Web gardens actually enable you configuring an application pool so that it starts more than one process. These processes share the work load for applications configured in the application pool. This is especially cool if you have more CPUs installed on your web server box as every process can be configured to run on a separate CPU for a better workload-sharing (the CPU mask can be configured through editing the metabase directly, only – the property is called SMPProcessorAffinityMask which is a part of the IIsApplicationPool configuration and therefore can be found at /LM/W3SVC/AppPools/YourAppPoolName).


Okay, so what’s with debugging? Unfortunately after I have played with web gardens I have forgotten to reset the settings for my application pool. And with that IIS starts as much processes as configured for the web garden setting in the IIS management console. Therefore when I started debugging with VS, it attached to the first available process of the application pool for debugging. But then IIS started some additional processes (I have configured the web garden setting for 3 processes; therefore 3 processes were running for my application pool). As VS attaches only to one process and IIS decides which process gets the request based on the work load, sometimes the first process, sometimes the second and sometimes the third worker-process processed my request. Therefore every time when the first process was hit, my breakpoint was hit as well (as VS attached to the first process).


Any other time of course my break point wasn’t hit because VS just don’t attach automatically to the other process (I mean it cannot do that because it gets even better: the additional processes might be launched even after you launch your debugging session as IIS decides when to launch them based on the work load).


This might just be one hint for you if you experience the same problem;-)...


