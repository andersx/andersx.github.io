---
layout: post
title: "Compiling DALTON2011 with 64-bit integers, OpenMPI and MKL"
date: 2011-10-29 10:54:20 +0200
categories: [DALTON, compiling]
tags: [dalton2011, openmpi, mkl, intel, parallel]
lang: en
---

Saturday, October 29, 2011  
**Compiling DALTON2011 with 64-bit integers, OpenMPI and MKL**

I was asked if I could do a blog post about how to compile DALTON2011 in parallel with 64-bit integers. This is not entirely trivial, unless you know what's going on. One of the key ingredients is an MPI that supports 64-bit integers, which OpenMPI does not do out of the box. I found 64-bit integers required to complete all test cases succesfully. This tutorial will assume that you have a working set of Intel compilers.

---

### 1) Get OpenMPI 1.4.3

```bash
wget http://www.open-mpi.org/software/ompi/v1.4/downloads/openmpi-1.4.3.tar.bz2
tar xjf openmpi-1.4.3.tar.bz2
cd openmpi-1.4.3/
```

---

### 2) Compile OpenMPI with 64-bit integer support

```bash
export CC=icc; export CXX=icpc; export FC=ifort; export F77=ifort
FFLAGS=-i8 FCFLAGS=-i8 ./configure --prefix=/home/andersx/programs/openmpi-1.4.3/
make
make install
```

*(both configure and make can take quite a while)*

---

### 3) Export variables

```bash
export PATH=/home/andersx/programs/openmpi-1.4.3/bin:$PATH
export LD_LIBRARY_PATH=/home/andersx/programs/openmpi-1.4.3/lib:$LD_LIBRARY_PATH
```

You can also add these lines to your `~/.bashrc` file, but I wouldn't recommend this, since you might want another version of OpenMPI for another program.

Check OpenMPI:

```bash
which mpirun
```

Should return:

```
/home/andersx/programs/openmpi-1.4.3/bin/mpirun
```

Check 64-bit integer support:

```bash
ompi_info -a | grep 'Fort integer size'
```

Should return:

```
Fort integer size: 8
```

If not, you did something wrong, and the following steps will not work!

---

### 4) Download DALTON2011

Go to [http://dirac.chem.sdu.dk/daltonprogram.org/www/download.html](http://dirac.chem.sdu.dk/daltonprogram.org/www/download.html) and get the latest version.

```bash
tar xjf Dalton2011_release_v0.tbz
cd Dalton2011_release/DALTON/
```

Run the configure script and answer:

```
This appears to be a -linux architecture. Is this correct? [Y/n] y
Do you want 64-bit integers? [y/N] y
Do you want to install the program in a parallel MPI version? [Y/n] y
Compiler /home/andersx/programs/openmpi-1.4.3/bin/mpif90 found, use this compiler? [Y/n] y
Compiler /home/andersx/programs/openmpi-1.4.3/bin/mpicc found, use this compiler? [Y/n] y
Do you want to replace this with your own directory search list? [y/N] n
```

The remaining questions you can answer however you want.

---

### 5) Find MKL path and configure Makefile

```bash
env | grep MKL
```

Example:

```
MKLROOT=/opt/intel/composerxe-2011.0.084/mkl
```

Then:

```bash
cd $MKLROOT
locate libmkl_intel_ilp64.a
```

Example result:

```
/opt/intel/composerxe-2011.0.084/mkl/lib/intel64/libmkl_intel_ilp64.a
```

Set variable:

```bash
export MKL_LIB_DIR=/opt/intel/composerxe-2011.0.084/mkl/lib/intel64
```

Then open `Makefile.config` and replace the line starting with `LIBS =` with:

```make
LIBS = -L/usr/lib -lmkl_lapack95_ilp64 -Wl,--start-group $(MKL_LIB_DIR)/libmkl_intel_ilp64.a $(MKL_LIB_DIR)/libmkl_sequential.a $(MKL_LIB_DIR)/libmkl_core.a -Wl,--end-group -lpthread
```

Make sure step 3's environment variables are still active.

Now compile DALTON:

```bash
make
```

---

### 6) Test the build

Compilation ends with:

```
mv ./dalton_mpi.x /home/andersx/zips/Dalton2011_release/bin/dalton_mpi.x
```

Then:

```bash
cd test/
./TEST parallel
```

Expected output:

```
#####################################################################
                              Summary
#####################################################################

ALL TESTS ENDED PROPERLY!

date and time         : Sat Oct 29 10:54:20 CEST 2011
```

Run full suite:

```bash
nohup ./TEST all &
less nohup.out
```

Final message should be:

```
All tests completed successfully
Perl-style tests computed successfully

Perl-style tests finished!

#####################################################################
                              Summary
#####################################################################

ALL TESTS ENDED PROPERLY!

date and time         : Sat Oct  29 13:02:34 CEST 2011
```

This means DALTON2011 works!

---

You can also head over to the DIRAC10 wiki pages, which is very much like compiling DALTON:  
👉 [http://wiki.chem.vu.nl/dirac/index.php/Installation_guide](http://wiki.chem.vu.nl/dirac/index.php/Installation_guide)

For support go to:  
👉 [http://dirac.chem.sdu.dk/daltonprogram.org/www/docs.html](http://dirac.chem.sdu.dk/daltonprogram.org/www/docs.html)

I did this on an Intel Core i5-760 with Ubuntu 10.04.3 Server and Intel Compilers version 12.0.0 20101006. The entire test suite took twice as long compiling with GNU compilers, so I recommend staying away from these for a "production" version. Further more MKL is one of the fastest and most stable BLAS and LAPACK libraries there is and it comes precompiled for 64-bit integers with the Intel Compilers, so do use MKL and save yourself a headache! Also GNU compilers >4.4 have compiler issues from what I've heard.
