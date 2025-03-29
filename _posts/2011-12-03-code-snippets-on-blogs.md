---
layout: post
title: Code snippets on blogs
date: '2011-12-03T09:34:00.037+01:00'
author: hellogetmyblogback
tags:
- code snippets blog blogger python fortran c cpp cplusplus c++ syntax highlighter
craftyfella pre
modified\_time: '2011-12-03T13:12:22.608+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-2276984618736767472
blogger\_orig\_url: https://combichem.blogspot.com/2011/12/code-snippets-on-blogs.html
---

I had a brief discussion with [Casper Steinmann](http://pythonchem.blogspot.com/) about how to present [code snippets](http://en.wikipedia.org/wiki/Snippet_%28programming%29) in blog-posts. We both agreed that nice formatting is really important. We also agreed that our previous attempts to do so were (and still are) crummy.

One of the (easy) ways to set up nice formatting can be found over at CraftyFella:

<http://www.craftyfella.com/2010/01/syntax-highlighting-with-blogger-engine.html>

Just put some stuff in your blogger template and all you have to do is wrap your code examples in HTML <pre>  tags. "Pre tags" denote a pre-defined format, and are often used to wrap around code examples, with the color coding pertaining to that particular code-language and an equispaced font, which makes it possible to use indentations properly.

Most popular programming languages are supported in this method. C, C++ C#, python, and probably many others. Hoever, no support for FORTRAN - quantum chemists beware. If you know of a user-friendly syntax highlighter that does FORTRAN, do leave a comment! Everybody loves FORTRAN.

Here is a simple example of how a dumb python script looks after using CraftyFella's guide to set things up:


```
#!/usr/bin/python

def print\_string(string):
print string

hello\_world = "Hello world, look at my fancy code formatting!"

for i in range(10):
print\_string(hello\_world)

```


... and a little bit of C++:


```
//! Return size of collection
//! \return size of collection
unsigned int size() const {
return data\_vector.size();
}

```


