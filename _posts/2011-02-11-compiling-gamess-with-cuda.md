---
layout: post
title: "Compiling GAMESS with CUDA (GPU support)"
date: 2011-02-11 23:00:00 +0100
categories: [GAMESS, CUDA]
tags: [gamess, cuda, compiling, gpu]
lang: en
---

Friday, February 11, 2011  
**Compiling GAMESS with CUDA (GPU support)**

As I mentioned in a previous post, much of the mathematics involved in quantum, chemistry can be formulated to be massively parallelized and implementation exists so you can run most types of calculations on hundreds or thousands of cores. I've heard people from Pacific Northwest National Laboratory running parallel coupled cluster CCSD calculations on 225,000 processors at 1.3 PFlops!

Very few people have access to hundreds or thousands of individual CPU cores at home or work, but most of us have access to hundreds of cores on the GPU that drives the computer graphics. So let's use our GPUs for massively parallel quantum computing, dawg!

This post tells you how to compile the newest GAMESS (Oct 10, 2010, R1) so it runs RHF 2-electron integrals using an NVIDIA GPU.  
I will follow up with a post on the theory behind the GPU parallel 2-el integral routines and a post on GAMESS/GPU benchmark. I've used a lot of the pointers in the gamess/libqc/aaa.readme.1st file. Let me know if you found this helpful.

In order to do compile GAMESS with CUDA support (using this guide), there are a few requirements:

