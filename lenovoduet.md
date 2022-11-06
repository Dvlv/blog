Title: The Lenovo IdeaPad Duet makes a nice Linux(ish) 2in1
Date: 2021-07-17
Tags: Review, Hardware
Category: Reviews

I'm quite surprised that I would ever end up desiring a ChromeOS device, but it happened. I'm not sure when, or why, but it did.

## Personal Backstory

As a Youtube addict, I've always loved the Lenovo Yoga tablet line. The built-in kickstands, the fantasic speakers, the huge battery life. They're pretty much perfect video-watching devices.
Despite this, there isn't really a lot of love for Android tablets. Aside from Samsung's Galaxy Tab range, I can't think of many others, and I know quite a few people who scoff 
at the general idea of an Android tablet. I have been wondering for a while if "the future of Android tablets" would end up being ChromeOS. 

I believe the first ChromeOS tablet I saw (and considered buying) was called the HP Chromebook X2. It was super expensive though. I also considered the Pixel Slate, but that was even 
dearer, and had pretty mixed reviews. My interest died off a little while later, and I ended up buying a Lenovo Yoga Tab Smart, [which I wrote a review of here.](https://www.dvlv.co.uk/lenovo-yoga-smart-tab-review.html)
TL;DR I needed an upgrade from the Yoga Tab 3 Pro because it was super out-of-date and slow, but it didn't offer anything really worth buying aside from that.

Fast forward to this year, and I somehow found out about the Lenovo IdeaPad Duet. It's a 10inch tablet running ChromeOS which comes with both a kickstand case and a keyboard, and it's
significantly cheaper than the two aforementioned devices - £259.99. 

As a Linux nerd who also loves tablets, I've been interested in a Linux tablet for who-knows-how-long. Doing some research about the Duet, I realised how far ChromeOS has come toward
being almost-full-linux, and I was sold. I've had one for about 3 weeks now and I've used it a ton (I'm writing this post from it right now!)

## Hardware / As a Tablet

The included kickstand case (it's more of a back cover than a "case", but hey ho) means it's pretty much as usable as the Yoga Tabs I'm used to. It stands surprisingly well on
my bed (as in, directly on the matress), and on my legs when I lay in bed. It's probaby better than the Yoga Tabs in that respect, as they tend to fall forwards due to their stands
being perpendicular to the base, whereas the cover of the Duet is tent-shaped. However, it doesn't stand too well on the back of my computer chair.

The speakers are stereo and on the top, compared to the front-facing of most Yoga Tabs (and the side-facing of the Yoga Tab Smart). They're certainly loud enough and 
I have no complaints with their quality for Youtube videos. The good thing about them being on the top is that you're never going to cover them with your hands while 
holding the tablet in landscape mode. 

A drawback with tablet-mode when treating this as a Linux device is that the built-in on-screen keyboard doesn't support linux programs. This means you need the keyboard attachment (or a USB keyboard) to type in a Linux program. Programs like Onboard don't seem to work either. So, although I can install Firefox in an attempt to avoid Chrome (which I don't use on any other machine I own, not even my work laptop!)
I can't type into it when in tablet mode.


## The Keyboard / As a Laptop

I'm surprised at how much I like the keyboard. I think I would go as far as to say it's a nicer typing experience than my Thinkpad T490s. I'm typically a mechanical keyboard snob,
and I find I miss a LOT of keys on the T490s. A 2in1's mini detachable keyboard is hardly perfect, and I sure wouldn't want to do serious programming work on it, but I am
doing a writing project in my spare time at the moment, and I've done almost all of the writing on the Duet's keyboard attachment without significant troubles.

For the small bit of programming I've done, I've found the miniature Tab key quite annoying. The backspace key is also quite small, and right above it is a button to lock the screen
which I've hit a lot. Luckily you have to hold it for a couple of seconds for it to actually lock, otherwise I think I would have a lot more hatred for the keyboard. There's also no delete key, but you can use alt+backspace to delete ahead.

The battery life is actually pretty great. I would guess I use the Duet in roughly 2 hour bursts _almost_ every day, call it 6 days a week. It's charging right now, for the second time
since I bought it (about 3 weeks ago). That's pretty reasonable I'd say. The official specs claim 10 hours, but for my usecase (mostly the Linux terminal running Vim, with the
occasional procrastination break on the Youtube app, and sometimes Youtube music running minmised) it's _loads_ longer.

The kickstand cover obviously doesn't offer as many viewing angles as a proper laptop, and there have been a few times I've wished I could get a better screen angle, but it's
never really been a _huge_ annoyance.


## As a "Linux" device


I mentioned above that I've installed Firefox on this Chromebook (blasphemy, I know) and, to probably nobody's surprise, it runs awfully. Scrolling is stuttery and transparency doesn't work (even though I enabled the GPU passthrough flag) so menus all have thick black boxes around
them where (I assume) there is supposed to be box-shadow. 

I also don't think you can change the GTK or Qt themes, but the provided theme (which is based on the look of ChromeOS itself) is mostly inoffensive.

Other things I've installed include KeepassXC and Calibre. Both seem to work perfectly fine. 

If you're happy in a Terminal when you Linux-it-up, you will be fine on ChromeOS. Enabling the Linux beta gets you (from what I can gather) a VM which runs a container of Debian
 10 and is SSHFS'd into ChromeOS so you can share files between the two easily. You then use the terminal with `apt` to install stuff, and it automagically appears in 
ChromeOS's app drawer.  

It's not actually magic though, ChromeOS seems to just pick up `.desktop` files from `/usr/share/applications` which are marked `terminal=false`. Figuring this out allowed
me to edit `firefox.desktop` to add `MOZ_USE_XINPUT2=1` to the `Exec` command so that it starts with touch enabled.

Judging by some Youtube videos I've seen, if you install Plasma 5 you can get "proper" desktop Linux going on ChromeOS. I've tried LXDE, XFCE4 and Gnome and I can't get any
of them to start X, so it looks to be only Plasma which can do that (I haven't tried since it's like 5GB to download Plasma and my Linux container is only 7.5GB). Maybe one
 day Ill get bored enough to try it, but for now I'm happy with keeping the keyboard plugged in to use Linux programs.

## Overall Thoughts

I'm very happy with this purchase. The writing experience is good enough that it hasn't put me off my writing project - it runs Vim in a terminal perfectly fine. The Youtube app
functions as well as it does on my Yoga tablets (probably better, even) and it boots so quickly that it's not even worth sleeping the device, I can do a full shutdown every time
 I'm finished with it.

The tablet works perfectly with the Lenovo Dock 2 I have for work, so I could plug it into the monitor, trackball and mechanical keyboard I use for work if I wanted to. Unfortunately
 it can't handle the 3440x1440 ultrawide, it can only do 1440x900 @ 60Hz on that monitor, but it handles my 1080p TV fine. 

For anyone wanting a small, portable machine with a good battery life for mostly living in a Linux terminal, I would recommend considering the IdeaPad Duet. It's not perfect by
any stretch, and if you're mostly watching video I'd still suggest a Lenovo Yoga Tab over the Duet, but it fills that hankering for something _a bit more_ than an Android tablet can offer. 
