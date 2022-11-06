Title: Installing Fedora Alongside Windows 10 Dual Boot
Date: 2021-11-27
Tags: Linux
Category: Linux

I've recently installed Fedora Silverblue on my Gaming Laptop, the last of my machines running Windows 10.
Getting it installed to dual boot took quite a few attempts, so I'll document here how to set it up successfully.

If you haven't already, you will need to clear some space on the drive with Windows. You can actually shrink the drive Windows is currently booted from, which
I don't believe you can do with Linux. Search the internet for "Windows 10 create disk partition" for a tutorial on this.

Follow the Fedora installer as normal. When you get to the Disk section, select your drive and choose "Manual" from
the set of radiobuttons. Then press "Next" in the top left corner.

You should then be presented with the partion creator. I had to open up the drive by clicking a bit
which said "Unknown >" on the middle-left of the installer.

Using the Plus button underneath the list of partitions, you will want to create three new partitons within the empty space you have created:

- 1024 MB, vfat, mounted at /boot
- 512MB, efi, mounted at /boot/efi
- The rest of the drive, whatever format you want (I went with the recommended btrfs), Mounted at /

Even though the drive already had an existing EFI Partition, mounting that at /boot/efi did not work. I needed to make another one for Fedora.

Then confirm this and follow the installer as normal. Fingers crossed, it should complete.

Grub still finds both efi partitions and lets me boot either OS easily.
