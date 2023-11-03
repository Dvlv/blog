Title: Are Immutable Linux Distros Taking Your Freedom?
Date: 2023-06-11
Tags: Linux
Category: Linux

As someone who loves the idea of the Immutable / Atomic / Image-Based distros, I tend to find myself reading or watching a fair bit of discourse around them. A common sentiment you will come across
in this area is the idea that an Immutable distro is "taking your freedom" or "too restrictive" or "locked-down". I find this frustrating, because it's simply not true! In this post, I'll explain where
I think this idea comes from, and why I disagree.

Why do I care about this? Because I think Immutable distros are a fantastic idea, and I think people could benefit greatly from switching to one, but the loud voices proclaiming "stealing my freedom!!" are
leading people to avoid even trying them because they feel like they couldn't re-create their usual workflow or experiment with various packages as they like doing (whereas I'd argue the latter is typically
_easier_ on an Immutable distro, since you can very easily rollback if something goes awry!).

So, if you've heard about Immutable distros but think they aren't worth trying because "they're too restrictive", I urge you to read on and see that there's actually very little difference once you learn
the slightly-different tooling around package management.

## Why Do People Think This

### Phones
I believe people see the word "Immutable", and/or read a description which contains a phrase such as "The system is read-only" and their mind immediately goes to an iPhone (or even Android device).
By default, you will not have very low-level access to mobile operating systems, and so people will throw around terms like "locked-down" and "restrictive" in response. As someone who loves to
customise stuff, I don't disagree with this - there are many things about both iOS and Android which I would like to tweak but can't. 

However, this is _not_ how immutable Linux distros are! While they do aim to keep your "apps" separate from your "system", this does _not_ mean you can't alter the base system. 

This confusion is why I am not fond of the term "immutable" when referring to these distros, and a lot of people I talk to seem to agree. 

### Support

There's also the fact that most developers / communities around an Immutable distro will discourage users from installing too much onto the base system (especially when a flatpak is available), since it takes away the implied stability and tested-ness that comes
with an untouched system. Note that this is more relevant on distros which are not using OSTree, since they don't have the "factory reset"-esque abilities that it does. As such, the Silverblue community is much less anti-layering
than with others.

However, just because they may not recommend it, that doesn't mean that you can't do it! The tools are provided for you to do as you like with (unlike with a phone).

## So How Can I Customise An Immutable Distro?

In this section I will refer to the three distros I know the best - Fedora Silverblue, OpenSUSE Aeon, and Vanilla OS. I'll go over some of the things people may think they can't do and show how they are possible.

Note that I am using Silverblue to mean all `rpm-ostree` based distros, and Aeon for all `transactional-update` based distros.

### Installing Something In The Base System

#### Silverblue

To install something new, for example virtmanager, run `rpm-ostree install virt-manager`. This will add virtmanager to your base system, and it will receive updates as you update the system - just like a "regular" distro.

#### Aeon

You can install things with transactional-update, for example:  `sudo transactional-update pkg install sshfs`. 

#### Vanilla OS

Installing programs is done via ABRoot, e.g: `sudo abroot exec apt install sshfs`. 

### Removing Something From The Base System

#### Silverblue

You can remove things from the base image with `rpm-ostree override remove`. For example, to remove Firefox: `rpm-ostree override remove firefox firefox-langpacks`.

(Note that dependency-resolution is not perfect when removing things, so if you come across an error saying something to the effect of "can't remove package ABC 
because it is needed for ABC-Locale", then you will need to try again by runnning `rpm-ostree override remove ABC ABC-Locale`. That is why removing Firefox in the example above requires removing the langpacks too).

#### Aeon

Remove things with transactional-update, e.g: `sudo transactional-update pkg remove gnome-terminal`.

#### Vanilla OS

ABRoot is used again here, e.g: `sudo abroot exec apt remove epiphany`.

### Adding a Repo

#### Silverblue

Adding a repo is a manual process, but works like normal once done. Simply curl the repo's file into `/etc/yum.repos.d`. For example:

```bash
 curl https://pkgs.tailscale.com/stable/fedora/tailscale.repo | sudo tee /etc/yum.repos.d/tailscale.repo

 rpm-ostree install tailscale
```

#### Aeon

Aeon supports opening a shell for your next deployment and using Zypper inside it like normal.

```bash
sudo transactional-update shell

sudo zypper addrepo https://pkgs.tailscale.com/stable/opensuse/tumbleweed/tailscale.repo

sudo zypper ref && sudo zypper install tailscale
```

#### Vanilla OS

Vanilla also supports a shell for your next deployment:


```bash
sudo abroot shell

curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list

sudo apt update
sudo apt install tailscale
```

### Swapping Your Desktop / Window Manager

You can use the aforementioned package manager for each distro to just install your DE / WM and reboot into it, just the same as with a regular distro. Doing this on an Immutable distro is actually a benefit, as if you
are just experimenting and you decide you don't actually like it, you can just rollback and it's like you never installed it!

Some people are claiming they have gotten some WMs like QTile working from Distrobox, but from some basic googling I can't find any tutorials - but if you're down to mess with containers, it's a secondary option. 

## Actual Limitations

Immutable distros are not 100% swap-ins at the moment, there are a couple of things which you may struggle to do as on a regular distro.

### Swapping The Bootloader

Currently OSTree is dependant on GRUB, and I'm fairly sure the other Immutable technologies are also assuming that the system boots from GRUB (but don't quote me on that, the only one I know for sure is OSTree). 

If you swap out your bootloader a lot, or have some kind of problem with GRUB, then an Immutable distro is probably not for you at the moment.

### Snaps

None of the three distros mentioned in this blog post can run Snap packages. However, the rumored upcoming [Ubuntu Core Desktop](https://www.omgubuntu.co.uk/2023/05/immutable-all-snap-ubuntu-desktop) will be built around snaps, so there may soon be a single choice of 
snap-compatible Immutable distros.

### IDE Support

There are flatpaks for VSCode and JetBrains, but due to the sandboxing done by Flatpak, the experience is not necessarily seamless. Since Immutable distros encourage development to be done via containers, you'll
likely need to use some kind of container extension for a flatpak'd IDE _and_ use a workaround to give it permission to access the host's `podman`.
Some people are also not fond of the fact that VSCode's container extension is proprietary.

Personally, if I wanted VSCode on an immutable system I would just layer it as normal. If I wanted JetBrains I would use their standalone JetBrains Toolbox app, which installs the IDEs as executables in the user's
home directory, meaning they are separated from the base system anyway.

Since I do my work in Virtual Machines by just sshing in and running neovim, this is not a problem for me at all.
