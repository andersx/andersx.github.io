---
layout: post
title: Getting started with Phaistos // installation
date: '2012-08-24T15:20:00.000+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2012-11-17T10:05:52.425+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-6216081371415176210
blogger\_orig\_url: https://combichem.blogspot.com/2012/08/getting-started-with-phaistos.html
---


I got an e-mail from a student named Alex at Boston University, asking if I could help him getting started with [Phaistos](http://www.phaistos.org/). First barrier for new users (in this case a student), is to download and install Phaistos on a Linux machine.




First, you have to download the latest and greatest revision of Phaistos from [sourceforge](http://sourceforge.net/projects/phaistos/). This is done via the following command:

```
svn checkout https://phaistos.svn.sourceforge.net/svnroot/phaistos/trunk phaistos
```


This creates a directory named "phaistos" and downloads the source code to taht directory. Now enter the "phaistos" directory, create a directory named "build" and then cd into that dir:

```
cd phaistos
mkdir build
cd build
cmake ..
```


cmake prepares the compilation step and finds out where your libraries etc. are located. Note that Phaistos requires [Boost](http://www.boost.org/) installed, version 1.41 or newer.




If you have Boost installed in a non-standard location and cmake doesn't find the Boost libraries, things are slightly more complicated. On my laptop (running CentOS 6.3), I cannot get cmake to consistently find my Boost via the "-DBOOST\_ROOT" option as mentioned in the Phaistos manual. Usually using these cmake command flags should work. Note that in the example, I have boost installed in "/opt/boost\_1\_41\_0".




```
cmake -DBoost\_DIR=/opt/boost\_1\_41\_0 -DBoost\_INCLUDE\_DIR=/opt/boost\_1\_41\_0/include
```



In this case, I explicitly tell cmake where all Boost libraries are located via these two lines (the second line is "a bit" long, make sure you get everything when you copy/paste!):

```
export BOOST\_ROOT\_DIR=/opt/boost\_1\_41\_0 #(i.e. where Boost is installed)
cmake -DBoost\_INCLUDE\_DIR=$BOOST\_ROOT\_DIR/include -DBoost\_LIBRARY\_DIRS=$BOOST\_ROOT\_DIR/lib -DBoost\_PROGRAM\_OPTIONS\_LIBRARY=$BOOST\_ROOT\_DIR/lib/libboost\_program\_options.a -DBoost\_SERIALIZATION\_LIBRARY=$BOOST\_ROOT\_DIR/lib/libboost\_serialization.a -DBoost\_THREAD\_LIBRARY=$BOOST\_ROOT\_DIR/lib/libboost\_thread.a -DBoost\_INCLUDE\_DIR\_DEBUG=/home/kasper/boost\_build/include -DBoost\_LIBRARY\_DIRS\_DEBUG=$BOOST\_ROOT\_DIR/lib -DBoost\_PROGRAM\_OPTIONS\_LIBRARY\_DEBUG=$BOOST\_ROOT\_DIR/lib/libboost\_program\_options.a -DBoost\_SERIALIZATION\_LIBRARY\_DEBUG=$BOOST\_ROOT\_DIR/lib/libboost\_serialization.a -DBoost\_THREAD\_LIBRARY\_DEBUG=$BOOST\_ROOT\_DIR/lib/libboost\_thread.a -DBoost\_INCLUDE\_DIR\_RELEASE=/home/kasper/boost\_build/include -DBoost\_LIBRARY\_DIRS\_RELEASE=$BOOST\_ROOT\_DIR/lib -DBoost\_PROGRAM\_OPTIONS\_LIBRARY\_RELEASE=$BOOST\_ROOT\_DIR/lib/libboost\_program\_options.a -DBoost\_SERIALIZATION\_LIBRARY\_RELEASE=$BOOST\_ROOT\_DIR/lib/libboost\_serialization.a -DBoost\_THREAD\_LIBRARY\_RELEASE=$BOOST\_ROOT\_DIR/lib/libboost\_thread.a -DBoost\_THREAD\_LIBRARY=$BOOST\_ROOT\_DIR/lib/libboost\_thread.a -DBoost\_REGEX\_LIBRARY=$BOOST\_ROOT\_DIR/lib/libboost\_regex.a -DBoost\_THREAD\_LIBRARY\_DEBUG=$BOOST\_ROOT\_DIR/lib/libboost\_thread.a -DBoost\_REGEX\_LIBRARY\_DEBUG=$BOOST\_ROOT\_DIR/lib/libboost\_regex.a -DBoost\_THREAD\_LIBRARY\_RELEASE=$BOOST\_ROOT\_DIR/lib/libboost\_thread.a -DBoost\_REGEX\_LIBRARY\_RELEASE=$BOOST\_ROOT\_DIR/lib/libboost\_regex.a -DBoost\_UNIT\_TEST\_FRAMEWORK\_LIBRARY=$BOOST\_ROOT\_DIR/lib/libboost\_unit\_test\_framework.a -DBoost\_UNIT\_TEST\_FRAMEWORK\_LIBRARY\_DEBUG=$BOOST\_ROOT\_DIR/lib/libboost\_unit\_test\_framework.a -DBoost\_UNIT\_TEST\_FRAMEWORK\_LIBRARY\_RELEASE=$BOOST\_ROOT\_DIR/lib/libboost\_unit\_test\_framework.a -DBoost\_FILESYSTEM\_LIBRARY=$BOOST\_ROOT\_DIR/lib/libboost\_filesystem.a -DBoost\_FILESYSTEM\_LIBRARY\_DEBUG=$BOOST\_ROOT\_DIR/lib/libboost\_filesystem.a -DBoost\_FILESYSTEM\_LIBRARY\_RELEASE=$BOOST\_ROOT\_DIR/lib/libboost\_filesystem.a -DBoost\_SYSTEM\_LIBRARY=$BOOST\_ROOT\_DIR/lib/libboost\_system.a -DBoost\_SYSTEM\_LIBRARY\_DEBUG=$BOOST\_ROOT\_DIR/lib/libboost\_system.a -DBoost\_SYSTEM\_LIBRARY\_RELEASE=$BOOST\_ROOT\_DIR/lib/libboost\_system.a ..

```

Now you are ready to compile:


```
make -j4
```

After a while you'll get a message saying:


```
Linking CXX executable ../../../bin/phaistos
[100%] Built target phaistos
```

From the build directory, you can test if Phaistos is working by typing


```
bin/phaistos --help
```

(A very long list of options should appear).



To compile the manual, cd into the "phaistos/build" dir ands


```
make manual\_pdf
```

This compiles the manual and puts it in the "phaistos/build/doc" dir. You can also download the latest manual from sourceforge [here](http://sourceforge.net/projects/phaistos/files/Phaistos/).



Do post a comment below, if you have questions or additional comments!