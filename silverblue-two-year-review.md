Title: Thoughts on Fedora Silverblue After Almost Two Years
Date: 2023-11-02
Tags: Linux
Category: Linux

I've been using Fedora Silverblue daily on my work laptop since around the release of version 35 (2nd Nov 2021 according to Wikipedia), and on my
main personal machine since I bought it (Jan 2022). This has given me long enough to form an in-depth opinion of the distro, so I will put my thoughts
down in this post for anybody curious.

This is just my personal experience and opinions, which may well differ from those of others. One important thing to note is that I've only ever
used it on laptops with Intel processors with integrated graphics, so I can't speak to anything Nvidia or AMD.

## Background
My current job gives developers free-rein to use whatever they want on their machines. From when I joined the company in 2017 to my Silverblue switch in 2021, I was using OpenSUSE. My first laptop was Tumbleweed, and my second (bought in 2019) was Leap.

I made the switch to Silverblue for two main reasons: 

- My Leap install would sometimes (once a fortnight maybe) crash completely, forcing a hard-reboot. I think it was often while playing a full-screen video (or screenshare / videocall). 
- OSTree sounded like a very intriguing technology.

Hesitant to switch blindly, I spun up a VM of Silverblue and installed all of my dev tooling inside that, then ran it fullscreen for a while, pretending it was my host system. This gave me the reassurance that it would be suitable for a bare-metal install.

## Experience
This is probably the main point I have to make with this post - **Fedora has *not* been a stable distribution**. 

I would go as far as to say that if I had jumped to regular Fedora Workstation, I would *definitely* be back on OpenSUSE by now. 

Let's go through the show-stopping bugs I've encountered in my 2 years on Silverblue.

### F35 - VirtManager Wouldn't Run Anything
My entire development workflow is done via KVM virtual machines to avoid having anything on my host system (sort of like how most people would use containers I suppose). Shortly after the release of Fedora 36 I updated my install of Fedora 35 and couldn't run any VMs.
This was the reason I jumped to 36 so shortly after the release (I was planning to wait a month or so to ensure any bad bugs were ironed out). 

### F36 - System Wouldn't Update
As far as I know this affected everybody running an OSTree version of Fedora - <a href="https://github.com/fedora-silverblue/issue-tracker/issues/322" target="_blank">an issue prevented GRUB from regenerating</a> causing the deployment to fail almost-silently. Anybody relying on automatic updates who hit this could have ended up with a system that did not update for a
very long time (as long as it took them to realise they were never updating).

We would get questions about this pretty much every day in the Matrix room for a month or so.

Luckily a solution was found fairly quickly, but it involved a manual fix if you were one of the unlucky ones who ended up affected.

### F36 - System Crash on Removing USB Devices
Definitely the worst thing I've encountered during my time with Silverblue. I use a laptop plugged in to a Thinkpad Dock, and removing the laptop from the dock caused a *hard* crash, mandating a hard-reboot. 

"Just don't unplug the laptop then" sounds like a manageable response, *but* switching the power off to the dock counted as removing it! Normally I put the laptop to sleep Monday - Thursday so I don't have to re-open everything each morning, but I switch the power outlet off
since there's no need to power my monitor or speakers overnight. This caused the dock to disconnect and the laptop to crash, meaning I had to shut it down every night while this issue was happening.

To better imagine my annoyance - it took me a few days to realise it was unplugging the dock that caused the crash (I initially thought the machine just wouldn't wake from sleep) and I also had no idea how to check if the problem had been fixed other than by
updating, rebooting, removing the dock and seeing if the machine crashed, then rolling back when it did.

I lived with this for about 3 weeks until realising that upgrading to 37 would fix the issue, despite the fact that 36 was still supported.

### F37/38 - Wireplumber Malfunctioning
During my use of 37, I'm not sure how I would trigger it, but Wireplumber would go haywire pretty much every day. 

I almost never hear the fans on my work machine, so it made the issue easy to spot:

- My fans would ramp up and become very loud
- My CPU would spike to ~15% (Which may sound low but it's typically around 5-10%)
- The temperature indicator would show 80 - 90 degrees (it's typically 45-55).

Opening the System Monitor would show Wireplumber at the top using ~9% CPU, and force-killing it would temporarily resolve the issue (usually until the next day).

I tried upgrading to 38 to solve this, but the same issue was there as well. This lasted probably 3 or 4 weeks. 

### F38 - GNOME Crashes on Suspend
For the last week or so I've been waking my laptop every morning to a blank GNOME session. My work VM is still running, which is actually worse, since I have to SSH back in and manually kill the process running a web server, so that I can bind back to the same port.

This defeats the purpose of sleeping the machine entirely, so I've been shutting it down fully.

### F38 - Laptop Won't Connect to a Screen
I updated my personal machine from 37 to 38, since 39 is now out. It booted fine once, but from then on would not connect to my external screen (plugged in via a Thinkpad Dock).

The machine would boot but I got no picture on the monitor. If I closed the laptop, its internal screen would go black and never wake back up, forcing a hard-reboot.

After about 6 attempts at getting the machine to work with the monitor, I gave up and removed the laptop from the dock and rebased to 39 - this fixed the problem, and it now connects
perfectly as before.


## The Big "However"
It must sound like I've had a pretty bad time with Silverblue, which I suppose I have, so it may seem strange when I say that I've still got it on both my work machine and my personal laptop.

The reason is simple - OSTree. 

Every time (well, almost) there's a problem I can reboot and load the previous problem-free deployment. This has made coping with the problems *much* easier, and I lose effectively zero working time because I don't have to wrestle with any of the issues to get my work done - I just
reboot and continue as before.

As said above, assuming Fedora Workstation is the same as Silverblue but without OSTree, there's *no way* I would be able to use it on a work machine, since there would be no zero-effort rollback mechanism when these problems occurred. 

## Concluding Thoughts

- **Do I like Fedora Silverblue** - Yes.

- **Would I recommend Fedora Silverblue to other developers?** - Probably, depending on their familiarity with the Linux Desktop.

- **Would I recommend Fedora Silverblue to someone new to Linux** - If they were a friend / family member who I was in consistent contact with to provide support - Yes. If they were a random person on the internet - No.

- **Will I continue to use Fedora Silverblue on my Work Machine** - Probably. I don't see any reason to move off at the moment (barring another showstopping bug).

- **Will I continue to use Fedora Silverblue on my Personal Machines** - Probably. I have two main machines that are fully-functional, the more powerful one (Lenovo Yoga Slim 7i Pro) is still on Silverblue, and I don't see that changing. My T490s (ex-work-machine) is now on OpenSUSE Tumbleweed.


