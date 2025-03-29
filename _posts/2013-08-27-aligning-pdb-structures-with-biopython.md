---
layout: post
title: Aligning PDB structures with Biopython
date: '2013-08-27T17:25:00.000+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-08-27T17:25:22.503+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-2479076619807297891
blogger\_orig\_url: https://combichem.blogspot.com/2013/08/aligning-pdb-structures-with-biopython.html
---

You can use the Bio.PDB module in Biopython to align PDB files. This is how I did it. The code should be pretty much self-explanatory.



In this example I align the crystal structure of Ubiquitin (PDB code: 1UBQ) to the first structure of a corresponding NMR ensemble (PDB code: 1D3Z, see picture below).


[![](http://www.rcsb.org/pdb/images/1D3Z_bio_r_250.jpg?bioNum=1)](http://www.rcsb.org/pdb/images/1D3Z_bio_r_250.jpg?bioNum=1)




