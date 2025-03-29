---
layout: post
title: 'FragBuilder: Installation'
date: '2013-12-27T14:29:00.000+01:00'
author: hellogetmyblogback
tags:
modified_time: '2013-12-27T14:29:00.840+01:00'
blogger_id: tag:blogger.com,1999:blog-8160351477288734008.post-7247575803958068734
blogger_orig_url: https://combichem.blogspot.com/2013/12/fragbuilder-installation.html
---

With the FragBuilder library out as a [preprint from PeerJ](https://peerj.com/preprints/169v2/) and the library available at [https://github.com/jensengroup/fragbuilder](https://github.com/jensengroup/fragbuilder), I’m going to make a couple of blog posts to get you started using FragBuilder. You can access the manual via the GitHub page linked above.

This post will guide you through the **installation** of the FragBuilder library.

> **Figure 1:** Example of peptide model made using the FragBuilder library

---

## ✅ Requirements

FragBuilder will run on most Linux distributions with a recent **Python 2.x** interpreter.

You need to have the following installed:

1. **NumPy**
2. **Open Babel with Python bindings**
3. **Git** (to clone FragBuilder from GitHub)

---

## 📦 Installing FragBuilder

Use `git` to download (clone) the repository:

```bash
git clone git://github.com/jensengroup/fragbuilder
```

This will create a folder named `fragbuilder`. There’s no compilation needed, so it’s ready to go.

To use FragBuilder in your Python scripts, export the path to the library:

```bash
export PYTHONPATH=/home/andersx/dev/fragbuilder:$PYTHONPATH
```

You can add this to your `~/.bashrc` file so it’s set every time you log in.

---

## 🧪 Testing Your Installation

Fire up Python and try importing the module:

```bash
$ python

Python 2.7.3 (default, Sep 26 2013, 20:03:06)
[GCC 4.6.3] on linux2
Type "help", "copyright", "credits" or "license" for more information.

>>> import fragbuilder
>>> print fragbuilder.__version__
1.0
```

If there are no errors, FragBuilder is working 🎉

---

## 🔧 Installing Dependencies

### 1. NumPy

Use your package manager:

**Ubuntu/Debian:**
```bash
sudo apt-get install python-numpy
```

**Fedora/Red Hat:**
```bash
su
yum install python-numpy
```

More info: [http://www.scipy.org/install.html](http://www.scipy.org/install.html)

---

### 2. Open Babel with Python Bindings

**Ubuntu/Debian:**
```bash
sudo apt-get install python-openbabel
```

**Fedora/Red Hat:**
```bash
su
yum install python-openbabel
```

#### Note:

If you run into issues (e.g. dihedral angles limited to 179.96°), you may want to compile Open Babel yourself.

I wrote a guide here:  
[Compiling Open Babel with Python](http://combichem.blogspot.dk/2013/12/compiling-open-babel-with-python.html)  
Official instructions:  
[http://openbabel.org/docs/dev/Installation/install.html](http://openbabel.org/docs/dev/Installation/install.html)

---

### 3. Git

**Ubuntu/Debian:**
```bash
sudo apt-get install git
```

**Fedora/Red Hat:**
```bash
su
yum install git
```

You’ll use Git to download FragBuilder and optionally submit patches.

---

That’s it! If you run into issues, feel free to leave a comment or question.
