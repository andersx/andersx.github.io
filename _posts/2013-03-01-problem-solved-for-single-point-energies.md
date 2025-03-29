---
layout: post
title: Problem solved? (for single point energies)
date: '2013-03-01T11:11:00.004+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-03-01T18:06:05.743+01:00'
thumbnail: https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj812lvujBvw3Y4w1EuFVrN3qFLlK9bIO5AsTLm5wdgGoRz\_16LZN0XliebcL60iUjxy9dk7HELS0HLDitrT8gTBYrTlPUq2581sTO\_bioDFRWG-wnGNGofF5dNugWR95M\_Y9iNvJ1lkCwg/s72-c/r15fd3\_000.jpg
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-3902736891490586234
blogger\_orig\_url: https://combichem.blogspot.com/2013/03/problem-solved-for-single-point-energies.html
---


I am looking at the conversion of chorismate to prephenate in Chorismate Mutase, to benchmark a hacky EFMO-RHF:MP2 method we've implemented in our group. Casper Steinmann did an adiabatic mapping of the reaction path, and we though it'd be cool to do coupled cluster single point energies on snapshots from the reaction path and some something ONIOM style.







[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj812lvujBvw3Y4w1EuFVrN3qFLlK9bIO5AsTLm5wdgGoRz_16LZN0XliebcL60iUjxy9dk7HELS0HLDitrT8gTBYrTlPUq2581sTO_bioDFRWG-wnGNGofF5dNugWR95M_Y9iNvJ1lkCwg/s320/r15fd3_000.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj812lvujBvw3Y4w1EuFVrN3qFLlK9bIO5AsTLm5wdgGoRz_16LZN0XliebcL60iUjxy9dk7HELS0HLDitrT8gTBYrTlPUq2581sTO_bioDFRWG-wnGNGofF5dNugWR95M_Y9iNvJ1lkCwg/s1600/r15fd3_000.jpg)

**Figure 1:** Snapshot 1 from the reaction path; chorismate to prephanate







Anyways, the reaction complex contains 118 electrons, so we fired up MOLPRO2012 and started looking at the DF-LCCSD(T0)-F12a method. A name you should probably memorize from now on.




The single point energy evaluation took 3:43 hours with the VDZ-F12 basis set! Mind you, this was on only a single CPU core with 4 GB 16GB of RAM allocated, so no supercomputers were involved.



First I though the calculation had crashed because it didn't show up in the queue. My first attempt with the 3-21G basis set didn't converge because I failed to set the charge to -2, but this time around Molpro actually gave me an energy!

Next problem on my list is to calculate the good old MP2/cc-pVTZ energy I need to subtract in order to get my ONIOM results. Those are going to take about 5-10 hours on 8 cores.

This is what the input file looked like for anyone interested

>
> memory,2000,M
> geomtyp=xyz
> geometry
> 24
> NS=  27 NA= 105 NF=   9 EFMO2-RHF:MP2 (this line is just a title btw)
> C      42.243912717439      56.985332654358      33.508219032291
> O      44.083656765173      58.469818126062      33.547456767374
> C      42.170657938422      56.969234127412      32.053802633762
> O      43.705108088223      57.205919113255      35.368497584787
> C      41.129420410826      56.416380137206      31.405612518418
> O      36.855437867615      57.552564896356      32.240095659821
> C      40.011578869479      55.731334232825      32.133332774369
> O      37.952282803211      59.164070642440      31.158486994867
> C      39.926225853593      56.110094283828      33.626395940165
> O      40.227454849874      54.351343596304      31.951217705746
> C      41.196604656028      56.578036178752      34.246569733982
> O      38.910804760126      57.082428023235      33.866893577924
> C      38.968148927395      58.213925767965      33.092045796028
> C      39.831866096203      59.221137407117      33.269727518172
> C      43.428858040104      57.602185825538      34.190487998445
> C      37.845543926101      58.319486314173      32.113613710711
> H      39.478451838017      53.847769952241      32.300919500400
> H      39.060609728200      56.030078213449      31.676260590468
> H      39.584331039092      55.233181854460      34.161137739114
> H      41.108061134298      56.387696261961      30.326013340305
> H      42.988978401897      57.425894863516      31.511012407792
> H      41.233442365036      56.630502649395      35.323808064193
> H      40.574407364526      59.218144574703      34.050005215454
> H      39.764972282572      60.084246967681      32.627123054496
> end
>
> set,charge=-2
> set,spin=0
> basis={
> C=vdz-f12
> H=vdz-f12
> O=vdz-f12
> }
> DF-HF,DF\_BASIS=vdz-f12
> DF-LCCSD(T)-F12,DF\_BASIS=vdz-f12










# Further reading:






Thomas B. Adler, Hans-Joachim Werner (2011) "An explicitly correlated local coupled cluster method for calculations of large molecules close to the basis set limit" J. Chem. Phys. **135**, 144117; [http://dx.doi.org/10.1063/1.3647565](http://link.aip.org/link/doi/10.1063/1.3647565)




Molpro manual entry: <http://www.molpro.net/info/2012.1/doc/update/node10.html>


