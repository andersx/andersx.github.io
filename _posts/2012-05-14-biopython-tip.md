---
layout: post
title: "Biopython tip"
date: 2012-05-14 12:00:00 +0200
categories: [Biopython, CentOS]
tags: [biopython, centos, python, install, tip]
lang: en
---

Monday, 14 May 2012  
**Biopython tip**

Installing Biopython on CentOS is easy. You need the following:

---

If you haven't already done so install this (rather large) package. It loosely corresponds to what I was used to in Ubuntu as **"build-essential"**:

```bash
yum groupinstall 'Development Tools'
```

You also need this package, or it won't compile (it's not included in "Development Tools"):

```bash
yum install python-devel
```

---

### Now the fun stuff:

```bash
wget http://biopython.org/DIST/biopython-1.59.tar.gz
tar xf biopython-1.59.tar.gz
cd biopython-1.59
python setup.py build
python setup.py test
python setup.py install
```

---

**Happy biopython'ing!**
