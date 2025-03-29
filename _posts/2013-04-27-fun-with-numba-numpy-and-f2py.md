---
layout: post
title: Fun with Numba, NumPy and F2PY
date: '2013-04-27T14:39:00.000+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-04-27T14:43:21.687+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-2963814980715068988
blogger\_orig\_url: https://combichem.blogspot.com/2013/04/fun-with-numba-numpy-and-f2py.html
---


I've started using Numba to speed up MD simulations for our course in Molecular Statistics, which I teach together with [+Jan Jensen](http://plus.google.com/113593600676820394169) and [+Jimmy Charnley Kromann](http://plus.google.com/111529974982597129664).






One exercise is using the Velo-Verlet algorithm to simulate a Lennard-Jones gas/liquid with periodic boundary conditions. Since we do everything in Python in this course, running the actual simulation is quite slow. What we do is we supply the students with a compiled FORTRAN module, compiled with F2PY which they can use when they've written their own Lennard-Jones gradient code.




The FORmula TRANslator module is extremely fast, compared to Python code. I rewrote the code from last year's course to a function I could use with Numba and compare directly to the F2PY module




For 100 particles:


* **Numba:** 0.146 ms/iteration (466x speed up)
* **F2PY:** 0.236 ms/iteration (289x speed up)
* **Python:** 68.06 ms/iteration


Now, I'm pretty sure a pure FORTRAN implementation would be faster than the F2PY, but I am very impressed with the Numba, since it's standard Python code and MUCH more simple to read than FORTRAN -- in theory all you have to do is write @autojit before a function definition. This is of course in theory ... I didn't do anything to improve on the readability of the Lennard-Jones code here. Before I posted, I had actually inserted a comment saying """This code block is unreadable""".



Now, there were a few issues I discovered:





# 1) @autojit vs. @jit(argtypes=[double[:,:], double[:]])



Since Numba has to decide what arguments a function takes before it's being compiled, it needs to know what possible type of arguments the function takes. If you're lucky, Numba decides on the right type and you can get away with @autojit, but sometimes autojitting makes the code SLOWER than standard, interpreted Python. In one case I got a factor of 10x slower with autojit, but explicitly stating the function argument types with @jit(argtypes = .... ) I got a speed up of 20x on the same code compared to interpreted Python.





# 2) Returning tuples in compiled code block



Numba does not allow a tuple to be returned inside a compiled code block. So don't do this.





# 3) Use Numpy properly



* Numba is NumPy aware, so code ran faster when numbers were stored in numpy.array types rather than just regular Python lists.
* At first I was numpy.round() to round to nearest integer in the periodic boundary condition code. Switching three numpy.round() calls to numpy.rint() gave a speed up of around 100x on the code execution.
* Use U[i, j] instead of U[i][j] on NumPy arrays. Not much of a difference in the vanilla Python code, but MASSIVE speed gains in compiled code.






Python Lennard-Jones code:






F2PY Lennard-Jones code:






Python Velo-Verlet solver:















[![](http://spinnakr.com/blog/wp-content/uploads/2013/03/Aint-Nobody-Got-Time.jpg)](http://spinnakr.com/blog/wp-content/uploads/2013/03/Aint-Nobody-Got-Time.jpg)







