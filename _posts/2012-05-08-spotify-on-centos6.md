---
layout: post
title: "Spotify on CentOS 6"
date: 2012-05-08 12:00:00 +0200
categories: [CentOS, Spotify]
tags: [spotify, centos, linux, alien, rpm, workaround]
lang: en
---

Tuesday, 8 May 2012  
**Spotify on CentOS 6**

There is no installation for Spotify on CentOS, and there are some whacked dependencies which are cumbersome to get around. I tried this on CentOS 6.2.

---

### Step 1: Install Alien (and git)

```bash
yum install git make
git clone git://git.kitenet.net/alien
cd alien/
perl Makefile.PL
make
make install
```

---

### Step 2: Download Spotify `.deb` files

Download from:

[http://repository.spotify.com/pool/non-free/s/spotify/](http://repository.spotify.com/pool/non-free/s/spotify/)

---

### Step 3: Convert and install the Debian/Ubuntu packages

```bash
alien --to-rpm spotify-client-qt*.deb
rpm -Uvh --nodeps spotify-client-qt*.rpm
alien --to-rpm spotify-client*.deb
rpm -Uvh --nodeps spotify-client*.rpm
```

---

### Step 4: Get required libraries

Download my magical package of required libraries:

[http://dl.dropbox.com/u/17435887/CentOS6/andersx/spotify_libs.tar.gz](http://dl.dropbox.com/u/17435887/CentOS6/andersx/spotify_libs.tar.gz)

Extract these in your home folder. It will create:

```
/home/andersx/spotify_libs
/home/andersx/spotify.sh
```

---

### Step 5: Edit the launcher script

Edit `spotify.sh` so it points to your actual home directory. For example:

```bash
export LD_LIBRARY_PATH=/home/andersx/spotify_libs
```

These libraries were carefully taken from an Ubuntu 10.04 LTS system. In order to **NOT mess up your CentOS**, use the supplied script to launch Spotify instead of changing your system's library path globally.

Of course, feel free to put the libraries and launcher script somewhere else if you prefer.

---

**Happy listening!**  
`:wq`
