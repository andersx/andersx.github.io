---
layout: post
title: Saving data into cPickle format (in Python)
date: '2013-02-21T14:39:00.000+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-02-22T15:45:20.082+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-2193218398867009815
blogger\_orig\_url: https://combichem.blogspot.com/2013/02/saving-into-data-into-cpickle-format-in.html
---

I recently created a python script which generated a huge-ass dictionary, which I wanted to save and use later in another program. The simple solution was simpy to print my dictionary and pipe it into txt file. However, the txt file ended up being almost 500MB large, and using eval() to parse the text string into a Python object took around 5 minutes. Enter the cPickle module. cPickle uses a C implementation of handling pickled stuff, which effectively means that it's faster. In my case loading time decreased to few seconds, and the size of the cPickle'd file was 50% of the plain .txt file.



Here are a couple of snippets to get you started using cPickle.


First import cPickle. The cPickle module should be included in most Python distributions



```
import cPickle
```

In this example, we have an array called "large\_array" created by the "create\_large\_array()" function. I want to store "large\_array" in a file called "large\_array.cpickle".



```
large\_array = create\_large\_array()
output\_filename = "large\_array.cpickle"

f = open(output\_filename,"wb")
cPickle.dump(large\_array, f, protocol=2)
f.close()

```

In this example "open()" is called with the second argument "wb", which tells Python to open the file in write/binary mode.

"cPickle.dump()" dumps "large\_array" into "f" using "protocol=2". cPickle has several different modes which all do the same -- protocol 2 is the fastest.



If you want to load the array from  "large\_array.cpickle" in another script, you can use something like this:


```
def load\_pickle(filename):
f = open(filename,"rb")
p = cPickle.load(f)
f.close()
return(p)


large\_array = load\_pickle("large\_array.cpickle")

```

