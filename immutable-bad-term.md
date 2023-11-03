Title: Why I Dislike the Term "Immutable Distro"
Date: 2023-10-14
Tags: Linux
Category: Linux

Immutable Distros are something I've written quite a lot about recently, but I'm really not fond of the actual term. I use it because, for better or worse, it's become part of the linux vernacular. However, it seems like most people who are actually involved
in the immutable distro "scene" don't actually like the term - and I'm one of them. 

In this post I'll dump a few reasons, in no particular order, why I think the term isn't really a good idea.

## It Makes People Think of iPhones

A common scenario I seem to experience is:

- Someone hasn't heard of the term Immutable Distro before.
- They search the web and find a definition somewhere.
- The first sentence in said definition is something like "Immutable Distros are distributions which have their root system mounted read-only".
- Their mind immediately jumps to a mobile operating system, such as iOS.
- They exclaim "I don't want my desktop to be as locked-down as an iPhone!!!".
- They refuse to even try an immutable system.
- Optional: They make some kind of YouTube video / podcast / blog about how "immutable linux bad, taking your freedoms!".

This is annoying to see, as I mentioned in <a href="/are-immutable-linux-distros-taking-your-freedom.html" target="_blank">a previous post</a>, since I think it puts people off for no valid reason.

The filesystem may be _mounted read-only at boot_ but it is not inaccessable like on a mobile OS. There are very very few things you can do to customize a "regular" distro that you can't on an immutable one, and these are usually due to technical challenges not yet overcome rather than deliberate design decisions.

## The Read-Only Filesystem is an Implementation Detail

If you ask most people why they would use an immutable distro over their "regular" counterpart, the answers would typically be the following:

- You can roll back with a simple reboot in the case of a breakage
- You know other people in "communities" (Matrix, Forums, etc) have a similar, if not identical, setup. In other words, systems are much more likely to be bug-for-bug compatible
- You can run updates without touching the running system, and these updates will not complete if there is a failure. Meaning:
    - There is no danger of a program which is split into 4 distinct packages from having 3 of them update and 1 fail, leaving the whole thing unusable.
    - If you have a power cut during an update, your system is _significantly_ less likely to be left in a broken, unusable state.

Read-only-ness is not mentioned above, because it isn't actually what's important. What's important is more accurately described as "Atomicity" - the reduction in liklihood of being left in a fail-state. 

This is why I believe that Immutability is merely an implementation detail - it's something done to make it clear(er) to users that the base system should not be arbitrarily modified, since it would no longer provide the guarantees.

Granted, some people (my past self included) will tout that an immutable system is "more secure" since malware can't drop things into `/usr`, and such. I believe this to be somewhat misguided, as malware can still drop malicious things in `/usr/local` or `~/.local/share/`, or `/etc/systemd`, or any other place a user would have write-access to.
The thing which makes mobile OSes secure is more the very rigorous sandboxing of the application layer, rather than the fact that the system is read-only.

## It Causes Incorrect Assumptions

Two main things I see assumed about Fedora Silverblue specifically are:

#### You can't edit `/etc`

People come into Matrix and ask something like:

> I'm trying to configure xyz-software and it says to etc `/etc/xyz.conf`, how do I do this on Silverblue, because the system is read-only?

The answer to this is: **you edit `/etc/xyz.conf`, since `/etc` is not read-only**.

People seem to read that a system is "immutable" and assume that they can't change _anything_ on it. This is, of course, not the case, as being unable to edit things in `/etc` would make a desktop distro basically useless.

#### You Can't Use [some_window_manager]

People will also becry that they can't use something, let's say `qtile`, on Silverblue, because it comes with the GNOME Desktop. This is also false. Nothing stops someone from running `rpm-ostree install qtile`, then rebooting and switching to Qtile in the Display Manager.

It's true that it's more frowned-upon to then _remove_ GNOME from the system, which is likely what someone would do had they began from Fedora Workstation, this doesn't actually stop them from using Qtile day-to-day. It also provides them with a supported backup environment
should a bug make Qtile unusable at any point. 

## What's a Better Term?

I think "Atomic" is probably the best term I can come up with, since it's the main draw of these distros. It draws attention to the reliability and rollback-ability of them without putting undue emphasis on the fact that some root directories are mounted read-only, which I maintain is purely an implementation detail, not a benefit.

"Atomic" is also the direction the Fedora project is currently gravitating towards, see [this](https://discussion.fedoraproject.org/t/creating-the-fedora-atomic-desktops-sig/90757) and [this](https://discussion.fedoraproject.org/t/i-am-ok-with-fedora-atomic-brand-now/86235/17). I think overall this is a step in the
right direction, if their goal is to get Silverblue-and-pals out in front of the "standard" versions some time in the not-too-distant future.

Overall, it will definitely take some time for the "Linux Community" to adopt a new term in place of "immutable", since it's a relatively recent term itself, but is gaining traction rather quickly (and I'm probably not helping by blogging about it so much!). We just need this inertia to swing in a new direction before
it solidifies too much. At the time of writing, Ubuntu Core Desktop is a rumored upcoming Immutable Distro, with very little known about it aside from the existence of a public Github repo. It will be interesting to see what terminology they use to brand it if it's released to the general public.







