---
layout: post
title: Numpy vs. cPickles (Python, ofc)
date: '2013-08-16T10:52:00.003+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-08-16T10:53:31.248+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-7212629680849730199
blogger\_orig\_url: https://combichem.blogspot.com/2013/08/numpy-vs-cpickles-python-ofc.html
---

I've been using **cPickels** for storing data into a Python-friendly format for some time. See my earlier blog post for more on **cPickles**.

<http://combichem.blogspot.dk/2013/02/saving-into-data-into-cpickle-format-in.html>



I have also been using Numpy's save function to do the same thing. numpy.save() and **numpy.load()** is so much simpler, however. I really recommend that people use **numpy.save()** and **numpy.load()** over **cPickles** for most purposes. It is *so* much more simple.



I always thought a **cPickle** was much, much faster than Numpy, but I guess I was wrong, according to this stackoverflow I just saw. Below are loading and saving times for a large array. Practically no difference between Numpy and cPickles!




[![](http://i.stack.imgur.com/gEWnv.png)](http://i.stack.imgur.com/gEWnv.png)
Source: <http://stackoverflow.com/questions/16833124/pickle-faster-than-cpickle-with-numeric-data>

















To save an array, a list or dictionary or whatever called **my\_array** into **my\_file.npy**:



# numpy.save("my\_file", my\_array)



Note that Numpy appends .npy to the filename automatically.





To load the stored data simply:



# my\_array = numpy.load("my\_file.npy")



Really py-fragging-thonicly easy!