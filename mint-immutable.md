Title: Making Linux Mint a Pretend-Immutable Distro with Btrfs and Timeshift
Date: 2023-03-18
Tags: Linux
Category: Linux

I'm definitely all-in on the new-ish trend of Immutable Distros, and I really think it will be The Future™. At the time of writing there are 2 big players in the immutable game - [Opensuse MicroOS](https://microos.opensuse.org/) and [Fedora Silverblue/Kinoite](https://silverblue.fedoraproject.org/). These are both essentially bleeding-edge distros, meaning bugs in the newest Kernel versions keep getting to my work laptop (with a 12th gen Intel CPU, which seem to be cursed on Linux). I'm interested in something a bit slower-moving and "stable".

I'm aware of [Vanilla OS](https://vanillaos.org/) and [Blend OS](https://blendos.co/), but both are very new projects, and I'm not sure are appropriate for a work machine.

I heard someone mention the integration with Linux Mint, Btrfs, Timeshift and Grub, and became intrigued. In this post, I'll show off how I've set up a system which works similar-ish to MicroOS but with Linux Mint as the base.

## Installing

(Standard disclaimer: Partitioning and formatting disks can lead to data loss, only follow this guide if the drive you are using does not contain anything you want to keep!)

Flash the ISO to a USB and boot Linux Mint as normal. I used [Fedora Media Writer](https://developers.redhat.com/blog/2016/04/26/fedora-media-writer-the-fastest-way-to-create-live-usb-boot-media) for the first time when doing this project, and I must say I'm very impressed - it's on the simpler side like Balena Etcher but it's not Electron!

Start the installer and follow it as normal up to the "Installation Type" section. Here choose "Something Else" and Continue.

Find the disk you want to install to in the table. If it contains partitions you no longer want, select it and click "New Partition Table" to clear them.

Click the **+** button and create a 1GB Ext4 partition mounting at `/boot`.

Click the **+** button again and create a 1GB partition of type "EFI System Partition".

The last partition changes depending on whether you want LUKS encryption or not.

### If you want LUKS Encryption

Create another partition and set the type to "physical volume for encryption". Set up your desired password.

Wait for the cursor to stop spinning, then scroll to the top of the table. You should see a device which looks something like `/dev/mapper/XXXX_crypt`, where the `XXXX` could be something like `sda3`. One below this should be an indented device of type Ext4. Double click this one.

Change the type to "btrfs journaling file system" and set the mount point to `/`.

### If you don't want LUKS

Click the **+** button again and create a partition of type "btrfs journaling file system". Set the mount point to `/`.

### Finishing

Finish the installer as normal, and reboot when prompted. Hopefully you will find yourself booting into your new Mint install. Dismiss the welcome screen.

## Prepping

Wait a minute or so after booting and in the bottom-right you should see a shield icon. Click this to open up the Update Manager. It should show you a pop-up asking you to select mirrors. If not, Edit > Software Sources.

Choose an appropriate mirror for your country and save.

Now you should update the system, either use the Update Manager app or open a terminal and `sudo apt update && sudo apt upgrade`. This can take a while.

Now we need a single dependency - `inotify-tools`. Run `sudo apt install inotify-tools` to install this.

## Configuring Snapshots

Launch Timeshift and make sure the selected type is "Btrfs". Configure the automatic snapshots as you'd like - personally I don't want them, but you may prefer to have them on.

Click the "Create" button at the top to make your first snapshot.

Now we're going to get a tool called `grub-btrfs`. [Grab this from Github](https://github.com/Antynea/grub-btrfs) by clicking the the green "Code" button in the top-right-ish and choose "Download Zip". Save this somewhere appropriate. 

Open up a terminal and `cd` to the directory where you saved the file and run `unzip grub-btrfs-master.zip`. `cd` into the new `grub-btrfs` folder and run `sudo make install`. This will install a grub plugin to let you boot your Timeshift snapshots - we just need to hook them up first.

Run the command `sudo systemctl edit --full grub-btrfsd` to open the service file for editing. Find the ExecStart line, which should be `ExecStart=/usr/bin/grub-btrfsd /.snapshots --syslog` and change it to `ExecStart=/usr/bin/grub-btrfsd --syslog --timeshift-auto`. Save and close this file.

I don't like the default naming of the snapshots, so open up `/etc/default/grub-btrfs/config` as root in your favourite editor, and find the line starting `GRUB_BTRFS_TITLE_FORMAT`. Make it the following:

```
GRUB_BTRFS_TITLE_FORMAT=("date" "description" "snapshot" "type")
```

Save and close this file.

Run `systemctl restart grub-btrfsd.service` to finish.

Now we will have a snapshots menu added to Grub, we just need Grub to actually show when we boot.

Open up `/etc/default/grub` in your preferred editor. Change the following:

- `GRUB_TIMEOUT_STYLE=menu`
- `GRUB_TIMEOUT=3` (Or your preferred timeout value, just make sure it's more than 0).
- `GRUB_TERMINAL=console` (uncomment this line)
- `GRUB_GFXMODE=640x480` (uncomment this line)

Save and close this file, then run `sudo update-grub` to regenerate your Grub config. You should see a message from grub-btrfs about it creating menu entries for your snapshot, which means that's all working.

If you want to test this out, reboot the machine and look for a "Linux Mint Snapshots" grub entry. You should see your created snapshot inside it. Don't boot from this right now though, press Escape and boot from the normal Linux Mint entry. The idea of this is, if an update or package install ever breaks some part of the system, you can boot from an old snapshot and continue using your machine.

## Installing Containers

### Distrobox

Most immutable distros come with a container technology built-in to help with installing tools off of the host system. Fedora variants come with `toolbox` and the others typically use `distrobox`. The choice is personal preference, and you may not necessarily want one at all, but for this tutorial I will go with `distrobox`.

Head over to [the Github repo](https://github.com/89luca89/distrobox#git) and follow the install instructions. If you'd like `git` on your host, install it with `sudo apt install git` and clone the repo. Alternatively, use the same Code > Download ZIP button as with grub-btrfs and unzip the file as before. Run the `install.sh` script and you should end up with `distrobox` in your `~/.local/bin` folder.

You now need a container engine to go with Distrobox, so use `apt` to install either `podman` or `docker` - I would definitely recommend `podman`:

```
sudo apt install podman
```

Distrobox's target is not in your PATH by default, so you will need to add this. Open up `~/.bashrc` in your favourite editor and add the following line to the bottom:

```
export PATH=/home/YOUR_USERNAME/.local/bin:$PATH
```

Replacing `YOUR_USERNAME` with your user.

Now run `source ~/.bashrc` followed by `distrobox`. If you get anything other than "command not found" then Distrobox is working.

### Optional - Distrobox GUI

I've made a GUI for Distrobox if you'd prefer to spin up containers with a graphical interface. [Grab it from Github](https://github.com/Dvlv/BoxBuddyGTK) and follow the install instructions.

### Flatpak

Flatpak should be installed by default, with the Flathub repository available to the system. My personal preference is to have flatpaks all as user installs, so I prefer to do this:

```
flatpak remote-delete flathub

flatpak remote-add --if-not-exists --user flathub https://flathub.org/repo/flathub.flatpakrepo
```

## Final Ventures with apt

Have a look around the system and spot anything you don't think you'll need. For me that included things like Hexchat, Thunderbird, Warpinator and LibreOffice (I prefer the Flatpak).

Use apt to remove these, e.g:

```
sudo apt remove package1, package2
```

Once you're happy with what's gone, now think of anything you absolutely _need_ on the host system. This would be things which won't work in Distrobox and aren't available as a Flatpak.

For me this includes neovim, tilix, sshfs.

Install all of these with apt as normal.

Once you're happy, it's time to "lock away" the `apt` command. Don't worry, it will still be usable, since Mint isn't _actually_ an immutable distro, we just want to discourage ourselves from using it unless necessary - just like Fedora's `rpm-ostree`.

## Aliasing apt

Over in your `~/.local/bin` folder, make a script named `fake-apt.sh` and write the following:

```
echo "Are you SURE you can't use Distrobox or Flatpak for this?";
echo "If so, run sudo su to use apt.";
```

Save and close this, then mark it executable with `chmod +x ~/.local/bin/fake-apt.sh`.

Open up `~/.bashrc` and add the following to the bottom:

```
alias sudo="sudo "
alias apt="bash /home/YOUR_USERNAME/.local/bin/fake-apt.sh"
```

We alias `sudo` to sudo-with-a-space because that's how you allow "chaining" of aliases. Without this, `sudo apt` would not use the `apt` alias below. We replace the `apt` command with our new shell script.

Run `source ~/.bashrc` and try `sudo apt update`, you should see your new message. The `apt` command is now hidden for our user - but not actually gone.

If you need it in future, `sudo su` to jump to root, or just comment out the alias. Your GUI programs will still work as well: Software Manager and Update Manager.


## Congrats

That's it! We now have a Linux Mint setup which pretends to be an immutable distro. We've got snapshot-booting similar to MicroOS allowing us to "roll back" if a bad update ever lands, and we have the tools necessary to use containerised applications to keep our host as minimal as possible.
