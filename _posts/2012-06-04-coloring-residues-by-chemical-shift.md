---
layout: post
title: Coloring residues by chemical shift errors
date: '2012-06-04T15:46:00.004+02:00'
author: hellogetmyblogback
tags:
modified\_time: '2012-06-04T18:17:01.117+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-7680689656358074171
blogger\_orig\_url: https://combichem.blogspot.com/2012/06/coloring-residues-by-chemical-shift.html
---






I wrote a quick script to replace the b-factor column in my .pdb files with the error in C-alpha chemical shifts (between experimental values and CamShift prediction of that same structure). This was the easy part.

At first PyMol would color the ensemble snapshots with the b-factor values of the first structure. The trick was to load the ensemble object with **discrete=1**, which loads an individual set b-factors for each structure. The last thing to be adjusted are the **minimum** and **maximum** values for spectrum-coloring.



```
reinitialize
import glob

def load\_structure():

native\_pdb\_file = "/home/andersx/color/1PGB.pdb"

for x in glob.glob("/home/andersx/color/1PGB\_opls\_colorensemble/col\_sample\_*.pdb"):
cmd.load(x, "ensemble",discrete=1)

cmd.load(native\_pdb\_file, "native")

cmd.align("ensemble", "native")
cmd.hide("all")
cmd.center("all")
cmd.h\_add

cmd.show("sticks", "ensemble")
cmd.hide("sticks", "ensemble &! n. n+ca+c+o+h")

cmd.show("lines", "native")
cmd.hide("lines", "native &! n. n+ca+c+o+h02")

cmd.spectrum( "b", "blue\_red", minimum=0, maximum=8)

cmd.color("grey", "native")

cmd.bg\_color("white")


load\_structure()


```






The final result looks something like this:








