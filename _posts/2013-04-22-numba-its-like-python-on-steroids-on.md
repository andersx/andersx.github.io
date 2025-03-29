---
layout: post
title: Numba -- It's like Python on steroids ... on steroids.
date: '2013-04-22T17:23:00.000+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-04-22T17:24:26.206+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-127044513647356719
blogger\_orig\_url: https://combichem.blogspot.com/2013/04/numba-its-like-python-on-steroids-on.html
---


I'm teaching a course in molecular simulations this year in which the students have to program simulations of various simple systems. We're trying to keep things as simple as possible and everything is done in Python.




The task of the second week of the course is to program a simple simulation of a 2D Lennard-Jones gas. The computationally intensive step in this exercise is calculating the energy and gradient. In the past we've (and by we, I mean [+Casper Steinmann](http://plus.google.com/103736230785278907829)) written a FORTRAN module and used F2PY to exploit the awesomeness of the good old FORmula TRANslator and have the students link their Python scripts with a precompiled .so module for this to run at an acceptable speed




This solution is of course completely fine, but not very general. I've been looking into Cython, pypy and various other methods to speed things up a bit. But they all seemed quite elaborate an unPythonic, and you might as well do the whole thing in C then.




Yesterday Numba was brought to my attention on [this blog](http://jakevdp.github.io/blog/2012/08/24/numba-vs-cython/). In short, Numba is a way to compile (at execution time) Python functions into C which normally makes your program run substantially faster. The blog has a very nice example where the code gets a 1000x speedup just by adding one function decorator. And the syntax couldn't be simpler.




Let's try Numba on our Lennard-Jones program. Here is the naive function they have to implement:




The code is then executed via Python as one would normally execute Python code, specifying in the script that the calc\_energy\_and\_gradient() function is to be compiled via Numba. This can be done simply by writing the the @autojit decorator before the function definition.




You can also specify exactly which type of input the *just-in-time* compilation takes takes (an example is commented out). This makes the compilation of the function be type specific, but a bit faster.  Make sure to import numba and the specific type of argument (here the arguments are of the type double) and the relevant decorators (jit or autojit).




**Conclusion:** 10000 gradient and energy evaluations take 16 seconds with the standard Python implementation. 2 seconds with @autojit and 1.5 seconds with the explicit @jit. Not too bad for something that doesn't take ANY code changes.




[![](http://cdn.memegenerator.net/instances/400x/28705638.jpg)](http://cdn.memegenerator.net/instances/400x/28705638.jpg)








