---
layout: post
title: You know what really grinds my gears? (In Python)
date: '2013-08-10T13:53:00.000+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-08-10T13:53:16.382+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-4000846360379862502
blogger\_orig\_url: https://combichem.blogspot.com/2013/08/you-know-what-really-grinds-my-gears-in.html
---

I can never correctly remember when things are passed as references or copied as local variables inside functions.





Take these two, innocuously looking functions. Because both do the same thing (namely set the contents of a vector, P, to [1, 1]) I call them 1 and a, respectively, since one is not better than the other.





**def implementation\_1(P):

P = [1, 1]


def implementation\_a(P):

P[0] = 1
P[1] = 1**





What you would expect is one of the following two options

1. Both functions change P to [1, 1] (permanently).
2. Both functions take a local copy of P and change it to [1, 1], and after the function returns, the local [1, 1] array is forgotten.




A simple test is to do this:





**P = [0, 0]
print P

implementation\_1(P)
print P

implementation\_a(P)
print P**



which prints:



**[0, 0]
[0, 0]
[1, 1]**



So clearly implementation\_a() is different from implementation\_1(), although they seemingly do the same.




[![](http://cdn.meme.li/instances/600x/40456695.jpg)](http://cdn.meme.li/instances/600x/40456695.jpg)






