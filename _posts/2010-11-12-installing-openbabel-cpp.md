---
layout: post
title: "Installing OpenBabel and Compiling with the C++ API"
date: 2010-11-12 23:20:00 +0100
categories: [C++, OpenBabel, compiling]
tags: [openbabel, c++, chemistry, linux, cmake]
lang: en
---

**OpenBabel** is a tool that makes life easier for everyone in the computational chemistry world. You can do tons of cool stuff with OpenBabel! I regularly use it to convert PDB files to input files for Gaussian09 and XYZ files, which are then used to calculate quantum chemistry on proteins — and often back to PDB format again.

It can also juggle around with SMILES strings, do all sorts of cheminformatics and whatnot. Everything you and I need. **OpenBabel is the King** for these kinds of things.

In addition, OpenBabel has APIs for **Python**, **Perl**, and **C++**, so you can use all of its features in your own scripts or compiled programs. I started using it for a project requiring automatic generation of thousands of protein fragment XYZ files and automatic handling of input and output files. Many of my colleagues use OpenBabel for similar tasks.

Not only is this an absolutely awesome piece of software, it's also **completely free and open source** under the GNU GPL. I love this!

This post covers basic installation and use of the **C++ API**, which is not completely straightforward in some cases.

---

## 🛠 Installing and Compiling OpenBabel with C++

For some users, installing and compiling OpenBabel on a Linux machine can be a daunting task. Recently, I helped my dear colleague **Kasper Thofte** set up OpenBabel and compile some C++ code on our local Beowulf cluster.

Kasper is currently developing some very cool automatic transition state search methods in **GAMESS**, which is also free quantum chemistry software.

Since we don't have root access on our cluster, everything had to be installed in our **home directories** — we couldn’t use `/usr/lib`, etc.

These are the steps we followed:

---

### 📥 Step 1: Download OpenBabel source

```bash
cd ~/src
svn co https://openbabel.svn.sourceforge.net/svnroot/openbabel/openbabel/trunk openbabel
```

---

### ⚙️ Step 2: Compile with CMake

```bash
cd openbabel
cmake -DCMAKE_INSTALL_PREFIX=/home/andersx/programs/openbabel .
make install
```

This installs OpenBabel into your personal directory.

---

### 🧠 Step 3: Set up environment variables

Edit your `~/.bashrc` and add:

```bash
export PATH=$PATH:/home/andersx/programs/openbabel/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/andersx/programs/openbabel/lib
```

Then restart your terminal or run:

```bash
source ~/.bashrc
```

---

### 🧪 Step 4: Compile your own C++ code using OpenBabel

You’ll need to manually tell `g++` where the OpenBabel headers and libraries live:

```bash
g++ -I/home/andersx/programs/openbabel/include/openbabel-2.0 \
    -L/home/andersx/programs/openbabel/lib \
    -lopenbabel OBtest.cpp -o OBtest
```

And there you go! The above took us quite a while to figure out, but this method should work on any Linux machine using GNU compilers.

---

## 📝 Update

My friend **Casper Steinmann** pointed out that he has a great blog post on installing and using the **Python API for OpenBabel**. So you have no excuses for not using OpenBabel in your workflow:

👉 [http://www.caspersteinmann.dk/?q=node/4](http://www.caspersteinmann.dk/?q=node/4)

---

> 🧪 Labels: `C++`, `compiling`, `OpenBabel`
