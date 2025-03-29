---
layout: post
title: How to run MOPAC6 on a modern Linux machine
date: '2011-12-05T22:21:00.006+01:00'
author: hellogetmyblogback
tags:
- mopac MOPAC2009 MOPAC6 6 ubuntu compiler gamess hartree ev electronvolt wine
modified\_time: '2011-12-16T14:27:40.831+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-6970130218504879708
blogger\_orig\_url: https://combichem.blogspot.com/2011/12/how-to-run-mopac6-on-modern-linux.html
---

Ahum, so we were trying to test the differences between [AM1](http://en.wikipedia.org/wiki/Austin_Model_1) and [PM3](http://en.wikipedia.org/wiki/PM3_%28chemistry%29) in [GAMESS](http://en.wikipedia.org/wiki/GAMESS_%28US%29) and [MOPAC2009](http://en.wikipedia.org/wiki/MOPAC) and surprisingly we found a not insignificant difference in the total energies. The energies are shifted by a constant factor of roughly ~1.000014.

However, going back to the MOPAC manual (<http://openmopac.net/manual/fun_con.html>) we see that the implementation of physical constants were changed in 1993. The GAMESS implementation of [semi-empirical methods](http://en.wikipedia.org/wiki/Semi-empirical_quantum_chemistry_method) is basically the MOPAC6 Fock-formation code with some GAMESS routines to diagonalize the whole shebang (for at least as far as single-point energies goes - I haven't been looking at derivatives yet).

Most likely this difference is due to conversion factors between kcal/mol (what MOPAC will give you) and hartrees (GAMESS). For instance, [this table](http://mccammon.ucsd.edu/%7Edzhang/energy-unit-conv-table.html) I randomly found on the Internet, which is *"taken from and old book by Karplus and Porter"*,says that 1 Hartree = 27.2107 eV, while Jimmy Stewart (Mr. MOPAC) says it's more like 27.2113961 eV. I guess the hartree must recently have been increased in energy along with the speed of light and neutrinos ...

Anyways, I wanted to go back and try MOPAC6 and see if the problem was in the code there. Compiling the MOPAC6 source code, however, proved very difficult, since the source syntax in certain places is not compatible with a newer [gfortran](http://en.wikipedia.org/wiki/Gfortran), and not even my fall-back f77 compiler would eat it. However, [Intel's trusty old Ifort compiler](http://software.intel.com/en-us/articles/intel-compilers/) was willing to eat MOPAC6 code, albeit with a couple of warnings. However, then the second problem arised. The Makefile was seemingly buggy and refused to work out-of-the-box on my Ubuntu 10.10 machine. So I decided to make a [Makefile](http://en.wikipedia.org/wiki/Makefile) that would actually run. The script can be found at the bottom of this page, and compiles like a charm, if you have Ifort. Ifort is free for non-commercial use, and usually also quite a bit faster than gfortran.

However, a possibly more simple (and possibly more fun) option is to install [Wine](http://en.wikipedia.org/wiki/Wine_%28software%29) (a Windows compatible environment for Linux) and use Wine to execute Mopac. Jimmy Stewart has a [Windows compatible binary version of MOPAC6 available for download](http://openmopac.net/Downloads/Downloads.html). Now, lemme show you how to install Wine and fire up MOPAC! On Ubuntu this is as simple as (let's say you have a file named methan.mop):



# $ sudo apt-get install wine   # This installs WINE

# $ wine mopac6.exe methan      # This runs MOPAC6 via wine with methan.mop



And that's it! Either get Ifort (free for non-commercial use) or use Wine to run MOPAC6 via the binary. I honestly found the last method to be hilarious, but I guess I'm not too different from the guy below.

[![](http://static.pokato.net/2011-03-10-11-09-56493792601.jpeg)](http://static.pokato.net/2011-03-10-11-09-56493792601.jpeg)

The newest MOPAC and GAMESS are available for free for personal and academic use.

**UPDATE:** Just a short note on how to run the compiled version of MOPAC6. First, rename **mopac.exe** to **MOPAC**. Next, set the mopac directory in **mopac.csh**. Now open a cshell and use the **mopac.csh** script to envoke MOPAC6:

# $ csh                     # Start a cshell****%** **source mopac.csh        # A script necessary to run MOPAC6****% mopac methan            # To actually run MOPAC6 with the file methan.mop



**$** indicates bash shell, and **%** indicates a cshell.



MOPAC6 Makefile for Intel Ifort: (put it in the source code directory and type 'make')<http://dl.dropbox.com/u/17435887/Makefile>