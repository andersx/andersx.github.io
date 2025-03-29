---
layout: post
title: minimalistic laptop setup
date: '2012-09-03T12:13:00.003-07:00'
author: Unknown
tags:
- i3
- i3wm
modified\_time: '2012-11-26T07:29:53.773-08:00'
blogger\_id: tag:blogger.com,1999:blog-4501956485444967735.post-7299875783859260109
blogger\_orig\_url: https://centosn00b.blogspot.com/2012/09/minimalistic-laptop-setup.html
---


I recently fell in love with [i3](http://i3wm.org/)wm, a tile window manager, so I wanted to format my computer and have a bare-minimum installation (instead of running gnome in the background). I checked out tile-window-managers after using pywo, and wanted to 'go the extra mile'.



This resulted in me downloading the minimal CD from ubuntu (12.04) and installing X, i3 and a smallest login manager I could find.



I just ordered the ASUS UX31A and so I need to install everything from scratch again, so what I did was to create a github project with bash file installing all the nice small packages from the first time.



<https://github.com/charnley/ubuntu-mts>



Here is an outline of the install script, which basicly uses apt-get (why I choose ubuntu) and installs everything needed.






Afterwards all the setup RC's, icon, font setup is done via setup.sh.



I really like this setup for a fast on-the-go geeky setup, especially on a 13" where room on the screen is limited.
