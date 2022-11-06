Title: Lenovo Yoga Slim 7i Review - Linux
Date: 2022-01-30
Tags: Linux, Hardware
Category: Linux

I recently bought myself a Lenovo Yoga Slim 7i Pro (bit of a mouthful of a name!) and have put Fedora Silverblue on it. This post will be a short review of my experience after 3 weeks.

## Specs
Here's the output of `neofetch`, which contains info about the CPU, GPU and RAM.

```

             .',;::::;,'.                Dvlv@toolbox 
         .';:cccccccccccc:;,.            ------------ 
      .;cccccccccccccccccccccc;.         OS: Fedora Linux 35 (Container Image) x86_64 
    .:cccccccccccccccccccccccccc:.       Host: 82NC Yoga Slim 7 Pro 14IHU5 
  .;ccccccccccccc;.:dddl:.;ccccccc;.     Kernel: 5.15.16-200.fc35.x86_64 
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.    Uptime: 4 mins 
.:ccccccccccccc;KMMc;cc;xMMc:ccccccc:.   Packages: 562 (rpm) 
,cccccccccccccc;MMM.;cc;;WW::cccccccc,   Shell: bash 5.1.8 
:cccccccccccccc;MMM.;cccccccccccccccc:   Resolution: 2880x1800 
:ccccccc;oxOOOo;MMM0OOk.;cccccccccccc:   DE: GNOME 
cccccc:0MMKxdd:;MMMkddc.;cccccccccccc;   WM: Mutter 
ccccc:XM0';cccc;MMM.;cccccccccccccccc'   WM Theme: Adwaita 
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;    Theme: Adwaita [GTK2/3] 
ccccc;0MNc.ccc.xMMd:ccccccccccccccc;     Icons: Papirus [GTK2/3] 
cccccc;dNMWXXXWM0::cccccccccccccc:,      Terminal: conmon 
cccccccc;.:odl:.;cccccccccccccc:,.       CPU: 11th Gen Intel i7-11370H (8) @ 4.800GHz 
:cccccccccccccccccccccccccccc:'.         GPU: Intel TigerLake-LP GT2 [Iris Xe Graphics] 
.:cccccccccccccccccccccc:;,..            Memory: 2070MiB / 15773MiB

```

The screen is a 14 inch 16:10 2k display, and it runs at 90Hz. This is definitely the nicest laptop screen I've ever used. The bezels around it are tiny, so there's very little wasted space.

Above the screen, the webcam sits in a little lip, which is also where you put your thumb to open the machine.

Speaking of which, the laptop can be very easily opened with one hand, and has the ability to boot upon being opened.

## Keyboard

The keyboard is great. I would say it's on par with the Thinkpad T490s I use for work. Slightly less key travel but a great layout.

As a sidenote - it's very difficult to find 14 inch laptops with a "normal" UK keyboard layout. _So many_ of them either have the small US-style Enter key or 
weird arrow keys. I don't understand why, there's plenty of space!

## Battery Life
I did a few unscientific tests of the machine's battery capabilities; running Fedora at low brightness with TLP activated and Gnome set to "Power Saver" mode.

Firstly, I did some "normal" activities - mostly reading documentation and viewing a couple of youtube videos.
In 30 minutes, the battery went from 52% to 45% (so -7%). This would imply ~7 hours battery for just browsing the web.

I then did some YouTube tests. These were all playing a video full-screen at 1080p for 20 minutes.

Firefox under linux took the battery from 89% to 80% - implying a 27% per hour, so just under 4 hours of battery life.

I then tried Edge on Windows 10, which went from 79% to 74% - implying 15% per hour, so just under 7 hours.

Surprised by the results, I tried Chromium on Linux. That took me from 68% to 62% - basically the same result as Windows. I'm guessing Chromium is a better choice for video battery life.

I'm yet to do any kind of big programming task on this machine, but so far I'm very happy with the battery life. I would guesstimate that for my typical use (mostly writing and web browsing) it loses ~10% per hour.

## Out-of-the-box Linux Experience

Not as good as I was expecting. See my post on [installing Linux on this machine](https://www.dvlv.co.uk/installing-linux-on-a-lenovo-yoga-slim-7i-pro.html) for the mandatory kernel parameters needed to make the device function by itself.

**Edit:** The below was fixed by switching to a Graphical Grub.<br/>
There's also a weird quirk on Grub: when the options display, there's a small dot which scans across the screen, working its way to the bottom. Grub will not respond until this dot 
reaches the bottom - about 3 seconds ish. Grub works fine as soon as it finishes its journey, though. I'm not sure if this is just a quirk of the console-grub which Silverblue uses - perhaps
a graphical-themed grub wouldn't do this. It doesn't bother me enough to find out. 

Once those three parameters are added, and aside from the grub quirk, everything else has been perfect. Sleep works fine, and the media keys all function. Speakers, wifi, trackpad all work perfectly.

There's pretty much no fan noise through normal use, I think I've only heard them spin up when watching YouTube while installing some bits.


## Would I Recommend This Machine?

The current market is in a weird place. The best hardware (internally, at least) is without-a-doubt the M1 Macbooks. I just can't stand MacOS or their weird hipster keyboard layouts.

If you're in the market for a somewhat-cheap machine with, in my opinion, a great keyboard and fantastic screen, the Yoga Slim 7i Pro is a good choice. 

If, however, you don't hate Macs, or you're not particularly desperate for a new laptop, it's probably worth getting an M1 Mac, or waiting for the rest of the industry's "response" to them.

I was also interested in, but never got to try, a Thinkpad X1 Carbon 9th gen. While that may offer more diverse specs, and possibly better Linux support, I do particularly like the
90Hz 2k screen on the Yoga, so I'm not unhappy with my choice.



