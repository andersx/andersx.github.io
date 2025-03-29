---
layout: post
title: Installing OpenBabel and compiling with the C++ API
date: '2010-11-12T23:20:00.016+01:00'
author: hellogetmyblogback
tags:
- C++
- OpenBabel
- compiling
modified\_time: '2010-11-16T11:20:47.452+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-1249888120022042306
blogger\_orig\_url: https://combichem.blogspot.com/2010/11/installing-and-compiling-c-with.html
---

[OpenBabel](http://openbabel.org/wiki/Main_Page) is a tool making life easier for everyone in the computational chemistry business. You can do tons of cool stuff with [OpenBabel](http://openbabel.org/wiki/Main_Page)! I regularly use it to convert PDB files to input files for Gaussian09 and XYZ files, which are then used to calculate quantum chemistry on proteins and often back to PDB  format again. It can also juggle around with [SMILES](http://www.daylight.com/dayhtml/doc/theory/theory.smiles.html) strings, do all sorts of chem-informatics and whatnot. Everything you and I need.  [OpenBabel](http://openbabel.org/wiki/Main_Page) is [the King](http://www.elvis.com/) for these kinds of things. In addition [OpenBabel](http://openbabel.org/wiki/Main_Page) has API to Python, Pearl and C++, so you can use all the features of [OpenBabel](http://openbabel.org/wiki/Main_Page) in your own scripts or compiled programs!  I started using it for a project requiring automatic generation of thousands protein fragment XYZ files and automatic handling of input and output files. Many of my colleagues use [OpenBabel](http://openbabel.org/wiki/Main_Page) for stuff like this.

Not only is this an absolutely awesome piece of software, it is also completely free and open source under the [GNU GPL](http://www.gnu.org/licenses/gpl.html). I love this! This post concerns basic installation and use of the C++ API, which is not completely straightforward in some cases.



[![](http://upload.wikimedia.org/wikipedia/commons/f/fa/Babel256.png)](http://upload.wikimedia.org/wikipedia/commons/f/fa/Babel256.png)











For the some users, installing and compiling with [OpenBabel](http://openbabel.org/wiki/Main_Page) on a [Linux](http://xkcd.com/456/) machine can be a daunting task. Recently I helped my dear colleague Kasper Thofte setting up [OpenBabel](http://openbabel.org/wiki/Main_Page) and compiling a piece of C++ code on our local [Beowulf Cluster](http://en.wikipedia.org/wiki/Beowulf_%28computing%29).  Kasper is currently developing some very cool  automatic transition state search methods and he is also developing transition search methods in [GAMESS](http://www.msg.chem.iastate.edu/GAMESS/GAMESS.html), which is free quantum chemistry software Since we don't have root access on our cluster, everything has to be installed in our home directories and we cannot use the /usr/lib etc. folders.  These are all the steps we went through to get his C++ code to compile.

First I checked out the latest development code from the [SourceForge](http://en.wikipedia.org/wiki/SourceForge) into my ~/src/ dir. Compiled code goes in the ~/programs/ dir on my account.



cd ~/src
svn co https://openbabel.svn.sourceforge.net/svnroot/openbabel/openbabel/trunk openbabel





Step two is to actually compile the code with cmake and telling it to install to my ~/programs/  dir with the a prefix option.


cd openbabel
cmake -DCMAKE\_INSTALL\_PREFIX=/home/andersx/programs/openbabel .

make install



Now, to make sure that you can access [OpenBabel](http://openbabel.org/wiki/Main_Page) from the command line as well as having the g++ compilier find the libraries, you have to find a file in your home ~/ directory called .bashrc and insert these two lines:





export PATH=$PATH:/home/andersx/programs/openbabel/bin

export LD\_LIBRARY\_PATH=$LD\_LIBRARY\_PATH:/home/andersx/programs/openbabel/lib



Now open a new terminal (to refresh the PATH and  LD\_LIBRARY\_PATH variables) and you are ready to compile your [OpenBabel](http://openbabel.org/wiki/Main_Page) code. There is one small catch however. You have to manually tell g++ where your libraries and headers for [OpenBabel](http://openbabel.org/wiki/Main_Page) are located. This looks like this:





g++ -I/home/andersx/programs/openbabel/include/openbabel-2.0 -L/home/andersx/programs/openbabel/lib -lopenbabel OBtest.cpp -o OBtest



There you go! The above took us quite a while to figure out, and this should also work on any type of linux machine with the GNU compilers.



UPDATE: My friend Casper Steinmann just pointed out that he has a detailed blog post on installing and using the Python API for OpenBabel. So you should have no excuses for not using OpenBabel in your workflow: <http://www.caspersteinmann.dk/?q=node/4>

