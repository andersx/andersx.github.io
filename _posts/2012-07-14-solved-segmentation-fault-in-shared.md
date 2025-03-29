---
layout: post
title: "[SOLVED] segmentation fault in shared-mime-types - Ubuntu 10.04 LTS"
date: '2012-07-14T14:35:00.001-07:00'
author: hellogetmyblogback
tags:
- maverick
- ubuntu
- '10.10'
- '10.04'
- fault
- lucid
- libxml2
- '9.10'
- karmic
- LTS
- segmentation
- shared-mime-info
modified\_time: '2012-07-17T04:39:51.652-07:00'
blogger\_id: tag:blogger.com,1999:blog-4501956485444967735.post-8531377252991078947
blogger\_orig\_url: https://centosn00b.blogspot.com/2012/07/solved-segmentation-fault-in-shared.html
---

I just did a kernel + gfx driver upgrade on our Beowulf cluster (Running Ubuntu 10.04 LTS Server), **`hostname`=sunray**.



However, **apt-get upgrade** choked (with nothing less than a segmentation fault) when I tried to update the package **'shared-mine-types'**. Even a **gdb** backtrace wasn't helpful at all.



I could replicate the seg/fault by trying to update the mime database:



# sudo update-mime-database -V /usr/share/mime



The workaround I found was pretty easy (though also ugly). The problem is a conflict with an early version of **libxml2** and **libxml2-dev**. The workaround is to do this (pull never versions from Ubuntu 10.10 (Maverick)):




# sudo -s

# apt-get update

# apt-get purge  shared-mime-info

# cd /root/

# wget https://launchpad.net/~ubuntu-security/+archive/ppa/+build/3103171/+files/libxml2-dev\_2.7.7.dfsg-4ubuntu0.3\_amd64.deb

# wget https://launchpad.net/~ubuntu-security/+archive/ppa/+build/3103171/+files/libxml2\_2.7.7.dfsg-4ubuntu0.3\_amd64.deb

# dpkg -i libxml2-dev\_2.7.7.dfsg-4ubuntu0.3\_amd64.deb

# dpkg -i libxml2\_2.7.7.dfsg-4ubuntu0.3\_amd64.deb

# apt-get install shared-mime-info


Now you should be able to do:



# apt-get upgrade



^\_^







The specs of the Ubuntu is 10.04.4 LTS (Lucid) with a custom compilation of **gdm-2.20** from Ubuntu 9.10 (Karmic).



Happy **apt-get upgrade**'ing!






[![](http://cdn.memegenerator.net/instances/400x/23581484.jpg)](http://cdn.memegenerator.net/instances/400x/23581484.jpg)