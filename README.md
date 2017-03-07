# Trackpad toggle switch on X1 Carbon / KDE Neon

### Intro
Aim of this script is to create a toggle switch to enable/disable trackpad on a Lenovo X1 Carbon running Ubuntu KDE Neon

I have fat fingers :( so whenever I type on my new (to me) X1 Carbon, my thumbs are always hovering and touching the touchpad which creates unwanted curser mouvements or scrolls (and it is quite annoying). 

There is no hardware switch on these laptops (as on older Thinkpads) so I had to create one.

I will create a script that switches on and off trackpad and in a second part I will link this script to a keyboard shortcut (KDE Neo seems pretty good at that)

### First part : the script

I chose zsh but I believe this should also work in bash. 

I will be using xinput to list available input devices. 

```zsh
➜  thinkpad xinput
⎡ Virtual core pointer                          id=2    [master pointer  (3)]
⎜   ↳ Virtual core XTEST pointer                id=4    [slave  pointer  (2)]
⎜   ↳ TPPS/2 IBM TrackPoint                     id=13   [slave  pointer  (2)]
⎜   ↳ Microsoft Microsoft® Comfort Mouse 4500   id=9    [slave  pointer  (2)]
⎜   ↳ SynPS/2 Synaptics TouchPad                id=12   [slave  pointer  (2)]
⎣ Virtual core keyboard                         id=3    [master keyboard (2)]
    ↳ Virtual core XTEST keyboard               id=5    [slave  keyboard (3)]
    ↳ Power Button                              id=6    [slave  keyboard (3)]
    ↳ Video Bus                                 id=7    [slave  keyboard (3)]
    ↳ Sleep Button                              id=8    [slave  keyboard (3)]
    ↳ Integrated Camera                         id=10   [slave  keyboard (3)]
    ↳ AT Translated Set 2 keyboard              id=11   [slave  keyboard (3)]
    ↳ ThinkPad Extra Buttons                    id=14   [slave  keyboard (3)]
```

`xinput` says that Synaptics TouchPad is id=12 so I can list all it's properties using :

```zsh
➜  ~ /usr/bin/xinput --list-props "SynPS/2 Synaptics TouchPad"
Device 'SynPS/2 Synaptics TouchPad':
        Device Enabled (139):   0
        Coordinate Transformation Matrix (141): 1.000000, 0.000000, 0.000000, 0.000000, 1.000000, 0.000000, 0.000000, 0.000000, 1.000000
        Device Accel Profile (268):     1
        Device Accel Constant Deceleration (269):       2.500000
        Device Accel Adaptive Deceleration (270):       1.000000
        Device Accel Velocity Scaling (271):    12.500000
        Synaptics Edges (293):  1574, 5368, 1352, 4502
        Synaptics Finger (294): 25, 30, 0
        Synaptics Tap Time (295):       180
        Synaptics Tap Move (296):       252
        Synaptics Tap Durations (297):  180, 100, 100
        Synaptics ClickPad (298):       1
        Synaptics Middle Button Timeout (299):  0
        Synaptics Two-Finger Pressure (300):    282
        Synaptics Two-Finger Width (301):       7
        Synaptics Scrolling Distance (302):     114, 114
        Synaptics Edge Scrolling (303): 1, 0, 0
        Synaptics Two-Finger Scrolling (304):   1, 0
        Synaptics Move Speed (305):     1.000000, 1.750000, 0.034892, 0.000000
        Synaptics Off (306):    2
        Synaptics Locked Drags (307):   0
        Synaptics Locked Drags Timeout (308):   5000
        Synaptics Tap Action (309):     2, 3, 0, 0, 1, 3, 0
        Synaptics Click Action (310):   1, 3, 0
        Synaptics Circular Scrolling (311):     0
        Synaptics Circular Scrolling Distance (312):    0.100000
        Synaptics Circular Scrolling Trigger (313):     0
        Synaptics Circular Pad (314):   0
        Synaptics Palm Detection (315): 0
        Synaptics Palm Dimensions (316):        10, 200
        Synaptics Coasting Speed (317): 20.000000, 50.000000
        Synaptics Pressure Motion (318):        30, 160
        Synaptics Pressure Motion Factor (319): 1.000000, 1.000000
        Synaptics Resolution Detect (320):      1
        Synaptics Grab Event Device (321):      0
        Synaptics Gestures (322):       1
        Synaptics Capabilities (323):   1, 0, 0, 1, 1, 1, 1
        Synaptics Pad Resolution (324): 68, 45
        Synaptics Area (325):   0, 0, 0, 0
        Synaptics Soft Button Areas (326):      3471, 0, 4098, 0, 0, 0, 0, 0
        Synaptics Noise Cancellation (327):     28, 28
        Device Product ID (259):        2, 7
        Device Node (260):      "/dev/input/event6"
```

The one property we are looking for is the first one in the list `Device Enabled` . Setting this property to 0 disables the touchpad, setting it to 1 enables it. It is now trivial to create a script that toggles on and off this property :

```zsh
#! /usr/bin/zsh
if [[ `/usr/bin/xinput --list-props "SynPS/2 Synaptics TouchPad"| awk ' /Device Enabled/ { print $NF} '` = 0 ]] 
        then /usr/bin/xinput set-prop "SynPS/2 Synaptics TouchPad" "Device Enabled" 1
else /usr/bin/xinput set-prop "SynPS/2 Synaptics TouchPad" "Device Enabled" 0
fi
```

### Second part : the shortcut

KDE Neon is pretty good at personalisation and allows you to easily create and modify shortcuts. I chose to reuse the `Search` shortcut (Fn+F10) as I never use it. 

In Systems Settings Apps, just look for 'Custom Shortcuts' Then click on Edit > New > Global Shortcut > Command/Url

In Trigger, just click on Create and specify which shortcut you are going to be using.
In Actions, add the script you create in step one. And voilà.

### Conclusion

Nothing fancy here just a script and using KDE Neon personalisation options. I tried to do a oneliner instead of a script but adding logic to switch on/off was proven to be tricky.
