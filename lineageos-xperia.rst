:Title: How To Install Lineage OS on a Sony Xperia X Compact
:Date: 2019-06-18
:Tags: Linux Android
:Category: Linux

Today I managed to get Lineage OS 14.1 to install on my old Sony Xperia X Compact. Since I couldn't find an actual step-by-step guide,
I figured I'd write one myself.

Disclaimers
===========
Installing a new OS on your phone will wipe *all* data from it. Do not change the OS unless you have backed up all of your data. At the very 
least, move your photos, music etc. to the SD card and remove it from the phone before continuing.

I am not the developer or uploader of any linked files. I take no responsibility for your phone if you follow this guide, and I provide no 
guarantee that this will work for you. All sources and credits are at the bottom of this page.

Overview
========
Installing Lineage is a 3 step process. You must do each step, and in the order below. It will not work if you don't.

I assume you have a normal, unmodified Xperia X Compact running Android 8.0, with a locked bootloader.

Before you begin
----------------
Install adb and fastboot on your computer. For Arch linux, this is as simple as installing ``android-tools`` via ``pacman``.
For any other OS, there are plenty of tutorials out there.

You will need to download the following files:

    - `Lineage OS 14.1 ROM <https://www.androidfilehost.com/?fid=890129502657578159>`_
    - `TWRP 3 <https://twrp.me/sony/sonyxperiaxcompact.html>`_

FYI if androidfilehost gets stuck searching for mirrors and you have uBlock Origin, try disabling it for that page.

The next steps are as follows:
    - Unlock the bootloader
    - Flash TWRP
    - Install Lineage with TWRP

Unlocking the Bootloader
========================
Visit the Sony Developers website here: https://developer.sony.com/develop/open-devices/get-started/unlock-bootloader

Scroll to the bottom and choose your device. 

Follow the instructions to receive your unlock code. For example's sake, I'll use CF30000000000.

Plug your phone into your computer and, when prompted, select "Transfer files". 

Open a terminal on your computer and type ``adb devices``. 
When prompted on your phone, check the "Always allow from this machine" checkbox to allow usb debugging from your computer.

Now run ``adb reboot bootloader``. Your phone screen will go black, and your notification light will shine blue.

Enter ``fastboot -i 0x0fce oem unlock 0xCF300000000`` replacing the CF300000000 with your own unlock code (keep the 0x there).

Your bootloader is now unlocked. Unplug from your PC or run ``fastboot reboot`` to reboot.

You will see a warning triangle and a message saying "Device is unlocked and cannot be trusted". This means your unlocking was successful.

If you want to leave it to boot, it will take a few minutes. By unlocking the bootloader, you have essentially reset the phone, and it will
ask you to set it up again upon booting. To force a power off, hold the power button and both volume buttons down. You will feel a small vibrate, 
then three bigger vibrates. Release after the wave of bigger vibrates.

Flashing TWRP
=============
You now need to get back to the bootloader state. If you have turned the phone on and set it up again, re-enable developer tools and usb debugging, 
then run ``adb reboot bootloader`` as before.

If you did not bother booting the phone again, hold the volume down key and plug it into your computer. You should then be greeted with the 
blue notification light as before.

``cd`` to the location at which you saved your ``twrp-3.x.x-kugo.img`` file and run ``fastboot flash recovery twrp-3.x.x-kugo.img`` making sure 
to replace the ``3.x.x`` bit with the actual file name of your twrp image.

Unplug your phone or ``fastboot reboot`` again. 

Power off your phone when you can, then hold power and volume down at the same time until the phone vibrates. Release, and 
you should be greeted with the TWRP screen.

Wiping the old and Installing LineageOS
=======================================
From TWRP click "Wipe". Swipe the blue arrows to clear the phone's cache. Back in the Wipe menu, click "Format Data". You'll see a warning 
and checkbox about this deleting all data and removing the encryption. Confirm all of this and perform the wipe.

Back to the main TWRP menu, click "Mount". Ensure that "Data" is checked, then plug your phone into your computer. Ensure it can be seen with 
``adb devices``. 

Push your ROM to the phone with ``adb push LineageOS-14.1-xx-xx-xx-kugo.zip /sdcard``

On TWRP, click "Install" and find your LineageOS zip file in the ``sdcard`` directory. Click on it, tick the box to reboot after installation, and 
swipe to install it.

That's it! Your phone should reboot and hang on the Lineage OS logo for about 3 minutes (The blue curved line with the circle bouncing back and forth) 
so be patient. 

What now?
=========
I have no desire to put the GApps on my phone, so I don't know how you would go about doing this. I'm sure there are other websites 
which will teach you how to do this.

I have chosen to put `FDroid <https://www.f-droid.org/>`_ on my phone instead. Visit f-droid.org on the phone's browser and download the apk to install it.

Otherwise, enjoy your LineageOS Phone!

Credits / Sources
=================
LineageOS | XDA Developers | jtk_de : https://forum.xda-developers.com/x-compact/development/rom-lineageos-14-1-xperia-x-compact-t3739525

TWRP | XDA Developers | Chippa_a : https://forum.xda-developers.com/x-compact/development/ub-twrp-v3-2-1-xperia-x-compact-t3793837

Bootloader Unlock | Get Droid Tips : https://www.getdroidtips.com/unlock-bootloader-sony-xperia-x-compact-f5321/

