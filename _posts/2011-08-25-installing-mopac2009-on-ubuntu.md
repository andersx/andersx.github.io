---
layout: post
title: "Installing MOPAC2009 on Ubuntu 10.04 LTS - Lucid Lynx (And keep source codes open!)"
date: 2011-08-25
author: hellogetmyblogback
tags:
  - mopac
  - MOPAC2009
  - MOPAC2009.exe
  - Ubuntu 10.04 LTS
  - no such file or directory
---

## Installing MOPAC2009 on Ubuntu 10.04 LTS

If you try and install [MOPAC2009](http://openmopac.net/MOPAC2009.html) on a standard [Ubuntu 10.04 LTS](http://www.ubuntu.com/business/desktop/long-term-support) system, you will get a very weird problem. I unzipped MOPAC2009 in `/opt/mopac` as described in the installation guide. However, when I tried to run the executable I got a very odd message:

```
-bash: ./MOPAC2009.exe: No such file or directory
```

How odd is that? I could see the executable with `ls` and I even tabbed my way to the full filename.

Turns out, the `MOPAC2009.exe` executable is compiled as 32-bit, and not 64-bit as you'd expect in 2011. And this is actually where the problem lies — not a missing file!

## The Fix

You need to install a set of 32-bit compatible libraries that will let 32-bit code run. Since we're running Ubuntu, this is of course easy as pie:

```bash
sudo apt-get install ia32-libs
```

But very odd, that I get a complaint saying `"No such file or directory"`! Really! I was going nuts and had started blaming the NFS file system. A big thanks to my favorite website [ubuntuforums.org](http://ubuntuforums.org/), where I found other possible causes for missing files, other than actual missing files.

![Ubuntu Forums Logo](http://ubuntuforums.org/images/misc/ubuntulogo.png)

---

## Why Source Code Matters

The take home message of my post here really is: **Start distributing source codes!** I'm sure it's possible for users to compile Mopac (written in Fortran 90/95), if a decent Makefile is included.

Some reasons binaries are problematic:
1. You will never discover bugs from binaries.
2. You will never know what exact setting your algorithms are using from binaries.

What if you needed to slightly tweak a parameter somewhere? What if you get unexpected results and you know the input is good? You should of course turn to the source code!

I have myself found bugs/quirks in both [Dalton](http://dirac.chem.sdu.dk/daltonprogram.org/) and [GAMESS](http://www.msg.ameslab.gov/gamess/).

- I had a Dalton calculation that kept crashing, and found a coefficient which was mis-typed. I quickly contacted the authors and things were solved **the same day**, and the fix lives on in the new DALTON2011!
- In GAMESS (and this is an even better story!) I discovered how to have semi-empirical methods run in parallel after snooping around in the code for a couple of days, looking for the Fock-matrix diagonalization routines. Turns out, the only thing that prevents parallel semi-empirical methods is a flag in the code — remove that and voilà!

Unfortunately, cheerful stories like these will never happen to programs such as MOPAC. And I'm willing to bet money that Mr. MOPAC would really prefer to receive a bug report **along with a code patch**, rather than just the bug reports.

MOPAC2009 is even free for academic use, and Jimmy Stewart (aka Mr. MOPAC) has even been helpful in getting us started with a GAMESS implementation of his newest PM6 method. Thanks Jimmy!

---

## ⚠️ Update: File Permissions

Another thing concerning installation of MOPAC2009: the installation guide recommends using `777` permission for the installation.

This is **dangerous**, and means that **everyone has write permissions** to that folder. Stick with `755`, which only gives the owner write permission — otherwise that file is an easy target for a hacker.

> **777 = bad, unless really necessary!**
