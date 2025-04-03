---
layout: post
title: Visualizing Pointer Aliasing in Python
date: '2013-05-07T12:54:00.000+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-05-08T10:57:58.748+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-91298897278992501
blogger\_orig\_url: https://combichem.blogspot.com/2013/05/visualizing-pointer-aliasing-in-python.html
---

<!---
[![](http://ip-pig.com/wp-content/uploads/2013/04/Python-Programming-Language.png)](http://ip-pig.com/wp-content/uploads/2013/04/Python-Programming-Language.png)
-->
This is a concept that has annoyed many a newcomer to Python - including the author of this post. I will be talking briefly about this concept in my lecture next Thursday, and the excellent TA, [+Jimmy Charnley Kromann](http://plus.google.com/111529974982597129664), pointed me to an example he saw on pythontutor. The Visualize tool on pythontutor is a very explicit way to illustrate what happens under the hood of python: [http://www.pythontutor.com/visualize.html](http://www.pythontutor.com/visualize.html%3C/a%3E%20%3Cbr%20/%3E%3Cbr%20/%3EEDIT:%20There%20seems%20be%20an%20issue%20with%20pythontutor%20at%20the%20moment.%20Click%20this%20link,%20if%20the%20embedded%20code%20below%20fails%20to%20run.%3Ca%20href=)



The Visualize tool on pythontutor is a very clever way to illustrate what happens under the hood of python: <http://www.pythontutor.com/visualize.html>



EDIT: There seems to be a problem with python tutor at the moment. Click this link, if the embedded code below fails to load:  [Python Example](http://www.pythontutor.com/visualize.html#code=a+%3D+%5B1.0,+2.0,+3.0,+4.0%5D%0Ab+%3D+a%0A%0Aprint+%22a+%3D+%22,+a%0Aprint+%22b+%3D+%22,+b%0A%0Aa%5B1%5D+%3D+99.0%0A%0Aprint+%22a+%3D+%22,+a%0Aprint+%22b+%3D+%22,+b&mode=display&cumulative=true&heapPrimitives=true&drawParentPointers=true&textReferences=false&showOnlyOutputs=false&py=2&curInstr=0)


*EDIT: embedded code not working - click link:*

[Python Example](http://www.pythontutor.com/visualize.html#code=a+%3D+%5B1.0,+2.0,+3.0,+4.0%5D%0Ab+%3D+a%0A%0Aprint+%22a+%3D+%22,+a%0Aprint+%22b+%3D+%22,+b%0A%0Aa%5B1%5D+%3D+99.0%0A%0Aprint+%22a+%3D+%22,+a%0Aprint+%22b+%3D+%22,+b&mode=display&cumulative=true&heapPrimitives=true&drawParentPointers=true&textReferences=false&showOnlyOutputs=false&py=2&curInstr=0)




If you want to avoid pointer aliasing,  you usually want to do one of these three things in order to copy a to b:



1)



b = a[:]



This takes a shallow copy. Personally, I don't like this option, because the slicing notation is not clear to everyone. But it is definitely the shorter notation. This will fail if you want to copy a list of lists. Being a shallow copy, it does not copy lists of lists. It will merely  copy the list of pointers to other "sub"-lists.





2)



import copy

b = copy.copy(a)



This takes a shallow copy. It is more clear from the code what this does, so this is usually my preferred approach. Still a shallow copy, however.





3)



import copy

b = copy.deepcopy(a)



This is the fail-safe method and takes a copy of everything, so use this if you're in doubt. Works pretty much in every case. The down side is that it's usually slower to copy everything with deepcopy.






[![](http://www.openbookproject.net/thinkcs/python/english2e/_images/mult_references1.png)](http://www.openbookproject.net/thinkcs/python/english2e/_images/mult_references1.png)





Figure 1 : a and b contain to the

word "banana" in two different ways.


