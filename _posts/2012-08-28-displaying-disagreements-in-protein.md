---
layout: post
title: Displaying disagreements in protein structures
date: '2012-08-28T17:19:00.000+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2012-08-28T17:20:50.640+02:00'
thumbnail: https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjeYv2j\_l77c9GpzCzBjQ-z7VlrGwdms9HYz6WrfPrQbP7MQ7wfTr2dfSYTeyP\_UujJCOOVTjcFjFHuIuxMUKptNYR2tpG1URRYExrJujvpoSxU0jzp3OElyGPL1D03O0nVPCBL3vLZknfi/s72-c/ct-2011-00913r\_0015.gif
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-7510711206894690937
blogger\_orig\_url: https://combichem.blogspot.com/2012/08/displaying-disagreements-in-protein.html
---


I recently made a [post](http://combichem.blogspot.dk/2012/06/coloring-residues-by-chemical-shift.html) on how to color a protein structure by
disagreements between experimental chemical shifts and chemical shifts
predicted from the structure.




Here is another way to display these kinds of errors, which I also find quite nice. Atoms radii are scaled proportionally the error in predicted chemical shifts.






[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjeYv2j_l77c9GpzCzBjQ-z7VlrGwdms9HYz6WrfPrQbP7MQ7wfTr2dfSYTeyP_UujJCOOVTjcFjFHuIuxMUKptNYR2tpG1URRYExrJujvpoSxU0jzp3OElyGPL1D03O0nVPCBL3vLZknfi/s640/ct-2011-00913r_0015.gif)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjeYv2j_l77c9GpzCzBjQ-z7VlrGwdms9HYz6WrfPrQbP7MQ7wfTr2dfSYTeyP_UujJCOOVTjcFjFHuIuxMUKptNYR2tpG1URRYExrJujvpoSxU0jzp3OElyGPL1D03O0nVPCBL3vLZknfi/s1600/ct-2011-00913r_0015.gif)

Picture from <http://dx.doi.org/10.1021/ct200913r> (paywall)




In the above example, chemical shifts are determined from quantum chemical calculations on a 23 residue protein structure, and then compared to the corresponding experimental chemical shifts.

It's clear, that calculated chemical shifts from several side chains atom do not reproduce the experimental values as well as the backbone atoms chemical shifts. Definitely useful to identify possible errors in the protein structure







# References:

Frank et al. J. Chem. Theory Comput., **2012,** *8* (4), pp 1480–1492

<http://dx.doi.org/10.1021/ct200913r>