- I am going assume 64-bit Linux, because I only tried this on that platfrom.
- You need a copy of GAMESS from at least Oct 10, 2010. I'm further more going to assume that you already have properly installed said version of GAMESS and that your installation works. I'm assuming it's installed in  `~/gamess`
- You need a NVIDIA graphics card which is CUDA enable (all newer NVIDIA cards are) with a working set of drivers.
- The GNU gcc and g++ compilers. I've only been able to make this work with 4.1 and 4.3, however. These are included the repositories of all major Linux releases. Other versions may work, but these are "guarateed" to work. Guaranteed, as in they potentially may compile the code. Get these via "sudo apt-get install gcc-4.3 g++-4.3", "yum install", etc. or the ditto of your distro.
- CUDA Toolkit version 2.3. You can download it here. GAMESS does not work with the current version (which is 3.2, at the time of writing. Get the version that matches your Linux the most. I used the Ubuntu 9.10 version on a Ubuntu 10.04 LTS system and the RedHat Enterprise Linux 5.3 version on a CentOS 5.4 system - both worked just fine!
- A compiled and version of Boost 1.43. I tried some other versions, but they didn't work. 1.43 is "guaranteed" to work.
- You need an auxiliary code preprocessor named "cheetah" to do stuff to the C++ source code. I used the newest of 2.4.4.

I can't really help you with installing the NVIDIA drivers, but there are many guides floating around on the interwebs. It is likely that the one that came with your Linux works, but it may not. In that case, get a newer driver. The method to do this is highly OS dependent. Unfortunately, you can't use the open source nouveau nv driver - you need to run the real (and closed) McCoy from NVIDIA.

**UPDATE**: I suggest running the newest beta driver from NVIDIA, since it includes some CUDA specific improvements:

```bash
wget http://us.download.nvidia.com/XFree86/Linux-x86_64/270.18/NVIDIA-Linux-x86_64-270.18.run
```

---

### Step 1) Installing and modifying Boost 1.43 from the source

```bash
mkdir ~/temp
cd temp
wget http://downloads.sourceforge.net/project/boost/boost/1.43.0/boost_1_43_0.tar.bz2
tar xjf boost_1_43_0.tar.bz2
cd boost_1_43_0/
```

Now, compile and install Boost. I always use `~/programs/` for my installation. Boost uses it's own configure and make scripts, and takes about 5–10 minutes to compile on my machine.

```bash
./bootstrap.sh --prefix=/home/andersx/programs/boost_1_43_0
./bjam
./bjam install 
```

`nvcc` (the CUDA compiler) requires three minor modifications of the Boost header files, in order to compile code linked to Boost.

---

#### Step 1.1) Change `integral_wrapper.hpp` line 59 from:

```cpp
#if BOOST_WORKAROUND(__EDG_VERSION__, <= 243)
```

to

```cpp
#if BOOST_WORKAROUND(__EDG_VERSION__, <= 243) || defined(__CUDACC__)
```

---

#### Step 1.2) Change `size_t_fwd.hpp` around line 22:

```cpp
#if defined(__CUDACC__)
   typedef std::size_t std_size_t;
   template< std_size_t N > struct size_t;
#else
   template< std::size_t N > struct size_t;
#endif
```

---

#### Step 1.3) Change `size_t.hpp` around line 23:

```cpp
#if defined(__CUDACC__)
  #define AUX_WRAPPER_VALUE_TYPE std_size_t  
  #define AUX_WRAPPER_NAME size_t    
  #define AUX_WRAPPER_PARAMS(N) std_size_t N 
#else  
  #define AUX_WRAPPER_VALUE_TYPE std::size_t    
  #define AUX_WRAPPER_NAME size_t    
  #define AUX_WRAPPER_PARAMS(N) std::size_t N 
#endif
```

---

### Step 2) Download and install Cheetah

```bash
cd ~/temp
wget http://pypi.python.org/packages/source/C/Cheetah/Cheetah-2.4.4.tar.gz
tar xvf Cheetah-2.4.4.tar.gz
cd Cheetah-2.4.4/
python setup.py install
```

---

### Step 3) Get the CUDA Toolkit v. 2.3

```bash
cd ~/temp
wget http://developer.download.nvidia.com/compute/cuda/2_3/toolkit/cudatoolkit_2.3_linux_64_ubuntu9.04.run
sh ./cudatoolkit_2.3_linux_64_ubuntu9.04.run
```

This installs the `nvcc` compiler. I used the default path `/usr/local`.

Update your `~/.bashrc`:

```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```

Then:

```bash
source ~/.bashrc
```

---

### Step 4) Compile the GPU Fock routines (`libqc`)

```bash
cd ~/gamess/libqc
```

#### If using gcc-4.3:

```bash
CC=gcc-4.3 CXX=g++-4.3 CXXFLAGS='-O2 -DNDEBUG -msse3 -ffast-math -ftree-vectorize -mtune=native' ./configure --with-gamess --with-integer8 --with-boost=/home/andersx/programs/boost_1_43_0 --prefix=/home/andersx/gamess/libqc
```

#### If using gcc-4.1:

```bash
CC=gcc-4.1 CXX=g++-4.1 CXXFLAGS='-O2 -DNDEBUG -msse3 -ffast-math -ftree-vectorize' ./configure --with-gamess --with-integer8 --with-boost=/home/andersx/programs/boost_1_43_0 --prefix=/home/andersx/gamess/libqc
```

Then:

```bash
make
make install
```

Update your `~/.bashrc`:

```bash
export LD_LIBRARY_PATH=/home/andersx/gamess/libqc:$LD_LIBRARY_PATH
```

---

### Step 5) Linking GAMESS to libqc

In the `~/gamess/comp` file, set:

```bash
GPUCODE=true 
```

Recompile the `rhfuhf` routine:

```bash
./comp rhfuhf
```

In the `~/gamess/lked` script, also set:

```bash
GPUCODE=true
```

Then change the library pathnames the second time the string 'GPUCODE' appears — the three paths must point to your **libqc**, **CUDA**, and **Boost** libraries.

Finally, link a new executable:

```bash
./lked gamess gpu
```

If linking was successful, there is now an executable named:

```bash
gamess.gpu.x
```

**Congratulations:** You're now able to do 2-el integrals and Fock-matrix formation GPU parallel.
