Title: Why All Developers Should Code in a Virtual Machine
Date: 2023-11-04
Tags: Linux
Category: Linux

As I've talked about in another post, my work setup involves running an Oracle Linux 8 VM via VirtManager to install and run all of my projects. My reason for this is, in a word, "security", and in this post I'll go over exactly how and why I use this set-up and why I think it's the way _all_ developers should be working (with the obvious caveat of having a powerful-enough machine).


## Why Use VMs?
As a developer, you are likely installing many packages from third-party repositories such as `npm`, `pip`, `cargo`, and the like. These packages can be written by anyone, and can do absolutely _anything_ on your computer. 

There is no shortage of news articles about malware found in [npm](https://www.zdnet.com/article/hundreds-more-malicious-packages-found-in-npm-factory/), [pip](https://arstechnica.com/information-technology/2023/02/451-malicious-packages-available-in-pypi-contained-crypto-stealing-malware/), [cargo](https://blog.phylum.io/rust-malware-staged-on-crates-io/), [nuget](https://thehackernews.com/2023/10/malicious-nuget-packages-caught.html), etc. These are just random news articles found by googling "Malware found in `$repo_name`", and there are plenty of results for each. 

There are also YouTubers making videos about how they have run into this exact issue, such as [this one by ThioJoe](https://www.youtube.com/watch?v=g2DO2Xhccq8).

For this reason, it only makes sense to keep as much untrusted third-party code as possible away from important files, such as SSH / GPG keys, your browser cache folder (`~/.firefox` on Linux, etc) and any work credentials such as `~/.aws` and the like.

The way to achieve this is isolation - keep your development _away_ from your home directory.


## How I Set Up My Work Machine
Essentially, I install any Linux distro which can run VirtManager (which I imagine is most of them) and then create a VM running the same OS as production, where possible. In my case, that's Oracle Linux 8.

Inside this VM lives anything I will need to use while coding, including `pyenv`, `nvm`, `ripgrep`, etc.

### The Host
I try and keep as little as possible on the actual host, for reasons of both stability and reducing potential attack surface. I find Fedora Silverblue a good fit for this usecase, since it's immutable and atomic, which generally nudges users towards keeping things away from the host. 

Aside from VirtManager, I have Evolution installed to read my work emails, a couple of web browsers (since my job is Web Developer), DBeaver to explore databases, and Bruno to test APIs. I prefer to use web-apps of things which will run in a browser, such as Slack.

### Coding and Running Servers
As a neovim user, it's as simple as `ssh`ing in to my development VM and running `nvim` directly.

To access webservers started in the VM, I use ssh tunnelling to bind the ports back to `localhost`:

```bash
ssh -N Dvlv@192.168.122.55 -L 8080:192.168.122.55:8080
```

With the above script, and running my flask servers on host `0.0.0.0`, I can visit `http://localhost:8080` from the host and it will all work.

### Version Control
Checking code into Github requires the use of my SSH and GPG keys, which I've mentioned are kept away from the VM. Therefore, I need a way to get the code back to my host so that I can push it to version control.

To do this, I have a script which will mount the VM's `Work` directory via `sshfs` but disallow executing anything:

```bash
sshfs -o noexec Dvlv@192.168.122.55:/var/Work /home/Dvlv/Work
```

Now I can just move to the relevant folder in `~/Work` and run `git` commands as normal, but nothing inside the mounted folder can execute anything on my host.

## How I Set Up My Personal Machines
I'm a bit lazier with my personal devices, and they aren't as beefy as my work machine.

I install VirtManager on the host as before, but I then install a graphical linux distro, usually Fedora Workstation. I run this VM in fullscreen, and use it like somebody would use their regular install - just install things onto it via the package manager.

Most of these VMs don't have my neovim setup added, I will just install either VSCodium or a JetBrains IDE and use it as normal.

I typically have a separate VM for each programming language - one for Python, one for Rust and one for C++ currently. 

Once the distro is installed and updated, and my programming tools are installed, I will take a snapshot of the VM so that I can cleanly revert to "day 1" if there is ever any need - although I rarely will ever update a VM once I am working on a project.

All tooling is thereby confined to its own VM, as well as any web browsing (i.e. googling for how to do stuff in the relevant programming language). 

On one machine I use the `sshfs` setup as above, but on another I just tend to `rsync` the VM's project folder down to the host and use `git` as normal from inside. This is slightly less convenient however, as if I work on that project from another machine, I have to remember to `git pull` then `rsync` the changes back from the host to the VM before I resume coding.

## Negatives of this approach
As alluded to, this requires a machine with enough spare RAM and Disk to run an extra VM. If your machine has 8GB RAM or less, this may not be very practical.

Also, due to my use of `ssh`ing and running neovim, there isn't a display server or clipboard agent running in my Oracle VM. This means if I need to copy-paste a code snippet to a colleague, I typically have to `cat` the file from the `sshfs` mount on the host and then copy-paste that. If I used a graphical set-up like on my personal machines, this would be no issue as the clipboards would sync.

## Can I Use Containers for This?
Possibly, but I have no personal experience with setting up and isolating containers. 

## Isn't This Just Qubes OS?
Essentially yes, but with the comfort of being doable on any distro (or even MacOS via something like [UTM](https://mac.getutm.app/)).
