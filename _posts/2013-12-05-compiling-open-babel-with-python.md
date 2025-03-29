---
layout: post
title: Compiling Open Babel with Python bindings
date: '2013-12-05T15:50:00.001+01:00'
author: hellogetmyblogback
tags:
modified_time: '2013-12-06T10:12:58.790+01:00'
blogger_id: tag:blogger.com,1999:blog-8160351477288734008.post-3237309238512978976
blogger_orig_url: https://combichem.blogspot.com/2013/12/compiling-open-babel-with-python.html
---

## 🛠️ How to Compile Open Babel with Python Bindings

Use the commands below to compile **Open Babel** from source with Python bindings:

```bash
git clone git://github.com/openbabel/openbabel.git
cd openbabel
mkdir build
cd build

cmake -DRUN_SWIG=ON -DPYTHON_BINDINGS=ON \
  -DCMAKE_INSTALL_PREFIX=/opt/openbabel \
  -DENABLE_TESTS=ON ..

make
make test
sudo make install
```

---

## 🧪 Environment Variables

To use your newly compiled Open Babel, export the following environment variables:

```bash
export PYTHONPATH=/opt/openbabel/lib/python2.7/site-packages:$PYTHONPATH
export LD_LIBRARY_PATH=/opt/openbabel/lib:$LD_LIBRARY_PATH
export PATH=/opt/openbabel/bin:$PATH
```

You're now ready to use Open Babel with Python!
