Title: Installing Linux on a Lenovo Yoga Slim 7i Pro
Date: 2022-01-16
Tags: Linux, Hardware
Category: Linux

Short post to help anyone else who is trying to get Linux working on a Lenovo Yoga Slim 7i Pro (Intel).

The machine did not work perfectly out-of-the-box for me, even on a very new Kernel (5.15, Fedora Silverblue).

The keyboard does not work properly after booting, and the screen will just flicker random colours.

To boot the live USB, you will need to use "Safe Graphics Mode" or "Basic Graphics Mode", or something similar. This should be an option in the menu when booting your live USB.

After install, to fix these issues you will need to add the following Grub flags:

```
i8042.direct i8042.dumbkbd i915.enable_psr=0
```

If you booted in Safe Graphics Mode, you will also need to remove the `nomodeset` flag.

How to do this depends on your distribution. On Silverblue, the command is:

```
rpm-ostree kargs --editor
```

On other distributions, you probably do this by editing `/etc/default/grub` and finding the section `GRUB_CMDLINE_LINUX_DEFAULT`, where you can put the new flags inside the speech marks.

Afterwards, run `sudo update-grub` to regenerate your Grub file.

That should sort both issues out.
