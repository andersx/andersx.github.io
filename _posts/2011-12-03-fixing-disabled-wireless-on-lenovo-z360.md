---
layout: post
title: Fixing disabled wireless on Lenovo Z360 running Ubuntu 10.10
date: '2011-12-03T13:04:00.000+01:00'
author: hellogetmyblogback
tags:
- Lenovo z360 wireless disabled turn on off ubuntu lucid maverick 10.10 10.04 linux
modified\_time: '2011-12-03T13:04:58.315+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-4905662824768999486
blogger\_orig\_url: https://combichem.blogspot.com/2011/12/fixing-disabled-wireless-on-lenovo-z360.html
---

Ok, this post isn't about chemistry at all. However, I'm going to defend it under the category of free software. Here is a short introduction to my problem. I have a Lenovo Z360 laptop running Ubuntu 10.10. It came with some Windows distribution (are they even called *distributions?* I haven't had a windows computer since 2004) which I never used. <sarcasm> Thanks to Lenovo, the laptop was set up with some proprietary driver software for my wireless network interface card (NIC). </sarcasm>

After installing Ubuntu 10.10 (AKA the perfect 10) everything was working fine ... until I one day pressed the "turn off wireless" buttons (Fn+F5) to save a tiny amount of battery power. This apparently bypasses the hardware "disable wireless switch" and switches off wireless via some built-in firmware. And since I wasn't using the proprietary drivers in Windows, there was no way to switch wireless back on.

The tedious solution to my problem was to replace the solid-state drive I had put in myself for the original drive, and boot up in windows, switch on wireless using the proprietary driver software. Long story short, I have up until this day always been carrying the original harddive and a small screwdriver with me, in case I should accidentally turn off wifi, so I could perform the wireless-enabling surgical procedure.

However, today I stubled up a more permanent solution. I turns out, that the signal to turn off the wireless NIC is transmitted via pin #20. So to avoid ever turning off wireless you just have to tape over that particular pin - see the picture below. After five minutes of fiddling with tape, tweezers and scissors I managed cut a piece of tape small enough and put it precisely over the pin. And lo and behold - it's now impossible to disable the wireless NIC via switches or accidental key-presses!

[![](http://images.anandtech.com/doci/4577/TCAE-3959.jpg)](http://images.anandtech.com/doci/4577/TCAE-3959.jpg)

Thanks to the guys over at [anandtech.com](http://www.blogger.com/goog_583748786)for pointing this out! And thanks to Lenovo for this post possible by making hardware that just works out-of-the-box-until-it-doesn't-and-there's-no-way-to-switch-it-back-on-besides-software-only-written-for-one-particular-operating-system.