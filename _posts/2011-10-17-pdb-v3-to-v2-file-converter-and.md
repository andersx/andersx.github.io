---
layout: post
title: PDB V3 to V2 file converter and CamShift 1.35 notes
date: '2011-10-17T13:38:00.002+02:00'
author: hellogetmyblogback
tags:
- PDB naming convention v2 v3 converter openbabel python camshift chemical shift
modified\_time: '2011-10-17T13:38:56.330+02:00'
thumbnail: https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1ialok0bu8km44Ug2NkrAlzKm4tioln5Eu6xXuwIuGbkJcwkukhpf3SxYPWEHmFc\_0kBLxMW0t99uUOXOmcuTledDWyDDznDPQEH8ZFVS6KlGr5BZXAUmzvsaSvcHeDpEVOgg\_AarCqyQ/s72-c/Download-icon.png
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-562252053967161673
blogger\_orig\_url: https://combichem.blogspot.com/2011/10/pdb-v3-to-v2-file-converter-and.html
---



I stubmled upon a program called CamShift 1.35, which predicts chemical shifts from a coordinates in a PDB file. However, everytime I tried to supply it with a seemingly  standard PDB file, adhereing to every PDB naming convention I could find, CamShift 1.35 would complain about missing atoms and used an internal force-field to add hydrogens and whatnot at coordinates that were not correspoding to the coordinates I had supplied.A little trick I found, was to uncomment the lines 278-279 in the camshift-1.35/bin/camshift.cpp file:

//  for(int r=0;r<rep.size();r++)

//    cout<<"\t"<<p.atom\_name(rep[r],Almost::Protein::BASE)<<"\n";

and the recompile. This will add the names of all atoms that are added. Please note, that protonation states are adjusted entirely by CamShift, so even if you give it a proper PDB file you will have things added, such as H-gamma on cysteine residues, even despite of actual cys-bridges in the supplied PDB file.

At any rate, by doing the above, I discovered that the CamShift 1.35 code uses the old PDB v2 naming convention (which was deprecated during the last millennium). Sadly I couldn't find any file converter that would let convert a standard (currently PDB v3) PDB file to an older v2 type. Not even my all-time favourite file-converter OpenBabel was capable of this. Long story short, I found the proper naming conversions and put it in a short python script.

Usage is like this:



$ python pdb\_v3\_to\_v2.py myfile.pdb



This will print the new file to standard out put. If you want it in a new file, simply use the awesomeness of shell:



$ python pdb\_v3\_to\_v2.py myfile.pdb > outfile.pdb



Since it's written in standard Python, it will work on any platform (Windows, UNIX, Linux, MacOS, etc) and you need not worry about compiling, just a working Python interpreter is enough.





Have fun converting. Shoot me a message, if you find any bugs or quirks. Download the program here:





[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1ialok0bu8km44Ug2NkrAlzKm4tioln5Eu6xXuwIuGbkJcwkukhpf3SxYPWEHmFc_0kBLxMW0t99uUOXOmcuTledDWyDDznDPQEH8ZFVS6KlGr5BZXAUmzvsaSvcHeDpEVOgg_AarCqyQ/s320/Download-icon.png)](http://dl.dropbox.com/u/17435887/pdb_v3_to_v2.py)<http://dl.dropbox.com/u/17435887/pdb_v3_to_v2.py>(click link or picture to download)