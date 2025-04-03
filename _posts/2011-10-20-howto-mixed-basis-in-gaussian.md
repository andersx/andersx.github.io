---
layout: post
title: 'HOWTO: Mixed basis In Gaussian'
date: '2011-10-20T21:52:00.001+02:00'
author: hellogetmyblogback
tags:
- mixed basis sets gaussian methylamin 3-21g 6-31g
modified\_time: '2011-10-20T21:52:35.125+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-3510255092503445280
blogger\_orig\_url: https://combichem.blogspot.com/2011/10/howto-mixed-basis-in-gaussian.html
---

One of the many tricks in speeding up quantum chemistry calculations is the use of locally dense basis sets. Maybe it is only necessary to have diffuse functions on certain atoms or you may only need to calculate parts of a system with a high accuracy.Use of locally dense basis sets is completely legal within the basis set approximation. Just remember, that things like Mulliken charges will often be off, if some atoms have significantly more basis functions than other. 

<!--- [![](http://upload.wikimedia.org/wikipedia/commons/6/6d/Methylamine-3D-balls.png)](http://upload.wikimedia.org/wikipedia/commons/6/6d/Methylamine-3D-balls.png)
-->
In this example an example, I show how to do a geometry optimization a of methylamine at the Hartree-Fock level of theory with a 6-31G basis set on carbon and nitrogen and a 3-21G basis set on hydrogen atoms. Note that the basis set is specified as "gen". This option tells Gaussian to use the basis set specified at the bottom. A the bottom there is a number for each atom (followed by a 0 for reasons unknown to the author), and the name of the basis set below. Data for each atom is terminated by a line containing four asterisks.

Remember to separate "groups" of data with *exactly one* blank line and the file *must* be terminated with *exactly two* blank lines.

--------------------------Start of file--------------------------# opt hf/gen geom=connectivity

Title Card Required

0 1 C                 -7.52265887    1.01208458    0.00000000 H                 -7.16600444    0.00327457    0.00000000 H                 -7.16598603    1.51648277   -0.87365150 H                 -8.59265887    1.01209776    0.00000000 N                 -7.03265039    1.70504284    1.20025020 H                 -6.03265041    1.70487221    1.20034129 H                 -7.36582334    2.64790856    1.20015900

1 2 1.0 3 1.0 4 1.0 5 1.0 2 3 4 5 6 1.0 7 1.0 6 7

1 06-31G****2 03-21G****3 03-21G****4 03-21G****5 06-31G****6 03-21G****7 0 3-21G****

--------------------------End of file--------------------------
