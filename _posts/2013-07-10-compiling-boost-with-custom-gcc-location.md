---
layout: post
title: Compiling BOOST with custom GCC location
date: '2013-07-10T02:25:00.001-07:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-07-10T02:25:18.835-07:00'
blogger\_id: tag:blogger.com,1999:blog-4501956485444967735.post-3923247118804156254
blogger\_orig\_url: https://centosn00b.blogspot.com/2013/07/compiling-boost-with-custom-gcc-location.html
---

Compiling BOOST on our shared cluster running CentOS 5 is always a pain in the neck. The default GCC version is 4.1, so compiling programs with just the default GCC is not always the best choice.

I was trying to compile a program that depends on BOOST, and figuring out how to specify non-default compilers to BOOST took me a while to figure out.



Download your version of BOOST and untar:




```
wget http://sourceforge.net/projects/boost/files/boost/1.52.0/boost\_1\_52\_0.tar.bz2
tar xjf boost\_1\_52\_0.tar.bz2

```





Find the user-config.jam file (this is where the magic happens) and edit:




```
cd boost\_1\_52\_0
vim tools/build/v2/user-config.jam

```





Add the line corresponding to your C++ compiler (In my case G++ 4.4). The user-config.jam file loosk something like:




```
# ------------------
# GCC configuration.
# ------------------

# Configure gcc (default version).
# using gcc ;
# Configure specific gcc version, giving alternative name to use.
# using gcc : 3.2 : g++-3.2 ;
using gcc : 4.4 : /usr/bin/g++44 ;

```



The syntax is:




```
using <toolset name> : <version> : <executable> ;


```



See more info here: <http://www.boost.org/boost-build2/doc/html/bbv2/reference/tools.html>



Now bootstrap BOOST as you normally would:




```
./bootstrap.sh --prefix=/opt/boost\_1\_52\_0\_gcc44

```



And compile! Use the -d2 option to up the debug level of b2, so you can see the actual compile command (and check that b2 is using the correct compiler):




```
./b2 -d 2
./b2 install

```



Tell your mother of that awesome blog post you just read.