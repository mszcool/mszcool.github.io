---
layout: post
title:  Git - Removing (accidentally added) secrets from the history
date:   2016-03-01 12:00:00 +0100
categories: wordpressarchive
tags: Archive DeveloperStuff
excerpt_separator: <!--more-->
---

***[restored from my Wordpress blog]***

I really do like Git a lot and even for my private projects I use it as the default. But some aspects of it are quite tricky. A well-known practice is, that you should never check-in secrets or things you don't want to share with others into a Git-repository. That is especially interesting with public repositories hosted on e.g. GitHub.

Well, saying you should not and actually not forgetting about it are two different things. Sometimes it just happens. And even if you are careful with secrets, it can also be other stuff you checked in but didn't want to share with others. So it happened to me when I wrote the last blog-post about [automating my developer machine setup](http://blog.mszcool.com/index.php/2016/02/my-developer-machine-setup-automation-script-chocolatey-powershell-published/) and published my [Machine Setup Script](<https://github.com/mszcool/devmachinesetup/>).

<!--more-->

## The secrets in the history on GitHub!?

As explained in [my previous blog-post](http://blog.mszcool.com/index.php/2016/02/my-developer-machine-setup-automation-script-chocolatey-powershell-published/), in the setup automation script I use for re-setting up a fresh developer machine I also do clone a hand of repositories which are of relevance to me and/or to which I contributed some code. The majority of those repositories is public on GitHub. But some of them are from real-world projects with our customers and partners which are hosted in a private VSTS environment we run for our global team. I accidentally published that list of `git clone` commands as well. 

No passwords, no secrets - but the repository names sometimes contained the names of the partners/customers and some of that work is not done or public, yet. So even though these were not secrets, I am not supposed to share them, yet.

Unfortunately, I realized that only after a few check-ins. So the entire history in my public GitHub repository contained those repository-names from an internal VSTS environment which I didn't want to share. Damn... the post is out, the link points to the repository... what to do?

## How-to remove secrets/content from the entire history with Git?

Of course the "easy" way for this specific case would have been to delete the repository and re-create a new one with the fixed file published. That works for cases where the history is not really important and where you have a truly small repository. In other words, it works for samples and the likes. But even I have received a pull-request for that file which I didn't want to loose, either. By all means, deleting and re-creating is not something that should be considered as a solution for this problem.

So, I did a little Internet-search and came across something that can save many Git-Hub repositories from mandatory deletion to remove things from the entire history, I guess:

[The BFG Repo Cleaner](<https://rtyley.github.io/bfg-repo-cleaner/>)

This is an awesome tool if you ran into the problem I've had. Let's say you have published something into a Git-repository across multiple commits and pushes that you want to get rid of from the entire history. All you need to do are the following steps:

1. Download the [BFG Repo Cleaner](<https://rtyley.github.io/bfg-repo-cleaner/>) into a local directory of your choice. 
    1. The app is written in Scala 
    2. It requires a Java-runtime on your machine.
    3. It is distributed as a JAR-package that contains all dependenices.
2. Open up a command prompt and switch to a temporary directory.
    1. I did this in a temp-directory because it requires a new `git clone --mirror` of your repository which is a 1:1 mirror of the remote repository.
    2. After that you need to push that mirror back to the remote repository again. And then you can delete the mirror and return to your ordinary repository clone.
3. Perform a clone of your repository with the option `--mirror` (I am using my devmachinesetup-repo here since I had to do it with this one, so just replace **devmachinesetup** with any of your repository-names in the commands below).
    1. `git clone --mirror https://github.com/mszcool/devmachinesetup.git`
    2. This clones a mirror of your remote repository with the entire history into a sub-folder of the current folder called `devmachinesetup.git`. 
4. Stay in the folder that contains the `devmachinesetup.git` folder with the mirrored repository in it.
5. Create a text file that contains the text you want to purge from the history of all files in your git repository. 
    1. Each line contains a string (incl. spaces, special characters etc.) that you want to remove. In my case these strings were the complete `git clone <<repositoryname>>` commands which I wanted to remove from the history of commits of the script in the repository. Each line in this text-file contained one of those entire commands.
    2. BFG searches every file in your git-mirror folder and replaces each instance of each lines from the text-file with the text `*** REMOVED ***` in the target files of the repository.
    3. A little sample excerpt for how the content of that text file shows, how simple it is - in my case it was just one git-clone per line which I wanted to remove from the history:
    
        `git clone https://xyz.visualstudio.com/_DefaultCollection/first.git`     
        `git clone https://xyz.visualstudio.com/_DefaultCollection/second.git`  
        `git clone https://xyz.visualstudio.com/_DefaultCollection/third%20complex%20name.git thirdrepo`          

6. Execute the BFG command. Note that BFG is based on the Java-runtime, so either add the folder with BFG JAR-package to your CLASSPATH environment variable or specify the full path to the JAR-package when executing Java. This looks similar to: 
    1. `java -jar C:\Temp\bfg-1.12.8.jar --replace-text myunwantedtext.txt  devmachinesetup.git`
    2. Note that when you download the BFG JAR package, the version in the name of the .jar-file might be different. 
    3. file **myunwantedtext.txt** contained the full 
7. Now BFG has replaced the unwanted content in the local clone. Last-but-not-least you need to push that one back to the remote repository.
    1. Again, in your command prompt window remain in the directory which contains the `devmachinesetup.git` sub-directory with your git-mirror.
    2. Execute `git push` to push the mirror back.
    
That's it, you're done. After I executed the steps above on my repository, I checked online with several commits if it worked. In your case, the result should now look similar to what I've achieved once you've completed the steps above:

![Results of Removing unwanted content](https://txfzva.dm2303.livefilestore.com/y3pwjYBEhhqiChyKL65x0Lxtz6uWAYYezMAYtEOSXRrizZd3-NtBXQ7zHCMdUWKGoT9cD5ts4KmWCiUbDCHOTCnvAeBHXOrskdViZCdSE2NCXTat1yLG_flOTUhb67ctxlLTOJKFwEXClYSWypmFFPLQGa0OsHMbCYSeUeBAYpyDzE/20160301-Git-Removing-Secrets-Figure-01.png?psid=1)

## Final words

Removing unwanted content from the entire history of a Git-repository is needed sometimes. Whether it's about accidental commits of secrets or other (sensitive) content or e.g. large files you want to clean up from your repository. 

The [BFG Repo Cleaner](<https://rtyley.github.io/bfg-repo-cleaner/>) is a handy tool for such cases. It can indeed be used for cases such as the one I described. But it also contains options for other cases such as removing large files from the history of your repository which are not needed there, anymore.

BFG is cool and handy, but if you need more advanced scenarios, you might need to fall-back to the way more powerful, yet much more complex `git-filter-branch` tool ([here](http://git-scm.com/docs/git-filter-branch)). I guess that for 80% of the cases, BFG might be good enough and given the fact it is super-easy to use I'd first give it a chance before digging through the docs of `git-filter-branch`. Kudos to the folks which built BFG... great job and thank you very much for saving my day (I will donate;))...