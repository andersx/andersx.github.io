---
layout: post
title: Guistos - a PHAISTOS GUI
date: '2012-06-13T18:14:00.002+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2012-06-13T18:14:49.548+02:00'
thumbnail: https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTpKHpCGIyVclVIgsEViMxux0oTxPDRzS6K6zRosDkL8Wsmx3\_Shi5phNMBgW5yAKGFBRwmrzsVb9EhCCuFLxYOOxzYNhd2og54uPcPMt9Wo\_sMRQAJ6uui6fXXRvRAU5m7PojCSZvCOgd/s72-c/Screenshot-Guistos+-+a+Phaistos+GUI.png
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-2384723833626273678
blogger\_orig\_url: https://combichem.blogspot.com/2012/06/guistos-phaistos-gui.html
---


This is what can be considered the user documentation for Guistos - a PHAISTOS GUI. I will upload the code to here soon.




Guistos is aimed at people who are new to molecular modeling, such as for instance bachelor students. It assumes relatively little knowledge and also limited Linux skills.

I've put some care into setting sane default values and sets of good mixes of Monte Carlo moves.




Developers will probably still prefer to use **{$FAVOURITE\_EDITOR}** and edit your text-based input files by hand.

If you want your PHAISTOS module supported in Guistos, give me a mail at: **andersx (a) nano.ku.dk** and we can work something out.







[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTpKHpCGIyVclVIgsEViMxux0oTxPDRzS6K6zRosDkL8Wsmx3_Shi5phNMBgW5yAKGFBRwmrzsVb9EhCCuFLxYOOxzYNhd2og54uPcPMt9Wo_sMRQAJ6uui6fXXRvRAU5m7PojCSZvCOgd/s640/Screenshot-Guistos+-+a+Phaistos+GUI.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTpKHpCGIyVclVIgsEViMxux0oTxPDRzS6K6zRosDkL8Wsmx3_Shi5phNMBgW5yAKGFBRwmrzsVb9EhCCuFLxYOOxzYNhd2og54uPcPMt9Wo_sMRQAJ6uui6fXXRvRAU5m7PojCSZvCOgd/s1600/Screenshot-Guistos+-+a+Phaistos+GUI.png)










# General Options




# Start from AA/PDB-file:

In order to tell PHAISTOS what the sequence of the protein is, you must supply a **.aa**-file containing the sequence. You can also choose to have the sequence (but not structure!) read-in from a **.pdb**-file or **.pqr**-file which are also PHAISTOS compatible.




# Initialize from PDB structure:

If you supply PHAISTOS with a **.pdb**-file or **.pqr**-file, you can "cheat" and start your simulation from the supplied structure. This is useful for things as refinement of homology models and simulation of protein dynamics.




# Short Title:

The title is a short name, which is applied to output files from the simulation. sample files will be put in a subfolder to the Guistos default output path (usually **/home/$USER/folds**), and the muninn logfile will also be named with this title.




# Iterations (per thread) and Parallel threads:

Iterations is how many Monte Carlo moves are carried out during a simulation. Parallel threads is the number of parallel simulations.

The total number of iterations in your ensemble will be the product of these two values.







# Energy Options




# Molecular Mechanics Force Field:

PHAISTOS currently supports two [molecular mechanics](http://en.wikipedia.org/wiki/Molecular_mechanics) force fields. The OPLS-AA/L force field and the coarse-grained PROFASI force field. PROFASI is much faster than OPLS-AA/L, while OPLS-AA/L is more accurate.




# Spectroscopic Data:






# Model:

By supplying a model of prediction of spectroscopic data from a given structure, is is possible to model the agreement between a proposed structure and experimental data.

Currently Guistos supports backbone chemical shift data via the CamShift module, and amide proton chemical shift data via the ProCS  module (it's pronounced *pro-see-es*).




# Data File:

CamShift needs a set of experimental data in the NMR-STAR format, while ProCS uses the internal **.bcs**-format.







# Monte Carlo Options




# Metropolis-Hastings:

This selection is a simulation at a constant temperature which must be supplied by the user. The default value is 300 K.




# Muninn:

This type of Monte Carlo is a more advanced simulation, where the temperature is dynamically adjusted throughout the simulation in order to sample more efficiently from the interesting low energy regions of conformation space.

The user must supply an upper and a lower bound of the temperature. Further more, two sampling schemes are supported through the Muninn module, 1/k and multicanonical histogram weights.




# Simulated Annealing:

This type of Monte  Carlo is almost identical to Metropolis-Hastings, only the temperature is lowered at a constant rate from start to end of the simulation.




# Greedy Optimizaion:

This optimization technique will run a standard Monte Carlo simulation, where only structures with a lower energy than the previous structure are accepted each step.







# Monte Carlo Move Sets




# Backbone Moves:

There are three selections for  backbone moves step sizes; "small", "medium" and "large". All three are mixtures of semi-local, CRISP and DBN moves. Choosing "small" is ideal for things such as refinement and optimization, while "medium" is meant for *ab inito* folding, and "large" is if you want to sample large parts of conformational space quickly.

For the exact mixes, I suggest looking in the source code, as this may change. Note that if the PROFASI force field is selected, CRISP moves are replaced by semi-local moves, since PROFASI in its current implementation does not allow bond angles and lengths to vary (torsion angles are still degrees of freedom)









# Side Chain Moves:

The same logic as for backbone moves applies here. Only these moves are mixes of BASILISK, BASILISK-local and sidechain-uniform moves.

The sidechain-uniform moves are designed to escape salt-bridges more easily. The BASILISK type moves are able sample likely side chain angles depending on the backbone angles of the residue.




# Compensate for move bias:

The default mixes of backbone and side chain moves do not sample from all parts of conformational space with the same frequency. This is good if you want to find good structures quicker, but not that great if you want to model the average behavior of a protein.

It is possible to compensate for this "move bias" by adding an energy to your samples, depending on how likely they are to be proposed.







Press the "Save Config" button to save your config-file. Start phaistos with your settings by something like:




# phaistos --config-file my\_folding.config
















# Supported PHAISTOS versions:

Currently Guistos is only tested with PHAISTOS 1.0rc1 rev 324.