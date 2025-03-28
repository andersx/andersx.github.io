---
layout: post
title: "HOWTO: RM1 in Gaussian09"
date: 2010-12-30 22:00:00 +0100
categories: [Gaussian, RM1]
tags: [gaussian09, semiempirical, rm1, qm, chemistry]
lang: en
---

Thursday, December 30, 2010  
**HOWTO: RM1 in Gaussian09**

In my group we regularly use semi-empirical QM methods when we need to minimize large structures. Lately we've had some good results with Jimmy Stewart's PM6 method (he also go by the, IMO, cool name of Mr. MOPAC). Recently, the 25 year old AM1 model of Dewar et al. has been reparametrized, by the group of Mr. MOPAC, into the RM1 method. Supposedly RM1 beat the \[euphemism\] out of older parameterizations, such as AM1, PM3 and PM5.

In short: Right now the big players in the field of semi-empirical methods seem to be the well-known PM6 and newcomer RM1. The approximations in the two are very alike with the most notable difference between the two, probably being the introduction of d-orbitals on main group elements in PM6, which are not present in AM1 and it's derivative reparametrizations.

I'm not aware of any program which does RM1 in parallel out-of-the-box. If you do know of any open source or free program capable of this, do leave a comment here! I know that Gaussian 09 does PM6 and AM1 and will let you run on multiple cores too. The down side is that I don't have a clue of how much speed up you get from running semi-empirical calculations in parallel in Gaussian09. From my experience, I've seen good scaling with parallel DFT calculations in Gaussian, but I've never really tested the semi-empirical methods in parallel this way. Step one is of course to get RM1 up and running in Gaussian. This proved to be a near impossible task.

Here I present how to make Gaussian09 do RM1 calculations. This is something I've been trying to figure out for about a week now, and solely using the information found on the RM1 website combined with the sub-par Gaussian09 manual, I've had little luck, until I contacted Mahmoud A. A. Ibrahim, who kindly supplied me with a g09 input-file. These things should be really simple, but when the documentation is erroneous and incomplete, there is not much to do other than old fashioned trial-and-error and guesswork. However, since I felt that my time was more valuable than this,  I more or less gave up until I five minutes later got a reply. So big thanks to Ibrahim for having sorted this out! Below is an outline of  how it is done. I hope the Gaussian-staff will include this in the manual, because it is by no means straightforward to see how and where to get the parameters and the correct names and units. For instance, the GCore parameters are not mentioned in the manual, so it can be hard to specify these correctly, and I believe that some of the entries in the MOPAC to Gaussian parameter table are erroneously paired up.

---

