---
layout: post
title: .NET Golf tournament in action
date: 2005-10-06 14:58:00
categories: msdnblogarchive
tags: Archive MSDNBlog
---

Wow, [the tournament](http://codefairway.net/de/currenthole.aspx) we launched with our cinema event last friday is already full in action. We have a number of .NET golf players who already solved the problems with amazing code-size (663 bytes)...


And we had some fun with finding a mistake in either the implementations of the gamers or my tester ... and we where both right ... the mistake was in the test-vector of the whole thing which had some lower-case letters although only captials are supposed to be there:))) My implementation fixed that automatically by converting letters always to upper-case letters but others leaved that out for code-optimization reasons... wow, that was hard to find:)


