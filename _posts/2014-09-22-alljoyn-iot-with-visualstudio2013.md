---
layout: post
title:  AllJoyn IoT Peer-To-Peer Protocol & Framework – Making it Work with Visual Studio 2013
date:   2014-09-22 12:00:00 +0100
categories: wordpressarchive
tags: Archive C++ IoT VisualStudio
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

Our global team is running several industry subject matter working groups with different focus areas. One of them is targeted to Internet-Of-Things (IoT).

The charter of this working group is the exploration of IoT standards, protocols and frameworks across various industries with the goal of developing recommendations, reference architectures and integration points to and on the Microsoft platform as well as feeding IoT-related product groups of Microsoft with input for technologies, services and features we’re building as a company.

Together with peers from this working group including @joshholmes, @tomconte, @mszcool, @reichenseer, @jmspring, @timpark, @irjudson, @ankoduizer, @rachelyehe and daniele-colonna we explored AllJoyn as a technology.

<!--more-->

### Background on AllJoyn and our activities

I had the chance to work with this working group on exploring [AllJoyn](http://www.alljoyn.org/) as a technology – especially because Microsoft joined the AllSeen alliance (<www.allseenalliance.org>). AllSeen has the sole target of making AllJoyn a defacto-standard for device-2-device communications in a mash-up oriented way across platforms and operating systems.

In a nutshell, AllJoyn is a peer-2-peer protocol framework that allows all sorts of device-2-device communication in local networks. It is built on-top of DBUS and TCP/UDP and includes advanced services such as on-boarding of devices to a network, device/service discovery, communication and notifications.

If you want to learn more about the fundamentals, [Rachel has published a great summary](http://azure.microsoft.co.il/?p=164) on AllJoyn on her blog, which explains the capabilities and services provided by AllJoyn and the architectural principles, in general!

### Why is this relevant?

Well, I think AllJoyn does have the potential revolutionize, how devices find and talk to each other in a standardized, cross-platform way in local networks and across wide networks (through a gateway). If done right and assuming, that a broad ecosystem of devices will adopt AllJoyn, this can lead to seamless detection and usage of nearby devices through other (smart) devices such as phones.

Think about the following scenario: you enter a hotel room as part of a business trip and your phone, regardless of which platform it is using, detects the TV, the coffee machine and the wake up radio in your room and you can “configure” and “use” those devices right away through your phone without the need of other remote controls or getting up from the bed to start brewing your coffee. Also media sharing could become much easier than it is today across devices from different vendors using different operating systems.

The potential is huge, the ecosystem nevertheless needs to be developed. And since Microsoft joined the alliance around this protocol and services framework I think we could and want to drive some of this development actively. Let’s see what the future brings based on our early efforts here right now;)

### Setup of an AllJoyn Development Environment with Visual Studio 2013 on Windows

This blog is solely focused on what you need to do to get the AllJoyn SDK working with Visual Studio 2013. This sounds like a simple thing, but so far the AllJoyn SDK is available for VS 2012, only. Since AllJoyn is using a specific combination of build tools from the OSS-world, tuning the setup to work with VS 2013 requires a few further steps which I’ll dig into as part of this blog-post.

Once you have completed your setup you can start developing AllJoyn enabled services for a variety of devices on Windows machines with Visual Studio 2013 including Windows Services, Desktop Applications and backend-services (e.g. ASP.NET) making use of AllJoyn as a technology and protocol framework.

To setup a full development environment that works with Visual Studio 2013 (and 2012 in parallel), follow these steps below. You need to install exactly the versions of the tools provided below as opposed to the official docs from the AllJoyn home page since these versions of the dependent tools also work with Visual Studio 2013.

1. Download and extract the [AllJoyn Source Code Suite](https://allseenalliance.org/source-code).
   1. Downloading the Windows SDK will give you libraries compiled with VS2012. You definitely will run into issues with using them on VS2013 since there were some changes relevant for AllJoyn in the VS2013 C++ compiler.
   2. In my tests I did use the version 14.06 from the SDK.
   3. For details on how-to work with the Thin SDK, look at Thomas’ blog post who writes about how-to get the Thin SDK to compile with VS 2013 and use it with Intel Galileo Boards running Windows.
   4. Note: for extracting the SDK, I suggest installing a ZIP-tool such as 7-zip which is capable of dealing with *.tar and *.gz archive formats.
2. Download & install [Python 2.7.8](https://www.python.org/downloads/).
3. Install [SCONS 2.3.3](http://www.scons.org/download.php) (use the [Windows Installer Download](http://prdownloads.sourceforge.net/scons/scons-2.3.3-setup.exe)) or higher (don’t use earlier versions of SCONS).
4. Install the following tools in addition to Python and SCONS. These are optional, but I’ve installed all of them to make sure not running into other distracting issues:
   1. [DoxyGen 1.8.8](http://sourceforge.net/projects/doxygen/files/rel-1.8.8/)
   2. Basic [MikTex 2.9.5105](http://miktex.org/download/)
   3. [GraphViz 2.3.8](http://www.graphviz.org/Download_windows.php)
   4. [Uncrustify 0.57](http://sourceforge.net/projects/uncrustify/files/uncrustify/uncrustify-0.57/) for Win32
5. Make sure to have Python as well as SCONs and the other tools in your PATH environment variable.
6. Fine-tune some of the source files for the AllJoyn SDK before compilation due to changes made from VS2012 to VS2013 in the C++ Libraries and Compiler.
7. Compile the AllJoyn SDK using SCONS.
8. Create your VS2013 C++ project to test your compiled AllJoyn Library.

For more details on how-to setup and verify the development environment, in general, also look at [Rachel’s blog](http://azure.microsoft.co.il/). She will create a post to explain, how-to install the tools above, make sure you have the environment variables setup correctly and verify if the tools are all available in your PATH environment variable. This post nevertheless will just explain, how-to setup for VS2012 based on the learnings with the official current releases from AllJoyn available at the time of writing this article.

### Update the AllJoyn Source to work with VS2013

As mentioned above, Microsoft made some changes in the Visual Studio 2013 compiler (making it more compliant to certain C/C++ standards and defacto-standards). This resulted in a refactoring of some of the standard template and base libraries the AllJoyn SDK makes use of. Also some workarounds AllJoyn used for its cross-platform build with SCONS are not needed in VS2013, anymore. So we need to get rid of those.

Fortunately, the changes you have to make are not that many (although they were a bit more challenging to be tracked down.

1. For the following steps, replace ```alljoynsdkroot``` with the root folder in which you have installed the AllJoyn SDK. To ensure we’re talking about the same directory structure, this is what I assume the AllJoyn root directory looks like:
   ![AllJoynSDKFolder](/images/posts2014/20140922 - AllJoyn-0001.png)
`
2. Helpful background info: the core SDK for AllJoyn is built in C++. For other platforms including C, Java, Node etc. the AllJoyn group has built language bindings which are all based on the core C++ libraries. Therefore some headers are available multiple times in the source code control structure for the different language bindings.

3. The first file we need to touch is one of the platform mapping headers AllJoyn is using the the core SDK. These header files are used to provide some macros that cover differences/workarounds for core functionality of C/C++ compilers of different platforms.
   1. Open the file <alljoynsdkroot>\alljoyn-suite-14.06.00-src\core\alljoyn\common\inc\qcc\windows\mapping.h
   2. Add the following pre-compiler macro at the beginning of the source file:
      ![AllJoynMappingHeader](/images/posts2014/20140922 - AllJoyn-0002.png)
   3. Un-comment the following source files at the end of the source file to avoid duplication.
      ![AllJoynUncommentDefines](/images/posts2014/20140922 - AllJoyn-0003.png)

4. The very same mappings file need to be updated for the C-bindings. For this purpose, conduct exactly the same changes as outlined in step 3 for the mapping.h file in the C language binding folder.
   1. The mapping file to update is called <alljoynsdkroot>\alljoyn-suite-14.06.00-src\core\alljoyn\alljoyn_c\inc\qcc\windows\mapping.h
   2. Perform the same changes as outlined in step 3.

5. The final change that needs to happen is an update in the SCONs build script files for AllJoyn so that it supports the VS2013 option from SCONS 2.3.3 in addition to the existing VS2010 and VS2012 options.
   a. Open the file <alljoynsdkroot>\alljoyn-suite-14.06.00-src\core\alljoyn\build_core\SConscript
   b. Search for the section in the script that defines the MSVC_VERSION enumeration with allowed values. This will support values up to VC 11.0 incl. VC 11.0Exp for the express edition.
   c. Add an additional value “12.0” to this variable definition as shown below (this assumes you have VS2013 Professional or higher – I didn’t test express but assume 12.0Exp would make it all work with the express edition, as well):
      ![AllJoynMsVcVersionDef](/images/posts2014/20140922 - AllJoyn-0004.png)

### Build with Visual Studio 2013

Now that we have made all changes to platform-headers and SCONS scripts, we can build the libraries for Visual Studio 2013. These can then be used in any VS2013 C/C++ project to enable you develop with the latest and greatest (released) development tools from Microsoft.

1. Open up a Visual Studio 2013 command prompt.

2. Change to the directory <alljoynsdkroot>\alljoyn-suite-14.06.00-src\core\alljoyn

3. Execute the following command: `scons BINDINGS="C,C++" MSVC_VERSION="12.0" OS="win7" CPU=x86_64 WS="off"`
   1. Note that you might see some warnings since a few verifications got stricter in VS2013 C/C++.
   2. The command builds the AllJoyn SDK with the following options:
      1. Language bindings for C and C++. For the other bindings I’d suggest to just use the existing SDKs;)
      2. Visual Studio 2013 using the 12.0 version of the Visual C compiler.
      3. Target operating system Windows 7 (which perfectly works on Windows 8, as well – it does not have any impact on the compiler options or Windows SDK references since all used are standard libraries – the SCONS scripts are just using this for validation of other options, e.g. the CPU-options available for the platform of choice).
      4. White space fixing of source files turned off (WS=”off”). To turn this on, make sure that Uncrustify is set in your path appropriately.
   3. Here a screen-shot of how the start of the build should look like:
      ![AllJoynSconsOutput](/images/posts2014/20140922 - AllJoyn-0005.png)

4. Since the build runs a while, wait until it is finished and see if any errors occurred. If not, you find the ready-to-use libraries built with the VS2013 C/C++ compiler under the following folders:
   1. X86: <alljoynsdkroot>\alljoyn-suite-14.06.00-src\core\alljoyn\build\win7\x86_64\debug\dist\cpp
   2. X64: <alljoynsdkroot>\alljoyn-suite-14.06.00-src\core\alljoyn\build\win7\x86_64\debug\dist\c

### Creating a VS2013 Console project for testing the libraries

Finally to verify if things are working, you can create test apps and see if they can join other devices on an AllJoyn bus (refer to [Rachel’s blog for details on what an AllJoyn Bus is](http://azure.microsoft.co.il/?p=164)). In this project you need to refer to the libraries just built.

1. Create a new Visual C++ project. For our testing purposes I’ve created a Console application (Win32/64).

2. Update your build configurations as you need them.
   1. E.g. by default the project template will create a Win32 32-bit application.
   2. If you want to have 64-bit, just add the 64-bit configuration as usual for any other C/C++ project.
   3. Note that the subsequent changes in this list need to happen for both, 32-bit and 64-bit build configurations.

3. In the Project Properties dialog, add the following “VC++ Directories” to your “Include Directories” and “Library Directories” settings so that the compiler finds the dependent header files and compiled libraries. These should now point to the directories used earlier to build AllJoyn with VS2013/VC12.0.
   1. Note: in the screen-shot below I have used a relative path from my solution so that whenever I get the solution to a different machine it will still compile without issues whenever the AllJoyn SDK is put into the same directory on that new machine. I’d suggest doing something similar for your projects, as well, so that re-creating a dev-machine from scratch is predictable and easy-to-do.
   2. Include Directories
      ![AllJoynVsBuildIncludeDirs](/images/posts2014/20140922 - AllJoyn-0006.png)
   3. Library Directories
      ![AllJoynVsBuildIncludeDirs](/images/posts2014/20140922 - AllJoyn-0007.png)

4. If you want to “avoid” IntelliSense complaining that it does not find headers required, also add the “Include Directories” added earlier to the more general “VC++ Directories” option to the option “C/C++ \ General \ Additional Include Directories”. These are exactly the same as those specified in step 3 for “Include Directories”.
   ![AllJoynVsAdditionalIncludeDirs](/images/posts2014/20140922 - AllJoyn-0008.png)

5. Next you need to define a pre-processor that is used by some of the header-files from the language bindings to detect the platform and define different types of Macros and platform types (remember the customization we made earlier to make stuff build on VS2013 – these are some of those). This pre-processor is called QCC_OS_GROUP_WINDOWS as shown below:
   ![AllJoynVsPreProcessor](/images/posts2014/20140922 - AllJoyn-0009.png)

6. Finally you need to tell the VC Linker which libraries to use for the linking process. This ultimately includes some dependencies from AllJoyn itself as well as the built AllJoyn libraries. See below for a list of items to include there:
   ![AllJoynVsAdditionalDependencies](/images/posts2014/20140922 - AllJoyn-0010.png)

With all these steps in place, you can start writing some code for AllJoyn. E.g. you can discover devices and services or register yourself as a service in an AllJoyn Bus network – all done with Visual Studio 2013.

For example the following code attaches itself to an existing bus service in a local network and queries for devices that do offer services with a specific service prefix name on this bus:

```cpp
#include <qcc/platform.h>
 
#include <assert.h>
#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
 
#include <alljoyn_c/DBusStdDefines.h>
#include <alljoyn_c/BusAttachment.h>
#include <alljoyn_c/BusObject.h>
#include <alljoyn_c/MsgArg.h>
#include <alljoyn_c/InterfaceDescription.h>
#include <alljoyn_c/version.h>
#include <alljoyn_c/Status.h>
 
#include <alljoyn_c/BusListener.h>
#include <alljoyn_c/Session.h>
#include <alljoyn_c/PasswordManager.h>
 
#include <qcc/String.h>
#include <qcc/StringUtil.h>
#include <qcc/Debug.h>
 
#include<vector>
#include<signal.h>

int main(int argc, char** argv, char** envArg)
{
    QStatus status = ER_OK;
    char* connectArgs = "null:";
    alljoyn_interfacedescription testIntf = NULL;
    /* Create a bus listener */
    alljoyn_buslistener_callbacks callbacks = {
        &buslistener_registered,
        NULL,
        &found_advertised_name,
        NULL,
        &name_owner_changed,
        NULL,
        NULL,
        NULL
    };
    /* Session Port variables */
    alljoyn_sessionportlistener_callbacks spl_cbs = {
        accept_session_joiner,
        NULL
    };
    alljoyn_sessionopts opts;
 
    printf("AllJoyn Library version: %s\n", alljoyn_getversion());
    printf("AllJoyn Library build info: %s\n", alljoyn_getbuildinfo());
 
    /* Install SIGINT handler */
    signal(SIGINT, SigIntHandler);
 
    /* Create a password */
    alljoyn_passwordmanager_setcredentials("ALLJOYN_PIN_KEYX", "ABCDEFGH");
 
    /* Create message bus and start it */
    g_msgBus = alljoyn_busattachment_create(OBJECT_DAEMON_BUSNAME, QCC_TRUE);
    if (ER_OK == status) {
        status = alljoyn_busattachment_start(g_msgBus);
        if (ER_OK != status) {
            printf("alljoyn_busattachment_start failed\n");
        }
        else {
            printf("alljoyn_busattachment started.\n");
        }
    }
 
    /* Register a bus listener in order to get discovery indications */
    g_busListener = alljoyn_buslistener_create(&callbacks, NULL);
    if (ER_OK == status) {
        alljoyn_busattachment_registerbuslistener(g_msgBus, g_busListener);
        printf("alljoyn_buslistener registered.\n");
    }
 
    /* Connect to the bus */
    if (ER_OK == status) {
        status = alljoyn_busattachment_connect(g_msgBus, connectArgs);
        if (ER_OK != status) {
            printf("alljoyn_busattachment_connect
                                    (\"%s\") failed\n", connectArgs);
        }
        else {
            printf("alljoyn_busattachment connected to 
                             \"%s\"\n", alljoyn_busattachment_getconnectspec(g_msgBus));
        }
    }
 
    /* Create the mutex to avoid multiple parallel executions of foundAdvertisedNames */
    gJoinSessionMutex = CreateMutex(NULL, FALSE, NULL);
    if (gJoinSessionMutex == NULL) {
        printf("Error creating mutex, stopping!");
        return -1000;
    }
 
    /* Find the Led controller advertised name */
    status = alljoyn_busattachment_findadvertisedname(g_msgBus, OBJECT_NAME);
    if (ER_OK == status) {
        printf("ok alljoyn_busattachment_findadvertisedname %s!\n", OBJECT_NAME);
    }
    else {
        printf("failed alljoyn_busattachment_findadvertisedname 
                         %s!\n", OBJECT_NAME);
    }

    /* Get the number of expected services
       In our test-setup we expect 2 services (arduino and galileo) */
    int nExpectedServices = 2;
 
    /* Wait for join session to complete */
    while ((g_interrupt == QCC_FALSE) && (i_joinedSessions < nExpectedServices)) {
        Sleep(10);
    }

    /* 
           Devices found, do whatever needs to be done now 
           ...
    */
}
```

The code above is using a call-back that is called by the AllJoyn core libraries whenever a device is found. More specific it looks for two devices we expected to be available in our “lab-environment” for testing purposes. One was an Arduino and the other one an Intel Galileo with a LED connected. Both where using the AllJoyn Thin Client Library to connect to the bus.

The only callback relevant was the following one since it is called by AllJoyn libraries when a new device was found that was connected to the same AllJoyn Bus (we’ve used the other callbacks for other tests):

```cpp
void found_advertised_name(const void* context, const char* name, 
                           alljoyn_transportmask transport, 
                           const char* namePrefix)
{
    printf("\nfound_advertised_name(name=%s, prefix=%s)\n", name, namePrefix);
 
    DWORD dwWaitResult = WaitForSingleObject(gJoinSessionMutex, INFINITE);
 
    s_sessionNames[i_joinedSessions] = (char*)malloc(sizeof(char)*1024);
 
    strcpy_s(s_sessionNames[i_joinedSessions], 1024, name);
 
    i_joinedSessions++;
    
    /* enable concurrent callbacks so joinsession can be called */
    alljoyn_busattachment_enableconcurrentcallbacks(g_msgBus);
 
    ReleaseMutex(gJoinSessionMutex);
 
    printf("found advertisments %d\n", i_joinedSessions);
}
```

Note that the code above is just meant to give you some sort of impression. For full end-2-end scenarios wait for further blog-posts from us as well as look at the official AllJoyn documentation.

### Final thoughts

Okay, the journey above is a lot of effort, isn’t it? Well, at this point it needs to be said that AllJoyn is still in a very early stage, and therefore some development steps are still a bit hard to get done. Especially setting up an environment that works end-2-end with the tools-chain of your choice (VS2013 in my case at the time of writing this post).

But, I am excited that I am involved in this journey. I see various things happening in the industry that are all geared towards some sort of device mash-up. Think of what Microsoft tried to start a few years ago with Live Mesh. Think of what Apple is doing with their seamless device-2-device interaction which really works great. And consider what Android is doing attempting to do with Android “L”. All of these efforts are really cool and enable great scenarios – but they’re all locked down to one vendor and their specific operating system.

When thinking beyond the ecosystems (Microsoft, Apple, Google) I mentioned above and involving all sorts of devices of our daily live such as (Smart) TVs, Coffee machines, HiFi stereo systems, cars and car information systems, anything related to home automation or even industrial facilities leveraging device mash-ups to solve more complex problems, there MUST be something that AVOIDS vendor lock-in.

AllJoyn can become “this something”. It has the potential. The technology is cool. All it needs now is some sort of dramatic simplification as well as an ecosystem of service interfaces and devices supporting those for all sorts of scenarios across different industries.

We’ll see and experience, how this will work out and where we will end up with this. Microsoft definitely has a huge interest in participating in this journey and you’ll definitely see more around the Microsoft platform and AllJoyn over the course of the upcoming months.

Also look at the Twitter and Blog accounts of my peers since we’re already planning a subsequent hackathon around this topic together with some product groups to dig even deeper into the world of peer-2-peer device mash-ups based on AllJoyn... so that said, expect more to come!!!