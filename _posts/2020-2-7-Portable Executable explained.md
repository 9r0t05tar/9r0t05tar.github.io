---
layout: post
title: "Portable Executable explained"
tags: [reversing]
comments: false
---


People always ask if they need to fully understand PE when coming to windows reverse engineering. 

The answer is "Yes"

Since there are so many things that connects to this topic such as anti debugging and packing, PE is one of the topics that readers<br>
shoud fully understand about before getting into real advanced topics. 

PE stands for portable executable, it means what it says, binaries that are portable so you can run it in a different environment in terms of operating systems.
But even though it means for the binaries to be portable in different environment still this doesn't mean you can run a windows exe file in a linux system(without using wine)<br>
but you can run the exe programs in different WINDOWS environment such windows VISTA, windows xp, windows 7, windows 10<br><br>

I personally studied pe by getting through a lot of analyzing getting knowledges piece by piece and I never did study pe in advanced. <br><br>

It is really never possible to understand PE fully, but this post will give you a bit of an idea on what pe is when the elements inside it to be able to analyze the binary in more advanced.
