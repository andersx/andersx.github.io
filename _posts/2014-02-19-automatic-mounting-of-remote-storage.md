---
layout: post
title: Automatic mounting of remote storage via SSHFS on Amazon EC2 instances
date: '2014-02-19T11:04:00.004+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2014-02-19T11:04:56.776+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-2446382380226116797
blogger\_orig\_url: https://combichem.blogspot.com/2014/02/automatic-mounting-of-remote-storage.html
---

In this blog I demonstrate how you can create an Amazon EC2 instance image that will automount a folder on a remote server via SSHFS.

The purpose here is to fire up a EC2 compute server, run a program and save the output from that program on our local compute cluster at the university.

Basically, you just need to a line to /etc/fstab and save the instance as an image (that's what I did).





What you need:



* An Amazon EC2 instance with sshfs installed.
* A user with SSH keys properly setup to the remote system (the SSH keys cannot require a passphrase).


Your remote server has a folder that is named remote\_folder and your instance has a folder named local\_folder. The default username on Amazon is "Ubuntu" for Ubuntu instances, so I'm using this as an example.



# sshfs#ubuntu@remoteserver:/home/ubuntu/remote\_folder/ /home/ubuntu/local\_folder/  fuse    user,delay\_connect,\_netdev,reconnect,uid=1000,gid=1000,IdentityFile=/home/ubuntu/.ssh/id\_rsa,idmap=user,allow\_other,workaround=rename  0   0



Everything is one long line that goes into /etc/fstab. The IdentityFile points to your SSH key. You need the "\_netdev" keywords to mount the SSHFS folder after network becomes available. The "reconnect" keyword does what it reads, so throw that in as well.

I read a few posts from other people who had difficulties mounting SSHFS properly without the "delay\_connect" and "workaround=rename" keywords, so I added those for good measure.



Note that you need the trailing / after the folder names! I won't work without (and I'm talking from bitter experience here).



Furthermore, you want to add the following line to /etc/ssh/ssh\_config



# ServerAliveInterval 5



This makes SSH send a keep alive signal every 5 seconds so you don't get disconnected due to being idle.



Apart from that I think the above should be self-explanatory (for someone looking for this information).


