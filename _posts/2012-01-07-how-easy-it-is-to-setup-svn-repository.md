---
layout: post
title: How easy it is to setup an SVN repository! Or how easy backup and version control
really is ...
date: '2012-01-07T15:54:00.005+01:00'
author: hellogetmyblogback
tags:
- svn version control subversion up ci co revert fragbuilder commit update revert
modified\_time: '2012-01-07T18:21:47.330+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-122804718876442640
blogger\_orig\_url: https://combichem.blogspot.com/2012/01/how-easy-it-is-to-setup-svn-repository.html
---

This post serves to spread the use of version control and easy backup in science and everyday life. SVN (an abbreviation of *Subversion*) is in my opinion the simplest method. Mostly because it is extremely easy to use and setup.[![](http://upload.wikimedia.org/wikipedia/en/7/79/Subversion.png)](http://upload.wikimedia.org/wikipedia/en/7/79/Subversion.png)

All you need is a server with SSH access and SVN installed for the most basic way to set up and use SVN. Most research clusters will have this. Our research cluster (cleverly named *sunray*) already has both, and I will use it to demonstrate how I set up an SVN repository for my latest project, which is an API for building peptide fragments and set up NMR calculations and subsequent data analysis.

On 90% of newer Linux distributions one of the two following commands should install everything required for SVN. This is needed on both the server and all clients.
```
andersx@computer:~# yum install subversion # as root
andersx@computer:~$ sudo apt-get install subversion

```
Now, let set up the repository on the remote server, from which we can access the repository from. First, create a directory where you want your repository to live. I use a folder called **~/repositories** to keep all my repositories in as sub-directories. My project in this example is called **fragbuilder**:
```
andersx@sunray:~$ mkdir /home/andersx/repositories/fragbuilder
andersx@sunray:~$ svnadmin create /home/andersx/repositories/fragbuilder

```
The command **"svnadmin create"** sets up the repository. This is all you ever need to do on the server. Now you can log out and forget about it.

Now hop on to the client computer. My laptop **"awesome"** is used in this example. First, we need to get a copy of the newly created repository. This is called "checking out" and abbreviated "**co**":
```
andersx@awesome:~$ svn co svn+ssh://sunray/home/andersx/repositories/fragbuilder

```


This creates a directory named **fragbuilder** which is under control of SVN. In this folder, put all my project files, I must:
```
andersx@awesome:~$ cd fragbuilder/
andersx@awesome:~/fragbuilder$ cp -r ../torsome/* .
# I.e. just copy your project files into the folder

```


Whenever new files are added to the folder, put them under version control by SVN by adding them to the repository:
```
andersx@awesome:~/fragbuilder$ svn add *

```


When you're done with tinkering with your project files and want to send them back to the server use (this is called *"to commit"* abbreviated **"ci"**) use the following command. A good habit is to add a comment, so you can remember what was changed before your last *commit*. This is done with the **-m "comment"** option:
```
andersx@awesome:~/fragbuilder$ svn ci -m "Created SVN repository"

```


Next time you want to update your working copy to the latest version from the server, simply *update* (**"up"**) in the working directory:
```
andersx@awesome:~/fragbuilder$ svn up

```


In a perfect world, I *commit* when I leave my work place, so if my laptop gets stolen, I still have a backup, and I can *update* what I did at work when I get home.

The last useful command  I want to show you today is how to update to a previous version. Let's say you want to *revert* to revision 233 of your project:
```
andersx@awesome:~/fragbuilder$ svn update -r 233

```


This will revert your working directory to how things were under revision 233.

For the most part, all you need to ever type is **svn ci -m "comment"** or **svn up**. This is how easy backup and version control is and should be!





