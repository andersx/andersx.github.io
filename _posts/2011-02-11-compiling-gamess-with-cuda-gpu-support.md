---
layout: post
title: Compiling GAMESS with CUDA (GPU support)
date: '2011-02-11T15:31:00.003+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2011-02-13T00:03:44.656+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-1998651932187766778
blogger\_orig\_url: https://combichem.blogspot.com/2011/02/compiling-gamess-with-cuda-gpu-support.html
---

As I mentioned in a previous post, much of the mathematics involved in quantum, chemistry can be formulated to be massively parallelized and implementation exists so you can run most types of calculations on hundreds or thousands of cores. I've heard people from Pacific Northwest National Laboratory running parallel coupled cluster CCSD calculations on 225,000 processors at 1.3 PFlops!

Very few people have access to hundreds or thousands of individual CPU cores at home or work, but most of us have access to hundreds of *cores* on the GPU that drives the computer graphics. So let's use our GPUs for massively parallel quantum computing, dawg![![](http://images.nvidia.com/products/geforce-gtx-560ti/geforce-gtx-560ti-top-med.png)](http://images.nvidia.com/products/geforce-gtx-560ti/geforce-gtx-560ti-top-med.png)

This post tells you how to compile the newest GAMESS (Oct 10, 2010, R1) so it runs RHF 2-electron integrals using an NVIDIA GPU. I will follow up with a post on the theory behind the GPU parallel 2-el integral routines and a post on GAMESS/GPU benchmark. I've used a lot of the pointers in the gamess/libqc/aaa.readme.1st file. Let me know if you found this helpful.

In order to do compile GAMESS with CUDA support (using this guide), there are a few requirements:

* I am going assume 64-bit Linux, because I only tried this on that platfrom.
* You need a copy of GAMESS from at least Oct 10, 2010. I'm further more going to assume that you already have properly installed said version of GAMESS and that your installation works. I'm assuming it's installed in  ~/gamess
* You need a NVIDIA graphics card which is CUDA enable (all newer NVIDIA cards are) with a working set of drivers.
* The GNU gcc and g++ compilers. I've only been able to make this work with 4.1 and 4.3, however. These are included the repositories of all major Linux releases. Other versions may work, but these are "guarateed" to work. Guaranteed, as in they potentially may compile the code. Get these via "sudo apt-get install gcc-4.3 g++-4.3", "yum install", etc. or the ditto of your distro.
* CUDA Toolkit version 2.3. You can download it [here](http://developer.nvidia.com/object/cuda_2_3_downloads.html#Linux). GAMESS does not work with the current version (which is 3.2, at the time of writing. Get the version that matches your Linux the most. I used the Ubuntu 9.10 version on a Ubuntu 10.04 LTS system and the RedHat Enterprise Linux 5.3 version on a CentOS 5.4 system - both worked just fine!
* A compiled and version of Boost 1.43. I tried some other versions, but they didn't work. 1.43 is "guaranteed" to work.
* You need an auxiliary code preprocessor named "cheetah" to do stuff to the C++ source code. I used the newest of 2.4.4.



I can't really help you with installing the NVIDIA drivers, but there are many guides floating around on the interwebs. It is likely that the one that came with your Linux works, but it may not. In that case, get a newer driver. The method to do this is highly OS dependent. Unfortunately, you can't use the open source nouveau nv driver - you need to run the real (and closed) McCoy from NVIDIA.

UPDATE: I suggest running the newest beta driver from NVIDIA, since it includes some CUDA specific improvements:


```
wget http://us.download.nvidia.com/XFree86/Linux-x86\_64/270.18/NVIDIA-Linux-x86\_64-270.18.run
```


**Step 1)** Installing and modifying Boost 1.43 from the source. First make a temporary directory and get Boost:
```
mkdir ~/temp
cd temp
wget http://downloads.sourceforge.net/project/boost/boost/1.43.0/boost\_1\_43\_0.tar.bz2
tar xjf boost\_1\_43\_0.tar.bz2
cd boost\_1\_43\_0/
```
Now, compile and install Boost. I always use ~/programs/ for my installation. Boost uses it's own configure and make scripts, and takes about 5-10 minutes to compile on my machine.
```
./bootstrap.sh --prefix=/home/andersx/programs/boost\_1\_43\_0
./bjam
./bjam install

```
nvcc (the CUDA compiler) requires three minor modifications of the Boost header files, in order to compile code linked to Boost.**Step 1.1)** Change /home/andersx/programs/boost\_1\_43\_0/include/boost/mpl/aux\_/integral\_wrapper.hpp line 59 from:
```
#if BOOST\_WORKAROUND(\_\_EDG\_VERSION\_\_, <= 243)
```
to
```
#if BOOST\_WORKAROUND(\_\_EDG\_VERSION\_\_, <= 243) || defined(\_\_CUDACC\_\_)
```
**Step 1.2)** Change /home/andersx/programs/boost\_1\_43\_0/include/boost/mpl/size\_t\_fwd.hpp around line 22, so that one original line is in an 'else' clause:
```
#if defined(\_\_CUDACC\_\_)
typedef std::size\_t std\_size\_t;
template< std\_size\_t N > struct size\_t;
#else
template< std::size\_t N > struct size\_t;
#endif
```
**Step 1.3)** Change /home/andersx/programs/boost\_1\_43\_0/include/boost/mpl/size\_t.hpp  around line 23, so that four original lines are in an else clause:

