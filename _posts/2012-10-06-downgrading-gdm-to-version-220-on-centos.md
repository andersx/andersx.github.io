---
layout: post
title: Downgrading GDM to version 2.20 on CentOS
date: '2012-10-06T02:10:00.003-07:00'
author: hellogetmyblogback
tags:
modified\_time: '2012-10-06T02:38:32.371-07:00'
blogger\_id: tag:blogger.com,1999:blog-4501956485444967735.post-888844774414776365
blogger\_orig\_url: https://centosn00b.blogspot.com/2012/10/downgrading-gdm-to-version-220-on-centos.html
---

Sun Ray Server Software requires GDM <= 2.20. Unfortunately contemporary (i.e. up-to-date) Linux distros do not offer outdated GDM. However GDM is surprisingly easy to build from source on CentOS.





# Step 1: Get GDM 2.20 source



wget http://download.gnome.org/sources/gdm/2.20/gdm-2.20.11.tar.bz2

tar xf gdm-2.20.11.tar.bz2

cd gdm-2.20.11





# Step 2: Edit gdm-2.20.11/daemon/gdm-net.c





Change



#define MAX\_CONNECTIONS 15



to




#define MAX\_CONNECTIONS 100



(Prevents GDM from hanging when all your Sun Ray terminals connect)




# Step 3: Install dependencies



su

yum install audit-libs-devel \

libglade2-devel \

libart\_lgpl-devel \

libgnomecanvas \

libgnomecanvas-devel \

librsvg2-devel \

dbus-glib-devel \

popt-devel \

gnome-doc-utils





# Step 4: Configure and make a dirty install



./configure

make

make install







# Step 5: Logout and test



gdm --version

GDM 2.20.11





Leave a comment if the above didn't work. This was done on a CentOS 6.3 64-bit machine.