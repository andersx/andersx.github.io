---
layout: post
title: Compiling DALTON2011 with 64-bit integers, OpenMPI and MKL
date: '2011-10-29T10:59:00.004+02:00'
author: hellogetmyblogback
tags:
- dalton intel compile openmpi integers 64-bit integers 64 bit icc icpc ifort openmpi
openmpi-1.4.3 dirac
modified\_time: '2011-10-30T09:09:15.843+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-3200098717966050502
blogger\_orig\_url: https://combichem.blogspot.com/2011/10/compiling-dalton2011-with-64-bit.html
---

I was asked if I could do a blog post about how to compile DALTON2011 in parallel with 64-bit integers. This is not entirely trivial, unless you know what's going on. One of the key ingredients is an MPI that supports 64-bit integers, which OpenMPI does not do out of the box. I found 64-bit integers required to complete all test cases succesfully. This tutorial will assume that you have a working set of Intel compilers.

1) Get the OpenMPI 1.4.3, which is the latest stable version to support 64-bit integers:

wget http://www.open-mpi.org/software/ompi/v1.4/downloads/openmpi-1.4.3.tar.bz2tar xjf openmpi-1.4.3.tar.bz2cd openmpi-1.4.3/



2) Now you're ready to compile OpenMPI. I keep my compiled programs in ~/programs/ -- you can use whatever directory you want, so change the prefix to wherever you want it. If you have root access /opt/ would be a great place to put OpenMPI. First, export the CXX, CC, FC and F77 environment variables to specify that you want to use the dedicated Intel compilers. Then run the configure script with the -i8 flag, specifying support for 64-bit integers:

export CC=icc; export CXX=icpc; export FC=ifort; export F77=ifortFFLAGS=-i8 FCFLAGS=-i8 ./configure --prefix=/home/andersx/programs/openmpi-1.4.3/makemake install

(both configure and make can take quite a while)



3) Now, you need to export the following variables:

export PATH=/home/andersx/programs/openmpi-1.4.3/bin:$PATHexport LD\_LIBRARY\_PATH=/home/andersx/programs/openmpi-1.4.3/lib:$LD\_LIBRARY\_PATH

You can also add these lines to you ~/.bashrc file, but I wouldn't recommend this, since you might want another version of OpenMPI for another program, so it's better to only export the variables you need, when you actually need them.

Check that your compiled OpenMPI is working by typing:

which mpirun

This should return something like (in the folder you specified in the --prefix in step 2):

/home/andersx/programs/openmpi-1.4.3/bin/mpirun

Further more, check that your OpenMPI picked up the -i8 flag properly (the compiler flag that tells to compile for 64-bit integers):

ompi\_info -a | grep 'Fort integer size'

This must return size 8:

Fort integer size: 8

If not, you did something wrong, and the following steps will not work!



4) Go to <http://dirac.chem.sdu.dk/daltonprogram.org/www/download.html> and download the latest version. Untar it, and head into the DALTON2011 folder:

tar xjf Dalton2011\_release\_v0.tbzcd Dalton2011\_release/DALTON/

Start the configure script and answer the following questions with:

This appears to be a -linux architecture. Is this correct? [Y/n] yDo you want 64-bit integers? [y/N] yDo you want to install the program in a parallel MPI version? [Y/n] yCompiler /home/andersx/programs/openmpi-1.4.3/bin/mpif90 found, use this compiler? [Y/n] y(This must be found in the folder where you just compiled and installed OpenMPI in steps 1-3)Compiler /home/andersx/programs/openmpi-1.4.3/bin/mpicc found, use this compiler? [Y/n] y(This must be found in the folder where you just compiled and installed OpenMPI in steps 1-3)Do you want to replace this with your own directory search list? [y/N] n(Just install whatever BLAS/LAPACK the script finds .. we're going to use MKL, but we need to specify that manually later on, overriding what the script finds here)

The remaining questions you can answer however you want to.

5) Now we need to find out, where MKL is placed on you system. This may vary from version to version of the Intel Compiler suite. First write:

env | grep MKL

This returns something like:

MKLROOT=/opt/intel/composerxe-2011.0.084/mkl

If it doesn't, you haven't got the Intel Compilers (and MKL) properly installed. Now head into this directory and locate libmkl\_intel\_ilp64.a:

cd $MKLROOTlocate libmkl\_intel\_ilp64.a

This returns something like:

/opt/intel/composerxe-2011.0.084/mkl/lib/intel64/libmkl\_intel\_ilp64.a

The above directory we will call MKL\_LIB\_DIR:

export MKL\_LIB\_DIR=/opt/intel/composerxe-2011.0.084/mkl/lib/intel64

Last thing in this step is to edit the Makefile.config and put the information we now have into it. Open Makefile.config with your favourite editor and replace the line that starts with "LIBS = " with:

LIBS = -L/usr/lib -lmkl\_lapack95\_ilp64 -Wl,--start-group $(MKL\_LIB\_DIR)/libmkl\_intel\_ilp64.a $(MKL\_LIB\_DIR)/libmkl\_sequential.a $(MKL\_LIB\_DIR)/libmkl\_core.a -Wl,--end-group -lpthread(the above is all in *one* line)

This will link to the correct MKL library, which supports 64-bit integers (also why they're called ilp64) and made for the Intel compilers. Now it's a good time to make sure that you still have the environment variables from step 3) exported. So now we're ready to actually compile DALTON2011:

make

(now it's time to get a coffee, and do something else for a *long* while ... Also compiling DALTON2011 does not seem to work in parallel so no -j8 flags here!)

6) Compiling ends without errors and the following line:

mv ./dalton\_mpi.x /home/andersx/zips/Dalton2011\_release/bin/dalton\_mpi.x

To test your compilation head into the test/ folder and start testing the parallel (these only take a minute or so .. the full test suite takes much longer).

cd test/./TEST parallel

Hopefully this returns:

#####################################################################

Summary

#####################################################################



ALL TESTS ENDED PROPERLY!



date and time         : Sat Oct 29 10:54:20 CEST 2011If not, something is wrong. :) Now proceed to the full test suite. I use nohup and just check the output in nohup.out.

nohup ./TEST all &less nohup.out

Hopefully nohup.out end with (after an hour or so):

All tests completed successfully

Perl-style tests computed successfully



Perl-style tests finished!



#####################################################################

Summary

#####################################################################



ALL TESTS ENDED PROPERLY!



date and time         : Sat Oct  29 13:02:34 CEST 2011

This means DALTON2011 works!



You can also head over to the DIRAC10 wiki pages, which is very much like compiling DALTON: <http://wiki.chem.vu.nl/dirac/index.php/Installation_guide>



For support go to: <http://dirac.chem.sdu.dk/daltonprogram.org/www/docs.html>

I did this on a Intel Core i5-760 with Ubuntu 10.04.3 Server and Intel Compilers version 12.0.0 20101006. The entire test suite took twice as long compiling with GNU compilers, so I recommend staying away from these for a "production" version. Further more MKL is one of the fastest and most stable BLAS and LAPACK libraries there is and it comes precompiled for 64-bit integers with the Intel Compilers, so do use MKL and save yourself a headache! Also GNU compilers >4.4 have compiler issues from what I've heard.

