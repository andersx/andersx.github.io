---
layout: post
title: Light alternative to compiz grid effect
date: '2012-06-09T11:40:00.002-07:00'
author: Unknown
tags:
- lxde
- half-screen
- compiz grid
- window
modified\_time: '2012-06-09T12:35:07.993-07:00'
blogger\_id: tag:blogger.com,1999:blog-4501956485444967735.post-3628078881030678429
blogger\_orig\_url: https://centosn00b.blogspot.com/2012/06/light-alternative-to-compiz-grid-effect.html
---




Implementing the



* **unity** - window placement half-screen (ctrl+alt+numpad 4/5)
* **compiz** - grid
* **windows 7** - window half screen (Super + Left/RightArrow)




effect on a 'naked system', without install heavy window management tools like Compiz.



Problem:

Okay, So I like being efficient with my working environment, especially with my window management, as I don't like wasting space on my 13" laptop screen.



I'm running **LXDE** with **OpenBox** and Anders is running **gnome2** with **Metacity**.

###
PyWO - Python Window Organizer


The solution is too use the small python script provided by PyWo which can be downloaded from <http://code.google.com/p/pywo/> written by <http://kosciak.blox.pl/html>.

In writing I'm using the 0.2 version.



###
**Implementation (really easy!)**:


1. Download version 0.2 from google code, and put in a folder. I put it in ~/settings/pywo-2.0/
2. Run the downloaded script (/home/charnley/settings/pywo-2.0/pywo.py)
3. DONE





# Default keyboard shortcuts:






```
Alt-Ctrl-Shift-Q    - exit PyWO
Alt-Ctrl-Shift-R    - reload configuration fileAlt-Ctrl-Shift-I    - print debug information about window manager and current window
Alt-KP\_Divide       - switch windows (change position of the window)
Alt-Shift-KP\_Divide - cycle windows (change contents of the window)
Alt-KP\_1-9          - move window in a direction (KP\_1-9 - numpad keys)
Shift-KP\_1-9        - expand window in a direction (5 works as maximumize compiz plugin)
Alt-Shift-KP\_1-9    - shrink windowAlt-Ctrl-KP\_1-9     - put window to predefined positionCtrl\_KP\_1-9         - put and resize (grid), and cycle widthsCtrl-Shift\_KP\_1-9   - put and resize (grid), and cycle heights
```





Note, the default configuration is to use the numpad as the control, but being a laptop user this is not an option. So my setup is:

###




Laptop configuration:




Create ~/.pyworc and fill in:






```
[SETTINGS]
; 1/on/yes/true - work only when NumLock is on
; ignore - ignore NumLock state (works when on and off)
; 0/off/no/false - work only when NumLock is off
numlock = ignore

; invert window gravity if it needs resizing (eg terminals with incremental
; size change), works only for grid
invert\_on\_resize = yes

; if true try expandig in vertical axis first
vertical\_first = yes

; grid\_2x2, grid\_3x2, grid\_3x3, custom
layout = grid\_2x2

;ignore\_actions = grid\_height

[KEYS]
; section/point-name =  key
top-left = KP\_7
top = KP\_8
top-right = KP\_9
left = Left
middle = Up
right = Right
bottom-right = KP\_3
bottom = KP\_2
bottom-left = KP\_1

switch = Alt-KP\_Divide
cycle = Alt-Shift-KP\_Divide

; key modifiers (Alt, Shift, Ctrl, Super) for actions
float = Alt-Shift
expand = Alt-Shift
shrink = Alt-Shift
put = Super-Ctrl
; cycle widths
grid\_width = Super
; cycle heights
grid\_height = Ctrl-Shift

; exit PyWo
exit = Ctrl-Shift-Alt-Q
; reload configuration file
reload = Ctrl-Shift-Alt-R
; print info for current window
debug = Ctrl-Shift-Alt-I

```






I changed the configuration so it works almost like the windows alternative, with arrow and super key.


Grid: **2x2**



Shortcuts:

# Super+Left Arrow  = fill left half of screen

# Super+Right Arrow = fill right half of screen

# Super+Arrow Up = fill full screen



For list of shortcut/keys available check:

<http://python-xlib.svn.sourceforge.net/viewvc/python-xlib/trunk/Xlib/keysymdef/miscellany.py?revision=151&view=markup&pathrev=151>

<http://python-xlib.svn.sourceforge.net/viewvc/python-xlib/trunk/Xlib/keysymdef/latin1.py?revision=151&view=markup&pathrev=151>





###
# Autostart the script:


####

LXDE:


To autostart a script in LXDE you create a .desktop file in ~/.config/autostart/.

If you are a awesome LXDE user, create **~/.config/autostart/pywo.desktop** and fill in the following:






```
[Desktop Entry]
Encoding=UTF-8
Name=PyWo
Comment=Python Window Organizer
Exec=/home/charnley/settings/pywo-0.2/pywo.py
X-GNOME-Autostart-enabled=true

```



Where you substitute the Exec with the path to where you downloaded pywo too.







And that is it! Happy windowing!


```



```






