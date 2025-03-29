---
layout: post
title: Compiling Open Babel with Python bindings
date: '2013-12-05T15:50:00.001+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-12-06T10:12:58.790+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-3237309238512978976
blogger\_orig\_url: https://combichem.blogspot.com/2013/12/compiling-open-babel-with-python.html
---

# Use the commands below to compile Open Babel with python bindings:



git clone git://github.com/openbabel/openbabel.git

cd openbabel

mkdir build

cd build

cmake -DRUN\_SWIG=ON -DPYTHON\_BINDINGS=ON \

-DCMAKE\_INSTALL\_PREFIX=/opt/openbabel  -DENABLE\_TESTS=ON ..

make

make test

sudo make install



# Export these variables to use your newly compiled Open Babel:



export PYTHONPATH=/opt/openbabel/lib/python2.7/site-packages:\$PYTHONPATH


export LD\_LIBRARY\_PATH=/opt/openbabel/lib:\$LD\_LIBRARY\_PATH


export PATH=/opt/openbabel/bin:\$PATH