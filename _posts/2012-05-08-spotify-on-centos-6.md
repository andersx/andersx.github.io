---
layout: post
title: Spotify on CentOS 6
date: '2012-05-08T11:24:00.000-07:00'
author: hellogetmyblogback
tags:
- centos
- spotify
- alien
- git
- centos 6.2
- centos 6
modified\_time: '2012-05-08T12:20:07.163-07:00'
blogger\_id: tag:blogger.com,1999:blog-4501956485444967735.post-2992683501907224484
blogger\_orig\_url: https://centosn00b.blogspot.com/2012/05/spotify-on-centos-6.html
---

There is no installation for Spotify on CentOS, and there are some whacked dependencies which are cumbersome to get around. I tried this on CentOS 6.2



First install Alien (and git):




# yum install git make

# git clone git://git.kitenet.net/alien

# cd alien/

# perl Makefile.PL; make; make install


Now download spotify .deb files from:



<http://repository.spotify.com/pool/non-free/s/spotify/>





Convert and install the Debian/Ubuntu packages:




# alien --to-rpm spotify-client-qt*.deb

# rpm -Uvh --nodeps spotify-client-qt*.rpm

# alien --to-rpm spotify-client*.deb

# rpm -Uvh --nodeps spotify-client*.rpm


You're almost done! Now download my *magical* package of required libraries:

<http://dl.dropbox.com/u/17435887/CentOS6/andersx/spotify_libs.tar.gz>



Extract these in your home folder. It'll create a directory and a file named (in my case):




# /home/andersx/spotify\_libs
# /home/andersx/spotify.sh



Edit the .sh file so it points to your home directory. In my case:

# export LD\_LIBRARY\_PATH=/home/andersx/spotify\_libs



The libraries in **spotify\_libs** were carefully taken from a Ubuntu 10.04 LTS, and in order to NOT mess up your CentOS, use the supplied script to start Spotify without exporting it to your **$LD\_LIBRARY\_PATH** when you don't need it.



Of course feel free to put the libraries and launcher script somewhere else.



Happy listening!

:wq