---
layout: post
title: Energy minimization RMS gradient convergence criterion; GAMESS vs. Tinker.
date: '2013-10-19T15:53:00.002+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-10-19T18:10:17.910+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-4461145232616687861
blogger\_orig\_url: https://combichem.blogspot.com/2013/10/energy-minimization-rms-gradient.html
---


As per the recommendations of [+Jan Jensen](http://plus.google.com/113593600676820394169)  we have in our group been using the convergence criterion, in GAMESS, OPTTOL=0.0005 in the $STATPT input group for minimization of protein structures using various fast quantum mechanical methods ranging from correlated fragment based methods to dispersion corrected Hartree-Fock theory to semi-empirical methods.












Yesterday I was about to carry out a molecular mechanics energy minimization of a PDB structure in order to remove a couple seemingly spurious bond lengths in the structure before putting the coordinates through a full QM calculation. I started Tinker and I was immediately faced with a cryptic question to which I only knew the answer in terms of GAMESS' OPTTOL variable:






# Enter RMS Gradient per Atom Criterion [0.01] :




Firstly, the question is cryptic since there is no mention of units. Secondly, my answer to the question was also not obvious, since I didn't know exactly what OPTTOL=0.0005 (which is in hartree/bohr) really meant. INPUT.DOC in the gamess/ directory was helpful:





>
> OPTTOL = gradient convergence tolerance, in Hartree/Bohr.
>
>          Convergence of a geometry search requires the
>
>          largest component of the gradient to be less
>
>          than OPTTOL, and the root mean square gradient
>
>          less than 1/3 of OPTTOL.  (default=0.0001)







So actually, my convergence criterion of the RMS gradient in GAMESS has been 1/3 * 0.0005 hartree/bohr.




After a bit of detective work, it turns out that the units of the Tinker criterion is in kcal/mol/angstom. So the relevant conversion factors from OPTTOL in GAMESS to RMS gradient in Tinker are can be summed up as:



* 1/3 (GAMESS definition)
* 627.51 (hartree to kcal/mol)
* 1.8897 (angstrom to bohr)






The total conversion factor becomes: 1/3 * 627.51 * 1.8897 = 395.27




All in all OPTTOL=0.0005 in "GAMESS units" (hartree/bohr) amounts to an RMS gradient of 0.2 in "Tinker units" (kcal/mol/angstom).




*Happy Tinkering!*
