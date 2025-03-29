---
layout: post
title: Downgrading from NVIDIA 304.43 to 295.71 on CentOS 6
date: '2012-09-07T09:10:00.003-07:00'
author: hellogetmyblogback
tags:
modified\_time: '2012-09-07T09:16:48.231-07:00'
blogger\_id: tag:blogger.com,1999:blog-4501956485444967735.post-3212271811982752851
blogger\_orig\_url: https://centosn00b.blogspot.com/2012/09/downgrading-from-nvidia-30443-to-29571.html
---

The latest upgrade in 'elrepo' updates the NVIDIA drivers from version 295.71 to version 304.43. However, it ain't no bed of roses. On my CentOS 6.3 box, 3D performance under Wine took a massive hit, to the point where even sound playback was affected.



However, it's easy to just roll back the NVIDIA driver upgrades and get on with your life. First, grab the latest working version from elrepo:




```
wget http://elrepo.reloumirrors.net/elrepo/el6/x86\_64/RPMS/kmod-nvidia-295.71-1.el6.elrepo.x86\_64.rpm
wget http://elrepo.reloumirrors.net/elrepo/el6/x86\_64/RPMS/nvidia-x11-drv-32bit-295.71-1.el6.elrepo.x86\_64.rpm
wget http://elrepo.reloumirrors.net/elrepo/el6/x86\_64/RPMS/nvidia-x11-drv-295.71-1.el6.elrepo.x86\_64.rpm

```



Now, remove the new, buggy 304.43 drivers:




```
yum remove nvidia-x11-drv kmod-nvidia nvidia-x11-drv-32bit
```


```

```

Beware that uninstalling kmod-nvidia may take quite a while. Finally, install the fresh, old drivers:




```
rpm -Uvh kmod-nvidia-295.71-1.el6.elrepo.x86\_64.rpm \
nvidia-x11-drv-295.71-1.el6.elrepo.x86\_64.rpm \
nvidia-x11-drv-32bit-295.71-1.el6.elrepo.x86\_64.rpm
```



Reboot. Commence life.


