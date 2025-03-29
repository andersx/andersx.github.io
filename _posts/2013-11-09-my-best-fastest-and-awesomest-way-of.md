---
layout: post
title: My best, fastest and awesomest way of including matplotlib in Latex
date: '2013-11-09T15:20:00.004+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-11-10T12:41:51.617+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-5289782246416762058
blogger\_orig\_url: https://combichem.blogspot.com/2013/11/my-best-fastest-and-awesomest-way-of.html
---

If you use this post you will accomplish the following:

1. Be able Include loss-less vector graphics in your Latex document (good).
2. Be able to select text inside figures with mouse (awesome).
3. Compile your latex document with figures faster (probably).
4. Make your files smaller and of higher quality (mostly).
5. That's it. The rest is up to you!


I see many people still using .eps files from matplotlib in their otherwise nicely formatted Latex document.  Forget this. From this post on we use matplotlib figures in .pdf format. It works exactly as with .eps or .png or what else you might be using:




Let's also use pdflatex for compiling (just replace the latex command):




```
andersx@awesome:~/my\_paper$ pdflatex mypaper.tex

```



This will also compile much faster than standard latex (using, say, .png-files), since you are now including something that is already in pdf-format.



Last two steps are (1) to save your file as a .pdf-file and (2) autocrop the white borders (loss-less). Step one is accomplished using standard matplotlib/pylab/etc. syntax. The 2nd part is done using a tool called pdfcrop (which is already included in you have latex installed). I prefer to call pdfcrop from within the Python script to avoid having to run more than one command each time I make a new figure.



The above is some boilerplate code. The result is:




```
andersx@awesome:~/my\_paper$ python my\_figure.py
PDFCROP 1.33, 2012/02/01 - Copyright (c) 2002-2012 by Heiko Oberdiek.
==> 1 page written on `my\_file.pdf'.
andersx@awesome:~/my\_paper$
```



Now you have a beautifully formatted pdf-file to include in  your latex document.



I might edit this post later with a bit of bling and YOLO/swag just to give it the attractive combination of vitality and glamour it deserves.


