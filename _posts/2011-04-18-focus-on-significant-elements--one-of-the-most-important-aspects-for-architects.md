---
layout: post
title: Focus on significant elements - One of the most important aspects for architects
date: 2011-04-18 12:07:08
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Reading books such as “[The Process of Software Architecting](http://www.amazon.com/Process-Software-Architecting-Peter-Eeles/dp/0321357485/ref=sr_1_1?ie=UTF8&s=books&qid=1303149429&sr=8-1)” you will see statements such as “architecture focus on significant elements” a lot. Given my recent experience I can’t tell you more how important this is! Therefore I thought it’s worth posting a bit from my recent experience…

 Of course, as architects we cannot focus on every tiny little detail – so we have to decide for ourselves, how deep we want to go in terms of influencing solutions for solving problems. Depending on the level of architecture (enterprise, solution/application, technical) you’re dealing with that might be very different. Also the goals you’re trying to achieve will influence what is significant for you in your specific project and/or organization, of course.

 Generally speaking, some examples that are “significant” for me and where I try to be involved as an architect are the following ones (please note, these are examples from my recent experience and by no means a full list of things):

 * **Significant elements of requirements.**
* *Do we have a broad picture on the business-requirements at a very high (not detailed!!) level?*   
On the one-hand-side I want to have a broad picture of all the requirements, on the other hand I know that going into details for all of them is too much and is not worth it. But having a broad picture (such as a business capability map) helps me quickly understanding what’s going on in the business, identifying potential relationships and help with driving decisions such as road-map / planning appropriately.
* *Are the requirements prioritized?*   
It can’t be that all requirements are equally important. Some are always more important to an organization than others. And those are the ones I want to look first.
* *Are non-functional requirements captured appropriately (measurable!!)?*   
Non-functional requirements that are not measurable are worth nothing. E.g. stating that a system should be scalable and available is nice. But what does it mean in numbers  now and in the mid-term future? How much do we need to scale. Given that I usually try to find decisions that are also justifiable from a “investment vs. value”-perspective this is really important to me.
* *Do we have a “terminology (glossary)” that is used and understood by everyone?*   
Ineffective and inefficient communication is in my opinion one of the core-sources for project failure or conflicts within projects. Very often inefficient communication comes from a different understanding of the same term. Defining a glossary upfront can safe a lot of pain in the future.

 * **Significant elements in solution architectures.**
* *Relationships between components.*   
These are key, and not just for classic aspects such as maintenance. If e.g. agility is a strategic goal then the way you define relationships between components will impact your flexibility of changing your system which finally will have an impact on agility.
* *Interfaces at the boundaries and between platforms / systems.*   
I cannot deal with all the detailed interfaces inside of a system-implementations. But a specific set of them I definitely will want to deal with. For me any kind of interface that exposes my system to other systems or even other platforms is something that is definitely significant to me. Because these interfaces introduce some level of coupling between the system I want to verify how that coupling is related to any of the strategic, business or non-functional (e.g. security) goals or requirements.

 * **Technical decisions which are significant to me.**   
Finally there are technical decisions I do deal with, as well. Although I do not jump into technical details in many areas such as “how do we implement this WF-activity in detail”, I will get involved in more depth in others and even go to the level of participating in a PoC if it’s really significant. Some questions that I am asking myself to decide whether to participate or not:
* Does the technical decision impact any of the high-risk requirements (functional / non-functional) identified in the requirements-prioritization exercise?
* Does the technical decision potential impact one of the higher-level goals (strategic, mid- to long-term)?
* Does the technical decision involve a major investment that justifies a more detailed analysis and assessment?
* Does the technical decision affect enterprise-level standards and policies which have been established (such as strategic platform-decisions, education / know-how of staff etc.)

 * **Anything that is related to mid-/long-term strategic goals and high-level requirements.**   
In many cases when undergoing a larger IT-initiative or project some high-level visions and strategic goals are one of the reasons for doing so. Typically these are defined at a C-level and are of significant importance. Therefore any decision that is related to strategic goals becomes a significant decision for me. E.g. if an organization has defined “flexibility, agility and responsiveness to change” I try to make sure and be involved in the following aspects:
* *Clear definition of those strategic goals!*   
It is important to understand, how far these strategic goals are meant to go and where they are meant to stop. If a CxO defines flexibility as a strategic goal that can mean a whole lot of things. So I want to be involved when these goals are defined, described and before they are shared so that I have the chance to try to nail it down to something concrete. Take flexibility as an example. That can mean many different things and depending on what it means and how far it goes it can mean major investments in some areas or not. E.g. if a CxO states that flexibility means to him that he wants to be able to support new business processes with IT in 3 months after they’ve been introduced and worked out by business instead of 6 months it means that we just need to find more effective ways of coming up with new solutions but it doesn’t mean that I need to build a super-generic solution. That helps me driving my decisions.
* *Evaluate decisions that have to be taken against these strategic goals.*   
I at least want to have a quick look at architectural decisions that need to be taken and asses them against the strategic goals. If they do potentially affect any of the strategic goals I would like to get involved in them, understand their dynamics and provide my input so that I am able to make sure we find an appropriate, good enough solution that is inline with strategic goals or the vision to avoid both, they are not taken into consideration at all or they are “miss-used” to justify a super-smart and super-expensive solution based on these strategic goal that costs too much related to the value that is provided and needed.

 All of the above are just examples I had to deal with in the past year since I am in my new role, and of course they can differ with every project and organization. All of this helps me both, to be an effective architect (hopefully![Smile](https://github.com/mszcool/oldmsdnblogarchive/blob/master/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/32/26/metablogapi/3223.wlEmoticon-smile_451565DE.png?raw=true)) and to save my energy for the really important things (instead of wasting it for discussions that are just not worth it). Also I know it is hard to find out, if something is really significant… but I think that’s one piece of our job that we HAVE TO do… and it makes it really interesting, by the way.


