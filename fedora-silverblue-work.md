Title: Using Fedora Silverblue for a more secure development machine
Date: 2022-04-10
Tags: Linux
Category: Linux

I'd been having a few problems with my three-year-old install of OpenSuse Leap on my work laptop, and this seemed like the perfect excuse for a clean re-install. So, I backed up my important files and replaced the drive with a fresh, encrypted install of Fedora Silverblue.
There have also been a number of recent reports of trusted NPM packages being turned into malware by their authors. In general, running unvetted code from outside sources is a security hazard, but using automated package managers comes with the job of a developer, so I want to
try and make sure I mitigate the risks as best I can.

Hopefully this new set-up should be a bit more secure than my previous attempt, and I will explain why I belive that in this post. But first, a brief overview of how the new install is set up.

For reference, I am a web developer who mainly uses the laptop for coding in Python and Javascript. We deploy on regular VMs, so no cloud-platform-specific tooling is required. Unfortunately, my workplace uses Microsoft AD and Office 365.

# The Set Up
The Silverblue install is running the default Btrfs configuration with LUKS encryption enabled.

## Software
I still find myself using a variety of ways to install things on Silverblue. I guess that's just one of the joys of Linux! When using software on this machine, I want to keep as "vanilla" as possible, and avoid running code from outside of official Fedora sources whenever possible.

### The Base Install

I have layered the following packages on top of the default install:

- evolution
- evolution-ews

For reading my Microsoft Exchange emails.

- libvirt
- libvirt-daemon-config-network
- libvirt-daemon-kvm
- qemu-kvm
- virt-install
- virt-manager
- virt-top
- virt-viewer
- fuse-sshfs

For virtual machines (more on this soon).

- keepassxc
- neovim
- tilix

For passwords, my favourite editor, and a dropdown terminal.

- gpaste
- gpaste-ui
- gnome-shell-extension-gpaste
- gnome-shell-extension-dash-to-dock
- gnome-shell-extension-appindicator
- gnome-tweak-tool

To make gnome bearable (I much prefer Plasma, but Fedora Kinoite is currently broken for GMT).

### Toolbox software

I currently use two toolboxes, one for Brave Browser (the browser I do my work in) and one for Google Chrome (which I occasionally have to use).

Brave was installed using the steps outlined on their install page, as normal.

Google Chrome was installed using the RPM file they provide. I had to install Chromium via dnf beforehand in order to bring the correct dependencies in. It seems installing an RPM doesn't do this by default.

A nice advantage to having browsers installed in a Toolbox is the ability to update them with `dnf update` without having to update my entire machine. I tend to do machine updates once every week or two, but I have been updating my toolboxes every couple of days.

### Flatpaks

I have installed GIMP and LibreOffice using Fedora's built-in flatpak repo. I have not yet needed to enable flathub.

### AppImages and binaries

I run both DBeaver and Slack as regular binaries. I can download DBeaver from GitHub, and I use `rpm2cpio` to unpack Slack.

I have Insomnia and StandardNotes running as AppImages.

## Coding Work

All of my projects are set up on a KVM Virtual Machine running Oracle Linux 8 (the same as our servers). 

My oracle VM contains a single install of Postgres 14, since older projects all seem to be forward-compatible with the latest version.

I use Pyenv to manage versions of Python (I currently need 3, but hopefully soon we should be upgrading everything to the latest).

### Pretending the VM is my host system

For compatibility reasons, it's been good to have things set up to behave as if they're part of my host system. 

To achieve this, I use `sshfs` to mount my projects in `/home/Dvlv/Work` on the VM to `/home/Dvlv/Work` on the host. For security, I add the `-o noexec` flag to the `sshfs` command, so that nothing from the VM can execute on the host.

All of our sites also use SSO for their login-guarded sections, and this SSO operates on a domain whitelist. This list obviously includes "localhost" for development, and I don't want to add the IP addresses of VMs I create to it.
To mitigate this, I use ssh tunneling to map ports from my local machine to the VM. This then allows me to visit `http://localhost:5000` as if the project was running locally.

### Editing

This is probably the most inconvenient part of my set-up, but it's due to my own preferences. My favourite way to write code is still Neovim with CoC, but this relies on having NPM installed and running unvetted node packages - the main thing I wish to avoid!
Since I need NPM for my work anyway, I have my vim set-up on the VM, and I need to ssh in before I can begin coding.

My workplace also use Git, which requires me to have GPG and SSH keys. I want to keep these away from the untrusted code running on the VM, so I have to switch to a local terminal in order to perform any Git actions. 
This means no using fugitive.vim to manage my git commits any more. Well, I can use it to view diffs and stage files, but I have to switch to a local terminal to perform the commits and pushes.

# Why I believe this is more secure

- **NPM and Pip are secluded** - Running untrusted code from these two platform is unavoidable. However, if more NPM (or python) packages become malware they should not be able to
access data from my browsers, write anything to my `/home` directory, access my SSH or GPG keys, or overwrite any important documents. 
- **Immutable System Dirs** - Fedora Silveblue has certain directories, including `/usr`, as read-only. This prevents malware from sneaking anything into the binary directories there.
- **Encryption** - No-brainer, an encrypted install is better-protected if the laptop is lost or stolen.

Some readers may also be expecting me to put something about Flatpaks being sandboxed here, but meh. I've not personally seen a demonstration of a Flatpak's sandboxing stopping
some kind of attack, and I am only using them because they're the most convenient way to install a couple of the GUI programs I need for work. I'm not a "hater" by any means though, I'm
very open to being shown the advantages of Flatpaks, I just haven't personally come across any.

# How this could improve

My original plan was a separate VM and Postgres install for each of my projects. This would further confine NPM and Pip packages to only being able to destroy the VM of the project where I happened to pull them in.

However, I was unable to think of a backup strategy with all of my work spread across separate machines. I currently `rsync` my `Work` folder, along with a few others, and then `tar` them all up.
I currently look after 8 separate projects, so I would have to run all 8 VMs simultanously then mount each one before I could use my `rsync` script. Obviously, 8 separate installs also used up a _lot_ of storage space, and my work machine only has a 250GB drive.

I have also been thinking about each project having its own user on my VM, so that a rogue library from one project should not have write access to any of the others. 
Due to my need to use Neovim to edit files, this would mean each user would need to share the `init.vim`, as well as all libraries needed for plugins, and any configuration directories. I am confident
this can be achieved with the `XDG` environment variables, but I haven't had enough free time to really look into it in much detail. It's definitely on my mental TO-DO list, though. 


