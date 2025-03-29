---
layout: post
title: Molecular dynamics and supercomputing, past and present
date: '2011-11-06T16:20:00.002+01:00'
author: hellogetmyblogback
tags:
- nvidia gpu cpu flops gflops tflops plops supercomputing molecular dynamics tesla
top500
modified\_time: '2011-11-06T16:21:36.309+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-3689586499390686158
blogger\_orig\_url: https://combichem.blogspot.com/2011/11/molecular-dynamics-and-supercomputing.html
---

What is the most powerful single computer you can get today, measured in sheer numbers of double precision [floating point operations per second](http://en.wikipedia.org/wiki/FLOPS) (flops)? Possibly, the answer is a rack server (such as the HP half-width 4U in the picture below) with no less than eight(!) [Nvidia Tesla M2090](http://www.nvidia.com/object/preconfigured-clusters.html) cards installed. Each card offers a theoretical max of 665 Gflops of double precision computing power -- a total of 5.3 Tflops (not counting any CPUs). Now, go to the [Top500](http://www.top500.org/) and find the list from 10 years ago ... [November 2001](http://www.top500.org/list/2001/11/100). Just one of these servers would bring you a safe 3rd place on the list - very impressive for just ONE single machine.



|  |
| Foto |



One might argue, that a 4U rack unit is not a very practical computer - so what's the max you can cram into a a standard sized [ATX](http://en.wikipedia.org/wiki/ATX) chassis these days? Well, take four of these GPUs (and a good power supply), and you're up to about 2.7 Tflops, theoretical peak. 10 years ago that would bring well into top10 world wide with a box that you could put under your desk!

The Japanese "K Computer" is currently the fastest cluster out there and recently [broke the 10 Pflops barrier](http://www.top500.org/lists/2011/06/press-release) (10¹⁶ flops)! Now, he question is, when can I expect to have that kind of performance in a desktop PC, and how about my huge ms protein [molecular dynamics](http://en.wikipedia.org/wiki/Molecular_dynamics) simulation?

Speculating on the future is difficult, so I'm just going to "re-blog" this review paper I stumbled upon by [Michele Vendruscolo and Christopher M. Dobson, "Protein Dynamics: Moore’s Law in Molecular Biology", *Current Biology,* **21**, R68-R70 (2011)](http://www-vendruscolo.ch.cam.ac.uk/vendruscolo11cb.pdf), which speculates on the future of MD simulations. According to the authors, MD computing power sees exponential growth, so hopefully, I can one day have a 10 Pflops (I'm willing to settle for less) box under my desk. Here are some of the bold predictions from the paper:



* In 2010 we saw the first nanosecond scale MD simulation of the Ribosome
* In 2030 we'll be moving up to millisecond  scale MD simulation of the Ribosome
* in 2050 we'll be doing all-atom dynamics of entire bacteria on a nanosecond scale.



I can't wait to get older and see an all-atom bacteria simulation!