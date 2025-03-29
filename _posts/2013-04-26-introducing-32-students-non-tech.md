---
layout: post
title: Introducing 32 students to Linux and Python via VirtualBox
date: '2013-04-26T17:06:00.000+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-04-26T17:39:14.300+02:00'
thumbnail: http://img.youtube.com/vi/wFUZjmnxTnU/0.jpg
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-4290754591782845846
blogger\_orig\_url: https://combichem.blogspot.com/2013/04/introducing-32-students-non-tech.html
---


I am currently teaching the Molecular Statistics course together with [+Jimmy Charnley Kromann](http://plus.google.com/111529974982597129664) and [+Jan Jensen](http://plus.google.com/113593600676820394169) at the University of Copenhagen. My part of the course is teaching the students how to program simple molecular simulation algorithms in Python. [+Jan Jensen](http://plus.google.com/113593600676820394169) does the theoretical lectures, and I give lectures in basic Python programming and how to implement the equations from Jan.




One of the challenges we faced before the start of the course was to just get Python, Numpy and Matplotlib up and running on every single student's laptop. Since the students run everything from Windows XP, 7 and 8 to Linux and Mac, this can pose quite a challenge for the students, some of which have barely ever even installed a program before.




This year, we opted to create a VirtualBox with Ubuntu on it, and make sure everything the need throughout the course was pre-installed. The students could then simply download the VirtualBox program and install our VirtualBox image.




[+Jimmy Charnley Kromann](http://plus.google.com/111529974982597129664) made this excellent video of how to get the VirtualBox going before the first class:










My biggest fear was that the students would be frightened and confused by their first meeting with a non-Microsoft system. But that (fortunately!) didn't turn out to be an issue at all.




These are my observation from the first lecture and first exercises class:


* To my surprise all of the students had managed to bring their laptop with a working VirtualBox to the first class. ALL students! I was pretty thrilled since that meant they all could follow my programming from the projector on their own laptop, which is what I want them to do during my lectures.
* What was going on on the projector was the same as on their laptop during the lecture, which I think help lessen the confusion.
* We didn't have to do ANY tech support during the interactive lectures or the following exercises class to get Python working. Only hitch was two students who wanted American keyboard layout (our Box came with a Danish keyboard layout by default). This was quickly (though some might say awkwardly) solved by "setxkbmap us" in the terminal.
* Some of the groups of students wanted an extra module to make a video of the things they had plotted in Matplotlib. [+Jimmy Charnley Kromann](http://plus.google.com/111529974982597129664) then posted simple instructions and the relevant "sudo apt-get install" command on the course website -- so that went relatively easy as well.
* Things which didn't work very well were the shared clipboard
and drag-and-drop of files between the host OS and the VirtualBox. That
only worked on 50% of the student's laptops. Must be a bug in VirtualBox
somewhere.﻿
* I only overheard the sentence *"Grrr, I hate linux!"* once.






So far so good!







