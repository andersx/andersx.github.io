---
layout: post
title: 'FragBuilder: Installation'
date: '2013-12-27T14:29:00.000+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-12-27T14:29:00.840+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-7247575803958068734
blogger\_orig\_url: https://combichem.blogspot.com/2013/12/fragbuilder-installation.html
---

With the FragBuilder library out as a [preprint from PeerJ](https://peerj.com/preprints/169v2/) and the library available at <https://github.com/jensengroup/fragbuilder>, I am going to make a couple of blog posts to get you started using FragBuilder. You can access the manual via the GitHub page linked above.



This post will guide you through the installation of the FragBuilder library.





|  |
| **Figure 1:** Example of peptide model made using the Fragbuilder library |








FragBuilder will run about every Linux distribution with a recent Python 2.x interpreter.



You need to havethree things installed before FragBuilder can run. Install of these can be found in the 2nd half of this post.

1. NumPy
2. Open Babel with Python bindings
3. git (in order to get FragBuilder from GitHub)


To actually download FragBuilder you need to use git, as FragBuilder is stored and maintained through GitHub. To clone the latest version do this:


>
> $ git clone git://github.com/jensengroup/fragbuilder


This will download (clone) FragBuilder to a folder named "fragbuilder". There is nothing that needs to be compiled, so in principle, FragBuilder is now installed and works.



To actually be able to use FragBuilder from a Python script, you need to export the path to the FragBuilder library to your $PYTHONPATH. For instance, on my laptop, I do the export like this:


>
> \$ export PYTHONPATH=/home/andersx/dev/fragbuilder:\$PYTHONPATH


Now you should be able to import FragBuilder from Python. You can copy this line into your ~/.bashrc file if you want don't want to export this every time you log in.



To test your installation fire up Python and import FragBuilder:


>
> $ python
>
>
>
> Python 2.7.3 (default, Sep 26 2013, 20:03:06)
> [GCC 4.6.3] on linux2
> Type "help", "copyright", "credits" or "license" for more information.
>
> >>> import fragbuilder
> >>> print fragbuilder.\_\_version\_\_
> 1.0
> >>>


If you don't get any errors FragBuilder now works.







# Installation of dependencies:



# 1) NumPy:





In most Linux distributions, NumPy is can be obtained through a package manager. The commands are usually.



For Ubuntu/Debian distributions:


>
> sudo apt-get install python-numpy


For Fedora/Red Hat distributions:


>
> su
>
> yum install python-numpy


See this link, if you still have troubles installing NumPy: <http://www.scipy.org/install.html>





# 2) Open Babel with Python bindings:



In most Linux distributions, Open Babel is can be obtained through a package manager. The commands are usually.



For Ubuntu/Debian distributions:


>
> sudo apt-get install python-openbabel


For Fedora/Red Hat distributions:


>
> su
>
> yum install python-openbabel


Optionally, you can compile and install Open Babel yourself. This may be required if you get a certain problem with setting dihedral angles to 180 degrees.

In older versions of Open Babel you are only allowed to set the dihedral angle to 179.96 degrees. I recently submitted a patch to fix this, but I don't know when the changes will go "live" onto repositories, etc.



I made a short guide here:

<http://combichem.blogspot.dk/2013/12/compiling-open-babel-with-python.html>



There is some extra stuff here from the Open Babel manual:

<http://openbabel.org/docs/dev/Installation/install.html>





# 3) Git:



Git is pretty much the program you use to download FragBuilder from GitHub. You can also use Git to submit patches back to the repository. Git also does a million other things, but you don't need to know more at this point.



In most Linux distributions, git is can be obtained through a package manager. The commands are usually.



For Ubuntu/Debian distributions:


>
> sudo apt-get install git


For Fedora/Red Hat distributions:


>
> su
>
> yum install git




I think that is all. If not, feel free to leave a message!