#if defined(\_\_CUDACC\_\_)

#define AUX\_WRAPPER\_VALUE\_TYPE std\_size\_t

#define AUX\_WRAPPER\_NAME size\_t

#define AUX\_WRAPPER\_PARAMS(N) std\_size\_t N

#else

//typedef std::size\_t std\_size\_t;

#define AUX\_WRAPPER\_VALUE\_TYPE std::size\_t

#define AUX\_WRAPPER\_NAME size\_t

#define AUX\_WRAPPER\_PARAMS(N) std::size\_t N

#endif

**Step 2)** Download and install Cheetah. This is pretty much straight forward:

cd ~/temp
```
wget http://pypi.python.org/packages/source/C/Cheetah/Cheetah-2.4.4.tar.gz
```
tar xvf Cheetah-2.4.4.tar.gzcd Cheetah-2.4.4/
python setup.py install

**Step 3)** Get the CUDA Toolkit v. 2.3:

You can follow [this](http://developer.nvidia.com/object/cuda_2_3_downloads.html#Linux) link and get the version which matches your Linux the closest. I used the Ubuntu 9.10 version on a Ubuntu 10.04.2.
```
cd ~/temp
```

```
wget http://developer.download.nvidia.com/compute/cuda/2\_3/toolkit/cudatoolkit\_2.3\_linux\_64\_ubuntu9.04.run
```
sh ./cudatoolkit\_2.3\_linux\_64\_ubuntu9.04.run

This installs the nvcc compiler. I used the default path which is /usr/local - you may of course put nvcc wherever you want. This can be useful, if you want the newest nvcc for another program and have different versions installed simultaneously.

You then need to add the following lines to your ~/.bashrc file in order to always be able to find the CUDA compiler and libraries:


```
export PATH=/usr/local/cuda/bin:$PATHexport LD\_LIBRARY\_PATH=/usr/local/cuda/lib64:$LD\_LIBRARY\_PATH
```
then write



source ~/.bashrc

or log out and back in to set the environment variables properly.

**Step 4)** You are now ready to compile the GPU Fock routines (called libqc). These are located in the libqc directory in the GAMESS directory. You may need to add a --mtune=native option. I found it needed with gcc-4.3, but using the option didn't work with gcc-4.1, YMMV. "native" tells your compiler to optimize the code for the local machine at compilation time.  cd ~/gamess/libqc

if you use gcc-4.3 and g++-4.3:CC=gcc-4.3 CXX=g++-4.3 CXXFLAGS='-O2 -DNDEBUG -msse3 -ffast-math -ftree-vectorize -mtune=native' ./configure --with-gamess --with-integer8 --with-boost=/home/andersx/programs/boost\_1\_43\_0 --prefix=/home/andersx/gamess/libqc

if you use gcc-4.1 and g++-4.1:CC=gcc-4.1 CXX=g++-4.1 CXXFLAGS='-O2 -DNDEBUG -msse3 -ffast-math -ftree-vectorize' ./configure --with-gamess --with-integer8 --with-boost=/home/andersx/programs/boost\_1\_43\_0 --prefix=/home/andersx/gamess/libqc

If the configure script ran without problems, you're now ready to compile libqc:

makemake install

This took my PC around 20 minutes. I thought it had crashed, but there was one file which just took 15 minutes to compile. Last thing to do in this step is making libqc visible to your compiler and GAMESS. Add the following line to ~/.bashrc and source it as in step 3.

export LD\_LIBRARY\_PATH=/home/andersx/gamess/libqc:$LD\_LIBRARY\_PATH

**Step 5)** Linking GAMESS to libqc:

In the ~/gamess/comp file, you need to set this flag to true:

GPUCODE=true

Now, recompile the rhfuhf GAMESS routine with libqc:

./comp rhfuhf

Finally, in the GAMESS linking script (~/gamess/lked) you need to link to libqc properly. Set the following flag to true:

GPUCODE=true

Then change the library pathnames at the second place the string 'GPUCODE' appears. The three paths must point to your libqc, CUDA, and Boost libraries.



Finally, you need to link a new executable. Type the following:

./lked gamess gpu

If linking was successful, there is now an executable named gamess.gpu.x.

Congratulations: You're now able to do 2-el integrals and Fock-matrix formation GPU parallel.