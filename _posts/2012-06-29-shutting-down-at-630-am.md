---
layout: post
title: Shutting down at 6:30 AM?
date: '2012-06-29T08:42:00.002-07:00'
author: hellogetmyblogback
tags:
modified\_time: '2012-07-06T11:28:33.801-07:00'
blogger\_id: tag:blogger.com,1999:blog-4501956485444967735.post-4099827913145789503
blogger\_orig\_url: https://centosn00b.blogspot.com/2012/06/shutting-down-at-630-am.html
---

We're having cooling maintenance carried out on Monday morning at 7:30 (yes AM!). As nobody likes getting up so *friggin'* early in the morning, here's how I'm going to shut down the servers nicely at 6:30. The command is simply called **'at'**.



You envoke it by **'at <time>'**, e.g.:




# # at 6:30

# > shutdown -hP [Ctrl-D]


Here is where write the command being carried out .. when you're done, simply exit with a **[Ctrl-D]**. You wil be greeted by a message like



# job 3 at 2012-06-29 6:30



The integer is the job id. You can also be more specific:




# # at 6:30 monday


Jobs are executed only once. List your current at jobs with **atq**:




# # atq

# 3    2012-06-29 6:30 a root


And cancel your at jobs with **atrm** (using the job id):



# # atrm 3



Back to the problem at hand. We have a bunch of compute nodes, named *unray (i.e. bunray, gunray, funray, zunray, punray, wunray, runray,). Since I don't want to manually SSH into every node, I use SSH-keys and a for loop. All nodes mount the same /opt/folder. I put a file containing the command "**shutdown -hP now**" in **/opt/scripts/shutdown.txt**. **at** reads commands from a file via the **-f** switch. Now it is very easy to make all nodes receive the **at** command:






# for i in b f g z p r w

# do

# ssh root@$i\unray at -f /opt/scripts/shutdown.txt 6:30am monday

# done﻿




Happy **at**'ing!


