Title: Linux on the ThinkPad X1 Carbon 10th Gen (+ Mini Review)
Date: 2022-10-29
Tags: Linux
Category: Linux

My work machine reached End of Life this year, so I got my choice of upgrade. I decided to get the newest ThinkPad X1 Carbon, as they offer Fedora
as an OS choice, and the X1C generally have good reviews and reputation in the Linux world.

Specs are as follows:

```
             .',;::::;,'.                Dvlv@toolbox 
         .';:cccccccccccc:;,.            ------------ 
      .;cccccccccccccccccccccc;.         OS: Fedora Linux 36 (Container Image) x86_64 
    .:cccccccccccccccccccccccccc:.       Host: 21CBCTO1WW ThinkPad X1 Carbon Gen 10 
  .;ccccccccccccc;.:dddl:.;ccccccc;.     Kernel: 5.19.16-200.fc36.x86_64 
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.    Uptime: 7 mins 
.:ccccccccccccc;KMMc;cc;xMMc:ccccccc:.   Packages: 457 (rpm) 
,cccccccccccccc;MMM.;cc;;WW::cccccccc,   Shell: bash 5.2.2 
:cccccccccccccc;MMM.;cccccccccccccccc:   Resolution: 1920x1200 
:ccccccc;oxOOOo;MMM0OOk.;cccccccccccc:   DE: GNOME 
cccccc:0MMKxdd:;MMMkddc.;cccccccccccc;   WM: Mutter 
ccccc:XM0';cccc;MMM.;cccccccccccccccc'   CPU: 12th Gen Intel i7-1260P (16) @ 3.400GHz 
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;    GPU: Intel Alder Lake-P 
ccccc;0MNc.ccc.xMMd:ccccccccccccccc;     Memory: 1739MiB / 31806MiB 
cccccc;dNMWXXXWM0::cccccccccccccc:,
cccccccc;.:odl:.;cccccccccccccc:,.                               
:cccccccccccccccccccccccccccc:'.                                 
.:cccccccccccccccccccccc:;,..
  '::cccccccccccccc::;,.
```

## Linux Compatability

### Confirmed Working:
- Wifi
- Bluetooth (Headphones)
- Sleep / Suspend (I put the machine to sleep after work at 16:30, and it typically has 91 - 93% the next morning at 08:00)
- Keyboard Backlight
- Speakers & Microphone
- Webcam (More on this below)
- Screen Brightness Controls
- Trackpad and buttons

### Untested
- Fingerprint Reader (I use the machine docked so I have no need for this)
- Hibernate (I just sleep the machine or shut it down)
- Mobile Internet (I have read that this is not expected to work)

#### Webcam Support
Apparently the X1C can come with two camera options, one of which claims "Computer Vision". This camera apparently will _not_ work on Linux right now. I
only had one option for the camera when building my machine, so I guess they either don't offer that camera in the UK or they stopped using it. The camera in my machine worked fine out-of-the-box.


## Mini Review
I never really know what to write for hardware reviews, as I'm much more of a software person. However, I will say that I really like the keyboard on this machine.
My T490s took a little more force to press each key than most machines, and as a result I find I miss quite a few keypresses when typing on it. My Yoga Slim 7i Pro has very soft
keys, and is much nicer to type on. I woudld say the X1C is a nice middle ground between the two, and I have no problems typing on this at all.

The X1C's screen is 1900x1200 and looks great. I think I prefer it to the 2k screen on the Yoga, as that causes weird issues with VMs thinking it's 4k, and I have to enable fractional scaling, which is a bit bleh.

I definitely think 14 inches is the sweet-spot for laptops. 13 feels too small and 15 a bit too big. I have no idea how some of my colleagues lug around 16 inch Macbooks!

The ThinkShutter (privacy cover over the built-in webcam) is a neat idea, and I keep it permanently closed as I have an external cam for meetings.

### Bad Start?
I've been reading some horror stories on Hacker News about 12th gen Intel processors, and the X1C 10th Gen itself, but my experience has been flawless out-of-the-box, so I guess either I've been lucky or the problems have been fixed by now.

If you've been reading the same things and are hesitant about the Linux compatability of this machine, I wouldn't worry (assuming you're planning on using an up-to-date distro such as Fedora or Arch. Slower-moving distros like Ubuntu or Mint I cannot speak for).

### Would I recommend this laptop?
As I said in my review of the Yoga, we are still in a weird time with laptop hardware. I still think the Apple M1 / M2 machines are probably the best hardware available at the moment. If you can stand to use MacOS, I would
honestly recommend a Macbook Pro. 

However, if you're like me and cannot get on with MacOS (or their _awful_ keyboards) then I can say I've had a great first couple of weeks with my X1C running Fedora Silverblue, and I'm glad I waited for it. 

