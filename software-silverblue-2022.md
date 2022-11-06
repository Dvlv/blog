Title: My WebDev Setup on Fedora Silverblue
Date: 2022-11-05
Tags: Linux
Category: Linux

This post is a kind of follow-up to <a target="_blank" href="https://www.dvlv.co.uk/using-fedora-silverblue-for-a-more-secure-development-machine.html">my previous post about my work machine</a>, but with the changes that I've made since first setting up Silverblue, and a focus on the particular software I use for my job (and some details on how I use it, and how I've installed it).

## Software

### Editor / IDE

**Neovim** is my editor of choice. I'm still yet to find a "proper" IDE with good enough vim emulation to make a switch, so for now I'm staying.
   
Notable Plugins:

- **CoC** - Language server for Neovim. I've had a play with the native LSP in Neovim but I just find CoC easier.

- **neovim-fuzzy** - Quick-opening of files using `fzy` behind-the-scenes.

- **semantic-highlight.vim** - Can't code without this anymore. This gives each variable / function its own colour, making scanning your eyes over code much quicker.

- **ack.vim** - Provides searching over the entire project, similar to Ctrl-Shift-F functionality of graphical editors.

### Terminal

**Tilix** is brilliant - it's got a quake mode, so I can drop it down at any time with F2, and it also supports both vertical and horizontal splitting, plus multiple tabs.

**Yaquake** does the same for me when on a Plasma desktop, but I can't use it as a "regular" terminal for coding, which I can with Tilix. 

### Databases

**DBeaver** is the best database explorer I've found, as I have to connect to a mix of both MySQL and Postgres databases. 

### API Tools

**Insomnia** is my current tool of choice for testing APIs. However, it's currently broken on Fedora, so I'm forced to use an older version inside flatpak. Next time I'm working on an API, I'll give **HTTPie Desktop** a try.

### Communications

**Evolution** is my email client. My workplace uses Exchange, and it seems to be the only option for Linux.

**Slack** is my workplace's chat tool of choice. Linux compatability is fine, though to share your screen on Wayland currently requires using it in a browser which has Pipewire RTC enabled.

### Notes

**Standardnotes** is amazing - E2E encrypted notes synced across devices. The free plan only allows for plaintext notes, though I find that's perfectly fine for quick jottings.

### Browsers

**Brave** is my choice for doing my work. I mostly make internal tools, and the people I make things for use Chrome, so I need to use something based on Chromium. Brave has some nice privacy-focused features, and definitely has the best icon!

**LibreWolf** is where I do my non-work-related browsing (HackerNews, checking the weather, etc.). This would normally be Firefox, more on this below, but tl;dr I need media codecs for YouTube.

### Office

**LibreOffice** handles the rare cases in which I have to interact with office documents. I _do_ have a Windows 10 VM with MS Office installed just in case, but I've not had to use it.

### Images

**Gimp** handles any image editing I have to make myself. I've been using it since before I even knew what Linux was.

**Photopea** is my go-to if I get sent a `.psd` file from a designer, it handles them much better than Gimp from my experience.

### Honorable Mentions

**Virt Manager** powers all of the VMs I run, notably the Oracle 8 VM I do my work in, and the Windows 10 VM I keep around for MS Office. 

I have a bit of a tendancy to experiment with random software during quiet periods, and this can end up filling my work machine with random libraries and such. Now I'm on an immutable distro, I can use either podman containers or VMs to avoid this!

**Gpaste** is a clipboard history manager. Another thing I can't work without any more.

**Jira** is my workplace's choice of task-management software.

**GitHub** is where we store our code.

**AWX** handles CI/CD throughout the company.

## Packaging Methods

Since Fedora Silverblue is immutable, software is not always just installed from the package manager as it is in other distros. I'll list here my methods of installing different packages I've mentioned above.

### Layered (rpm-ostree)

- Evolution (needed to integrate the EWS calendar with the Gnome Desktop Calendar)
- GPaste (as well as my other Gnome extensions, as I think it offers better guarantees of compatability)
- Virt Manager (I tried the flatpak of Gnome Boxes but it's much less powerful)
- Tilix (terminals kinda need to be native) 

### Flatpak

- Insomnia (as mentioned, all other versions do not work on current Fedora)
- LibreOffice
- Gimp
- LibreWolf - The inbuilt Firefox doesn't play ~75% of YouTube videos due to needing codecs. The suggested solution is to replace it with the Flatpak version, but I don't really want to remove base packages (nor do I want 2 Firefoxes) so I went with the Flatpak of LibreWolf instead. It comes with uBlock Origin and a bunch of the privacy-related changes I would have made anyway, which saves me quite a few clicks.

### AppImage

- Standardnotes
- HTTPie Desktop
- VSCodium (I use this very occasionally for C/C++ development just because I haven't bothered setting up CoC)

### Toolbox

- Brave
- Chromium
- Google Chrome (you can never have too many browsers!)

### Tarballs / Extracted RPMs
- DBeaver
- Slack
