:Title: Entroware Zeus Review
:Date: 2018-04-23
:Tags: Linux, Review
:Category: Linux


**Update 07/2019**
It seems Entroware have released a new version of the Zeus. It's now a 16" machine which features *less* IO than the model I purchased, but comes with 
a better processor and a 2080 GPU. This means my review is now somewhat obsolete.

------------

The Zeus is a 15 inch High-end Gaming Laptop from the British-based Linux computer company called Entroware.

Since getting a new job, which I do using a laptop running OpenSUSE Tumbleweed, I've been itching for a Linux laptop of my own. Whilst I am able to take my work laptop home, I thought it best to keep personal stuff off of it, since I don't technically own it. I looked around for a laptop which is high-end enough for some intensive programming (including game dev) and runs Linux without problems.

I came across Entroware online (amongst others, including Starlabs and Stationx) then realised they were affiliated with Ubuntu Mate via some podcasts. I poked around their website and found that the Zeus laptop looked like a good fit. My criteria were not extensive, but there were quite a few laptops which did not fit them. What I wanted was:

    - A Numpad
    - Backlit Keys
    - Normal arrow keys
    - A Home / End Key in a sensible place
    - A Touchpad with Buttons
    - 32GB+ RAM

The Zeus meets all of these criteria.

I've had this laptop for around a month now, and I've been using it as my main machine for anything other than gaming (unfortunately most of what I want to play is only on Windows, and I have a separate machine for that). In this blog post I'll be giving my opinion on various aspects of this laptop.
My Set-Up

    - Processor - i7 7700HQ 3.8GHz
    - RAM - 32GB DDR4 2400MHz
    - Storage - 500GB NVME SSD
    - GPU - Nvidia GTX 1070 Max Q

This came to something like £1960.

I've installed Antergos as the main operating system for this laptop. Usually my go-to distro is OpenSUSE, but due to graphics issues (more on this later) I had to go for my second choice of distro.

Desktop-wise, I'm using Plasma 5.

Out-of-the-Box Experience & First Impressions
---------------------------------------------

The laptop has a very sturdy feel, and opens with one hand (which seems to be important to people). When I first turned it on I was very impressed by how nice the backlit keyboard looks. It can cycle through a few different colours with a function-key-combination (it seems to be hardware based, so no need for some fancy driver, which was a concern of mine. My gaming laptop running Windows required me to find a random .exe and run it as administrator EVERY TIME I want to turn the keyboard lights on).

The Numpad is a good size and the arrow keys are full-sized too.

The Zeus came with Ubuntu Mate 16.04 (and the proprietary Nvidia drivers), which I tried to like, but managed to kill within about an hour by somehow removing the system's Python2 trying to install Python3.6.

Performance
-----------

No complaints here. Things which sometimes take a few seconds to open, like Firefox, Gimp and Godot, are all pretty much instant (Gimp takes about 1.5 seconds, probably the slowest thing I open).

Build Quality
-------------

Fantastic. I'm typing this review on the laptop's keyboard, and it's very comfortable. I find it a bit nicer than the laptop I have for work, which is a Lenovo E570. The laptop does not squish in when you press hard on the keyboard, and the screen does not wobble at all, despite the fact that my hands are quite quick and clumsy while typing (I'm used to big gaming keyboards).

I/O
---

Another thing which drew me to this laptop was the crazy assortment of I/O. It has an HDMI, Two Mini-Displays, Two USB-C and Three USB-A. It also has Ethernet, Microphone and Headphone jacks, as well as an SD card slot. This allows me to use two external monitors very easily, totalling three screens (when my workload gets crazy, which is rare, but I like having it as an option).

Battery Life
------------

Awful. About 2 hours, even after tlp and powertop, and all that jazz. You might get better with a different distro and/or desktop environment. I don't exactly travel much, so it isn't a problem for me, but it will put some people off no doubt.

The Big Downside - Dual Nvidia Graphics
---------------------------------------

Dual Nvidia graphics seems to be a place Linux struggles with tremendously. I'm a big fan of the bleeding-edge rolling-release distributions, and the latest version of Nvidia's proprietary drivers are messed up.

Some distros gave me only the laptop display without the proprietary drivers, and only external displays with it installed. I don't want to have to choose between the two, I should be able to use the laptop as a laptop, then plug a screen in and have it work.

The 16.04 version of the ubuntu-based distros have an older version of the Nvidia drivers, which allow me to do just that. OpenSUSE Tumbleweed and Arch, the two distros I love using, have the newest versions, which refuse to recognise the laptop screen.

**Update June 2018** - Nouveau stopped working with my external monitor on Arch. I took a long shot and installed the proprietary nvidia drivers, and they seem to be fine now. I still need to have a second monitor plugged in on boot, since the DM will only display on an external monitor, but once logged in I have display on both screens.

**Update Oct 2018** - Nvidia's proprietary drivers are working perfectly fine. Still on Arch and Plasma, with no use for the Kubuntu partition (I'll get rid of it if I run out of space, or get really bored). Due to the issue with the DM only appearing on an external monitor, I've decided to ditch them entirely and I just run ``startx`` manually. This means I can log in from the laptop display itself. Aside from that, all is good now.

Distro-hopping Adventures
-------------------------

When I first purchased the laptop I managed to kill the Ubuntu Mate 16.04 installation on it. When I got home from work I decided to re-install with 18.04. This distribution would not recognise an external monitor, so I put the proprietary drivers on it, and the laptop's screen itself then stopped working.

I went back to Ubuntu Mate 16.04 with the proprietary driver, but it absolutely refused to save any of my monitor configurations. Every time I booted the laptop I had to re-order my monitors and change the resolution on one of them. This annoyed me, so I tried my favourite distro - OpenSUSE Tumbleweed.

On booting the live USB it could not recognise my external monitors. I decided to remedy this after install with the proprietary Nvidia driver, which then completely killed X, meaning the machine would only boot into a TTY.

Kubuntu 18.04 did the same thing as Ubuntu Mate 18.04, forcing me to choose between the laptop's screen or an external screen. No good.

Finally, late at night and about to give up, I booted Antergos just to try Arch. To my surprise both external monitors came on when the live USB booted (this was the first distro which had done this) and they still all worked after installing (with Plasma 5). I can't install the nvidia drivers because that disables the laptop's display, but using Antergos with Nouveau is the best solution I have found.

I've settled with a dual-boot of Antergos and Kubuntu 16.04. I don't really use Kubuntu yet, but if I need to do some 3D game dev some time in the future I may need the proprietary drivers, and the older package available on the 16.04s seems to be the only ones which don't disable the laptop's display.

Would I recommend this laptop?
------------------------------

**Update Oct 2018** - If you want raw performance and don't care about Battery Life or loud fan noise, and you REALLY don't want to get a desktop, then this may be worth a look. Be prepared for some potential distro hopping / driver woes, since Nvidia on Linux is Nvidia on Linux. Now that drivers are sorted, I'm happy with this purchase, but a small part of me wonders why I didn't just buy a desktop.

**Original Answer** Probably not. At least not while the Nvidia drivers are so borked. If you are planning on staying with a 16.04 version of ubuntu and don't intend to use it away from a power source much, then it could be fine for you. If I weren't planning on beginning a 3D game project soon I wouldn't get something with a dedicated GPU at all, but I certainly can't recommend something with dual Nvidia graphics at the moment. Also, the fans can get very loud quite quickly. I've invested in a cooling pad to keep the noise down.