```g09
============ Begin file ============
# opt AM1=(Input,Print,NoGenerate)

Title

0 1
 C                  5.52110000    2.02080000   -0.14150000
... rest of molecule ...
 H                  2.51520000   -7.31660000    0.02560000

 Method=8 CoreType=1
 ****
 H       
 PQN=1 NValence=1 F0ss=0.5138736446 ZetaOverlap=1.0826737000 U=-0.4395468110
 Beta=-0.2118762033 CoreKO=0.9730018200 KON=0,0,0,0.9730018200 KON=1,0,1,1.0589197790
 KON=0,1,1,0.9730018200 KON=2,1,1,1.0589197790 EISol=-0.4395468110 EHeat=0.0830298228
 Alpha=1.623706039
 GCore=0.0071452321,1.6526506619,2.2204505049
 GCore=0.0044844511,1.7971829010,3.6631297957
 GCore=-0.0024774154,0.7854047496,3.0926358381
 ****
 C       
 PQN=2,2 NValence=4 F0ss=0.4796935160 F0sp=0.4165460293 F0pp=0.3723813969 F2pp=0.1879094681
 G1sp=0.1711215396
 ZetaOverlap=1.8501880000,1.7683009000
 U=-1.9008794468,-1.4481913012
 Beta=-0.5681197391,-0.3026706191 DDN=0,1,0.7967571300 DDN=1,1,0.6926111205
 CoreKO=1.0423321900 KON=0,0,0,1.0423321900 KON=1,0,1,0.9808908700 KON=0,1,1,1.0423321900
 KON=2,1,1,0.7558858400 EISol=-4.3315453857 EHeat=0.2723305520 Alpha=1.477897228
 DipHyp=1.5934225837
 GCore=0.0051822600,1.6071441515,1.9728170130
 GCore=0.0008174160,1.9389223038,3.1399608166
 GCore=0.0025838555,1.7534236006,3.0832529699
 GCore=-0.0001879630,2.5202671079,5.2828786928
 ****
 N       
 PQN=2,2 NValence=5 F0ss=0.4809517358 F0sp=0.4855419470 F0pp=0.4603627461 F2pp=0.2692199995
 G1sp=0.5512408181
 ZetaOverlap=2.3744716000,1.9781257000
 U=-2.6037351707,-2.1306270015
 Beta=-0.7670041923,-0.6126744081 DDN=0,1,0.6495619700 DDN=1,1,0.6191441047
 CoreKO=1.0396053400 KON=0,0,0,1.0396053400 KON=1,0,1,0.5151439200 KON=0,1,1,1.0396053400
 KON=2,1,1,0.6231658500 EISol=-7.5368325074 EHeat=0.1800769640 Alpha=1.568600643
 DipHyp=1.2990492003
 GCore=0.0042177292,1.2850311275,2.6054387408
 GCore=0.0016934863,1.2957774179,3.9376355712
 GCore=-0.0015857545,0.5748275884,3.5293247133
 ****
 O       
 PQN=2,2 NValence=6 F0ss=0.5145797792 F0sp=0.5496321127 F0pp=0.4844989583 F2pp=0.2207863333
 G1sp=0.4335139609
 ZetaOverlap=3.1793691000,2.5536191000
 U=-3.5628280133,-2.8624391247
 Beta=-1.0970045571,-1.0712800661 DDN=0,1,0.4886700600 DDN=1,1,0.4796114166
 CoreKO=0.9716666100 KON=0,0,0,0.9716666100 KON=1,0,1,0.4967410600 KON=0,1,1,0.9716666100
 KON=2,1,1,0.5584555600 EISol=-11.4672725099 EHeat=0.0949133088 Alpha=2.207710126
 DipHyp=0.9772838928
 GCore=0.0160375838,1.4612692876,1.7076238079
 GCore=0.0040694547,2.0804240743,2.8677465230
 ****
 F       
 PQN=2,2 NValence=7 F0ss=0.6144822801 F0sp=0.6159711092 F0pp=0.5507178433 F2pp=0.0551275865
 G1sp=0.2202381595
 ZetaOverlap=4.4033791000,2.6484156000
 U=-4.9311603036,-3.9632901345
 Beta=-2.5724529652,-1.2009616000 DDN=0,1,0.3488927400 DDN=1,1,0.4624443630
 CoreKO=0.8136931000 KON=0,0,0,0.8136931000 KON=1,0,1,0.5419872400 KON=0,1,1,0.8136931000
 KON=2,1,1,0.8017335300 EISol=-17.8085651426 EHeat=0.0301031314 Alpha=3.175063812
 DipHyp=0.6977453358
 GCore=0.0279882125,2.0174429443,1.5430182683
 GCore=0.0049208369,2.5202610313,2.7174711546
 ****
 P       
 PQN=3,3 NValence=5 F0ss=0.4072043031 F0sp=0.2088608220 F0pp=0.2745110810 F2pp=0.0308577654
 G1sp=0.1280880722
 ZetaOverlap=2.1224012000,1.7432795000,1.0000000000
 U=-1.5366852349,-1.2635676846
 Beta=-0.2254626127,-0.2184534727 DDN=0,1,1.0106954700 DDN=1,1,0.9598690581
 CoreKO=1.2278848400 KON=0,0,0,1.2278848400 KON=1,0,1,1.2715722400 KON=0,1,1,1.2278848400
 KON=2,1,1,1.6008006600 EISol=-4.5267737772 EHeat=0.1204284617 Alpha=1.010693038
 DipHyp=2.0212746476
 GCore=-0.0285170033,1.7046815287,2.4878293672
 GCore=-0.0113192311,1.9867256080,3.6041106250
 GCore=-0.0033939241,2.5201987249,5.0239839450
 ****
 S       
 PQN=3,3 NValence=6 F0ss=0.4589360160 F0sp=0.3149088537 F0pp=0.2922830364 F2pp=0.1308243369
 G1sp=0.4288413981
 ZetaOverlap=2.1334431000,1.8746065000,1.0000000000
 U=-2.0273776403,-1.7099205406
 Beta=-0.0719958680,-0.3224498447 DDN=0,1,0.9936920700 DDN=1,1,0.8926246952
 CoreKO=1.0894764700 KON=0,0,0,1.0894764700 KON=1,0,1,0.7214224200 KON=0,1,1,1.0894764700
 KON=2,1,1,1.0081994600 EISol=-6.8128155113 EHeat=0.1058151363 Alpha=1.291275251
 DipHyp=1.9872698041
 GCore=-0.0518075719,1.3470435829,1.1221218344
 GCore=-0.0045273966,2.0183359552,2.4470443530
 GCore=-0.0004555529,2.5202571669,3.4026437095
 ****
 Cl      
 PQN=3,3 NValence=7 F0ss=0.5644781272 F0sp=0.4890126782 F0pp=0.3906816863 F2pp=0.4442159843
 G1sp=0.1945765429
 ZetaOverlap=3.8649107000,1.8959314000,1.0000000000
 U=-4.3538053708,-2.8059323918
 Beta=-0.7322047420,-0.4236959083 DDN=0,1,0.4541788400 DDN=1,1,0.8825847052
 CoreKO=0.8857739000 KON=0,0,0,0.8857739000 KON=1,0,1,0.6675936500 KON=0,1,1,0.8857739000
 KON=2,1,1,0.6487473400 EISol=-14.0555179538 EHeat=0.0461985061 Alpha=1.954562896
 DipHyp=0.9083054214
 GCore=0.0089912708,0.8337132813,2.7731689426
 GCore=0.0002006300,1.9877196813,4.7243667328
 ****
 Br      
 PQN=4,4 NValence=7 F0ss=0.6289878820 F0sp=0.5741785343 F0pp=0.3485867906 F2pp=0.2870889303
 G1sp=0.2464182944
 ZetaOverlap=5.7315721000,2.0314758000,1.0000000000
 U=-4.1704597746,-2.7998282113
 Beta=-0.0492954863,-0.3014275181 DDN=0,1,0.2099005300 DDN=1,1,1.0442262465
 CoreKO=0.7949278600 KON=0,0,0,0.7949278600 KON=1,0,1,0.3797804400 KON=0,1,1,0.7949278600
 KON=2,1,1,0.8527529400 EISol=-13.1237877769 EHeat=0.0426129028 Alpha=1.517206895
 DipHyp=0.4197769085
 GCore=0.0685363742,1.1998779275,3.7798245418
 GCore=-0.0643982928,1.2713460218,3.8100223654
 ****
 I       
 PQN=5,5 NValence=7 F0ss=0.7349770009 F0sp=0.2825867563 F0pp=0.2574091259 F2pp=0.0690025699
 G1sp=0.1561143756
 ZetaOverlap=2.5300375000,2.3173868000,1.0000000000
 U=-2.7525236785,-1.8892915650
 Beta=-0.1540958564,-0.1617111472 DDN=0,1,1.2963425100 DDN=1,1,1.1085963335
 CoreKO=0.6802933800 KON=0,0,0,0.6802933800 KON=1,0,1,1.3450521300 KON=0,1,1,0.6802933800
 KON=2,1,1,1.4234289100 EISol=-9.1319620411 EHeat=0.0406639282 Alpha=1.133270597
 DipHyp=2.5925358606
 GCore=-0.0056582787,0.4370267028,3.7794911941
 GCore=0.0041077335,1.6132758519,4.1666344737
 ****


============= End file =============
```

---

Do remember, that Gaussian is ultra picky about blank lines, to be sure to have exactly one blank line between sections and your file must be terminated with two or more blank lines.

A short explanation is in order. The method is specified as AM1, because AM1 and RM1 use identical formulas. The Input keyword tells AM1 to read the parameters from the input stream (i.e. the stuff in the bottom part of the file). I'm including the Print and NoGenerate keywords as a safety check. Print tells Gaussian to print the used parameters in the output file. This way we're sure, that our optimization used the right keywords. NoGenerate tells Gaussian to not use the native AM1 parameters if a parameter is missing in the input stream. This way we're likely to get some sort of failure in the calculation if our parameters are not read in correctly.

For those who worry about too much clutter in their input files, it is of course safe to leave out the parameters for elements which are not present in the molecule.

---

**EDIT**: To honor the fact, that this website promotes free software, let me point out, that the RM1 method is public domain and the parameters are fully disclosed. It is also included in the free quantum chemistry programs **GAMESS** and **MOPAC**. GAMESS is free, while MOPAC is free for academic or non-profit purposes.
