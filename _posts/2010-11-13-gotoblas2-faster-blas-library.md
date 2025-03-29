---
layout: post
title: 'GotoBLAS2: A faster BLAS library'
date: '2010-11-13T13:54:00.007+01:00'
author: hellogetmyblogback
tags:
- BLAS
- installing
- Gotoblas2
- compiling
modified\_time: '2010-11-13T22:07:15.598+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-8215747758042337184
blogger\_orig\_url: https://combichem.blogspot.com/2010/11/gotoblas2-faster-blas-library.html
---

This post is about a little trick I have for speeding up my quantum chemistry calculations. For some reason this software is unknown to most people, and thus I decided do a short advertisement!

The bottleneck in all forms of computational quantum chemistry is the CPU time spent to converge a calculation to a given accuracy. The most expensive part is in most cases the evaluation of two-electron integrals,  but during many calculations, a lot of time is also spent doing linear algebra operations. The computational routines for doing linear algebra has been optimized greatly over the years and today there are quite a few standard linear algebra libraries for doing this. One standard API of linear algebra routines is called [BLAS](http://en.wikipedia.org/wiki/Basic_Linear_Algebra_Subprograms), short for "Basic Linear Algebra Subprograms". Many equations involved in quantum chemistry are formulated into [matrix notation](http://www.cobalt.chem.ucalgary.ca/ziegler/educmat/chm386/rudiment/quanmath/matrix.htm) which is easily interpreted and turned into fast, parallelized code.

[![](http://www.enotes.com/w/images/thumb/e/eb/Matrix_multiplication_diagram_2.svg/313px-Matrix_multiplication_diagram_2.svg.png)](http://www.enotes.com/w/images/thumb/e/eb/Matrix_multiplication_diagram_2.svg/313px-Matrix_multiplication_diagram_2.svg.png)When people want to use a fast BLAS library, often well-known libraries such as the ALTAS or Intel MKL BLAS libraries are used. However, there is a slightly faster BLAS library out there, named GotoBLAS2. This library was started by a Japanese guy named Kazushige Goto (the Japanese pronunciation is something lik "go-toe"), who, like Albert Einstein, worked in a patent office while he began developing his own BLAS library. After a while he was 'discovered' by a university in Texas and soon Goto was deported from Japan. Now he is said to be very rich and working for some large [coporation](http://en.wikipedia.org/wiki/Corporation_%28rpg%29) somewhere in the US.

Enough with the history! Tobias Wittwer has made a detailed comparison of common, fast BLAS libraries in this pdf: [blas\_lapack.pdf](http://www.google.com/url?sa=t&source=web&cd=5&sqi=2&ved=0CDQQFjAE&url=http%3A%2F%2Fwww.tudelft.nl%2Flive%2FServeBinary%3Fid%3D608b3015-5195-438a-90fc-c30c2252a066%26binary%3D%2Fdoc%2Fblas_lapack.pdf&rct=j&q=gotoblas%20mkl%20atlas%20comparison&ei=0YLeTKidJIabOuDQkJ0P&usg=AFQjCNFX2E0OBb7CNcDJJ2zmcA6Mm15-6Q&cad=rja)



You can download the latest GotoBLAS2 [here](http://www.tacc.utexas.edu/tacc-projects/gotoblas2/). Not only is GotoBLAS2 faster  than Intel's MKL library, GotoBLAS2 is also completely free of charge and open source under the [BSD license](http://en.wikipedia.org/wiki/BSD_licenses).

GotoBLAS2 will be used in future posts on compiling and installing software, for which I am always using GotoBLAS2. So be sure to use this and make the most of your CPU resources!