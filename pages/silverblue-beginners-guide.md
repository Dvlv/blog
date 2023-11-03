Title: A Beginner's Guide to Fedora Silverblue
Date: 2023-04-29
Tags: Linux
Category: Linux

------

[TOC]

------

## 0.0 What is Fedora Silverblue?

Fedora Silverblue is a linux distribution very similar to Fedora Workstation, with the key difference of being an _Immutable Distro_.

### 0.1 What is an Immutable Distro?

An Immutable Distro typically exhibits two key features:

- Certain (root-level) directories are mounted read-only, preventing the user of a running system from making any changes to them.
- At least one previous "version" of the system is kept, allowing the user to revert to a previous state.

### 0.2 Why use an Immutable Distro?

The benefits of an Immutable Distro are twofold:

- Consistency - When distros are released, the system is likely tested "as-is" to ensure it is working. It is unfeasible for the maintainers to then test every possible combination of packages and configurations on top of what's provided. Therefore, the "closer" your system is to the default, the more likely it is to be tested and working, and the more likely it is that you can find another person with a similar system to help with any problems you encounter.

- Rollbacks - It is very easy to roll back any update which you do not want, for example if a program stops working or its interface changes in a way you were not expecting, you can revert your system to _exactly_ how it was before you updated.

## 1.0 How does Silverblue achieve Immutability?

Fedora Silverblue makes use of a technology called `ostree`, as well as a utility called `rpm-ostree`.

### 1.1 What is ostree?

OSTree is a tool which provides complete filesystems in a tree-like structure. It is similar in concept to `git`. The filesystem is often referred to as an "image", similar to container technologies. Acquiring a copy of an OSTree filesystem is known as "pulling". Each time you "pull" an "image" you are acquiring a complete and identical copy of the filesystem.

There can be multiple different, independent filesystems hosted by the same entity. These are known as "branches". Switching between these branches is known as "rebasing", and is easy to do (and undo!).

### 1.2 What is rpm-ostree?

`rpm-ostree` is a tool provided in Silverblue which allows a user to install RPM packages, including those from Fedora's repos, onto an OSTree system. This concept is known as "layering", as you can think of each additional RPM installed on top of Silverblue's OSTree image as adding a "layer" on top.

Once an image is downloaded from OSTree and your layered packages are applied on top, the final product is available to boot into. This final product is known as a "deployment".

## 2.0 How is Silverblue different from Workstation?

Silverblue's use of OSTree means software is installed differently, the system is updated differently, and there are different packages available out-of-the-box compared with Workstation. Examples include Gnome Boxes, RhythmBox and LibreOffice.

As well as differences in included software, some of the applications which come with a Silverblue install are installed as Flatpaks, compared to being installed via the regular repos in Workstation. Some examples are Weather, Maps, Connections and File Roller.

### 2.1 Updating Silverblue vs Workstation

Fedora Workstation updates using `dnf`. When you run `dnf update` individual RPM packages are fetched from Fedora's repo and each is installed separately. Notably, only software which has a higher version in the repos versus on your machine will be fetched and installed. Once installed, the newer versions of these packages are available on your running system right then-and-there.

By contrast, Silverblue is updated using `rpm-ostree`. When called, this fetches the latest version of the entire OSTree image, then individually re-layers all of your layered RPMs on top of this "fresh" image. This new image is then saved _independently_ of the image you are currently booted into. If this process fails for any reason, the OSTree image is not saved to disk, and your system will be the same as if you had not run the update at all. If the process succeeds, you will be able to boot into your new image by restarting or shutting down your machine (instantly-available installs / updates are possible, but more on that later; a reboot is needed by default).

If you would like to undo an update on Workstation, `dnf` is called to download older versions of the packages which were previously updated, and these are then re-installed in-place on your running machine, overwriting the updated packages. If an update caused the machine to not boot, this can be tricky to deal with, possibly involving the use of a live USB.

If you would like to undo an update on Silverblue, you can simply reboot and pick the previous image from GRUB. Since you will have been using this image to perform the update previously, you can be confident that this image will boot and function exactly as before the update.

## 3.0 Installing Software on Silverblue

There are three recommended ways to install software on Silverblue: 

- Using Flatpak
- Layering via `rpm-ostree`
- Installing into a Toolbox


### 3.1 Using Flatpak

Flatpaks are applications built upon a consistent base-system independant of the user's distribution. They run in a sandbox, giving the user fine-grained control over certain permissions, such as network access or ability to see certain files / folders.

By default, Fedora Silverblue comes with a number of flatpaks from Fedora's own flatpak repo. As of version 38, the Flathub repo is also enabled out-of-the-box, giving access to a very wide range of graphical applications.

It is recommended to install graphical applications using `flatpak` where available, since they are independent of the base OSTree image. Since their dependencies are self-contained, they cannot cause any conflicts within the base system, and they can be updated independently (or even automatically!) without having to update the entire system.

There are two ways to install Flatpak applications on Silverblue - either through the graphical Gnome Software application, or the CLI `flatpak` command.

#### 3.1.1 Gnome Software

Open the application named "Software". On first launch, this may take a few minutes to scan for available applications. Once finished, you can use the magnifying glass in the top-left to search for what you want to install. Clicking on your selected application will then present you with more details about it, as well as an "Install" button in the top-right. Simply click this button to install the application. To uninstall something, click the trash can icon and confirm.

When updates are available you should receive a notification from Gnome Software which will take you to its "Updates" section when clicked. You can then press "Update All" to update all of your flatpaks.

#### 3.1.2 The `flatpak` command

The `flatpak` command can both search for and install applications from both Fedora's and Flathub's repos. To search for Inkscape, simply run `flatpak search inkscape`. This will show you a list of results in a table. You will need to use the identifier in the "Application ID" column for installing and removing.

To install with the `flatpak` command, run `flatpak install <repo> <application_id>`. For example, to install Inkscape from the Flathub repo execute: `flatpak install flathub org.inkscape.Inkscape`.

Removing a flatpak is `flatpak remove <application_id>`, for example: `flatpak remove org.inkscape.Inkscape`. 

Updating your flatpaks is just `flatpak update`.


### 3.2 Layering with `rpm-ostree`

Anything which needs access to the base system, such as drivers, must be layered via `rpm-ostree`. 

CLI programs are not typically distributed by Flatpak, so these are also good candidates for being layered.

At the time of writing there is no way to use `rpm-ostree` to search for available packages. You can instead either use the website <a href="https://packages.fedoraproject.org">packages.fedoraproject.org</a> or open up a Toolbox (more on that next) and search with `dnf search`.

Installing a package is done by `rpm-ostree install packagename`, for example to layer Inkscape: `rpm-ostree install inkscape`.

Removing a package is done by `rpm-ostree uninstall inkscape`.

Installing and removing packages pulls the OSTree down again and layers any chosen RPMs on top to create a brand new deployment, just like an update would. This means you will need to reboot into your new deployment to use (or get rid of) packages installed / uninstalled.

If you don't want to reboot, you can pass `--apply-live` or `-A` to the (un)install command to have them applied to the running system (a new deployment is still created, however). For example: `rpm-ostree install -A inkscape` will install Inkscape _and_ make it available to use without a reboot.

#### 3.2.1 Should I avoid layering?

There is nothing necessarily _wrong_ with layering packages - it's the whole point of including `rpm-ostree` in the first place. Packages in Fedora's repos are designed to be installed in tandem and are expected to play nicely together.

The things to keep in mind are:

- The more packages you layer, the further you stray from the base OSTree image, making it possible that you run into problems that people who have not layered the same packages will not face. Bugs with dependency resolution do occasionally happen.
- As each update re-layers all of your RPMs from scratch, the more there is to install, the longer the updates will take. In practice this may not be a big deal at all, since you can keep using the system while it is being updated.

Generally, if you are unable to get something by Flatpak, and you don't have any desire to mess around with toolboxes, then just layer it!

### 3.3 Toolboxes

A Toolbox (sometimes called Toolbx) is a Podman container of Fedora which is nicely integrated with the base system in a way that allows both graphical and CLI applications to execute (almost) as seamlessly as if they were installed on the base system. 

Toolboxes are great for developers, as they allow for installation of programming languages and tools with `dnf` just like Workstation, but with the bonus of having anything system-level isolated from both the host and from other Toolboxes. This means you can easily install, say, Python 3.8 in one Toolbox and Python 3.11 in another, without the need for a version-manager on your host.

Graphical applications should typically Just Work™ from inside a toolbox, but be aware that they will not appear in your menu unless manually added. 

#### 3.3.1 Creating and using a Toolbox

Create a toolbox by executing `toolbox create name_of_my_toolbox`, where `name_of_my_toolbox` is an appropriate name for what you will be installing inside. By default this will create a container of the same version of Fedora as your base system. To use a different version, pass the `-r` flag followed by the version number, for example `toolbox create my_tbox -r 39` to create a container of Fedora 39.

Enter a Toolbox with `toolbox enter name_of_my_toolbox`. You will now be in a shell inside your Toolbox. 

From inside the shell, you can use `dnf` as normal to install, update, and remove packages.

To run a single command from inside a toolbox while on the main system, run `toolbox run -c name_of_my_toolbox application_name`. For example, if you have Inkscape inside a toolbox named `graphics`, run: `toolbox run -c graphics inkscape`.


#### 3.3.2 When should I use a Toolbox?

Tools which have a specific use-case with a small scope, such as versions of `npm` or `rustc` specific to a particular programming project, are good candidates for being installed in a Toolbox.

Graphical applications which are not available as Flatpaks and will not layer with `rpm-ostree` for any reason (which sometimes happens with proprietary tools) could also be installed in a Toolbox. See the later section for tips on adding toolbox-installed applications to the system menu.

### 3.4 Other Methods

The above should cover 99% of use-cases, but there may be situations where your chosen software is not available. Here are some other methods of installing applications:

#### 3.4.1 AppImage

AppImages should work out-of-the-box on Silverblue.

If you find you have an AppImage with a system-level dependency which is not met (such as VSCodium), it may prove more convenient to make this AppImage its own Toolbox, allowing you to use `dnf` to install these dependencies without affecting the base system. This AppImage will then need to be run from inside the Toolbox.

#### 3.4.2 Snap

Snaps do not work on Silverblue, due to `snapd`'s assumption that system-level directories such as `/snap` are writeable. Snaps will also not work in Toolbox or Distrobox containers. 

If you rely on software which is only available as a Snap, either use a Virtual Machine, or simply run a different, non-immutable distro.

#### 3.4.3 RPMs

Some RPMs will install normally via `rpm-ostree install my_rpm_file.rpm`. Some may not, and require installing inside a Toolbox.

#### 3.4.4 Arbitrary Scripts / Tarballs

Certain directories which appear to be system-level on first glance are actually symlinks to `/var`, which is always writeable. This includes `/opt` and `/usr/local`.

Binary files can be placed in `/opt` or `/usr/local`, and install-scripts which place files in these directories should work fine. 

If you'd prefer to install things at a user level, `~/bin` and `~/.local/bin` are good typical places.

#### 3.4.5 Distrobox

You can install Distrobox on Silverblue by layering it with `rpm-ostree` or following the <a href="https://github.com/89luca89/distrobox#curl-or-wget">Curl/Wget instructions</a> from Github. It should then function as-normal on Silverblue.


#### 3.4.6 Nix

It is technically possible to install and use Nix on Silverblue - but it involves some deeper changes to the system. A Gist is available <a href="https://gist.github.com/queeup/1666bc0a5558464817494037d612f094"> over on Github</a> for those who _really_ need it.

## 4.0 Updating Silverblue

### 4.1 Using Gnome Software

By default, Gnome Software should check for updates in the background while you are using your machine. This applies to both the base system and your flatpaks. When updates are available a notification will be displayed letting you know. 

The "Updates" tab at the top of the application will show the available updates, and there will be a button to apply them.

### 4.2 Using the CLI

Run the command `rpm-ostree update` to update your base system. This will pull a fresh copy of the latest OSTree system and layer your RPMs on top, then create a new deployment for you to boot into when you next power your computer on.

Run `flatpak update` to update all of your flatpak applications.

### 4.3 Updating Toolboxes

Toolboxes are Fedora containers, so running `sudo dnf update` will upgrade a Toolbox. These are not currently handled by Gnome Software, so will need to be updated manually via the CLI.

Note that Toolboxes cannot be upgraded from one major version to the next in the same way as Workstation. This is covered next.

## 5.0 Upgrading Silverblue to the Next Major Version

Neither Fedora Silverblue nor Workstation are "rolling" distros, they need to be manually upgraded to the next major version at the user's discretion.

Upgrading to the next version in Silverblue involves pointing the system to a different "branch" of OSTree. This is referred to as "rebasing".

### 5.1 Using Gnome Software

If the Gnome Software settings are left as default it should prompt you when the next major release is available. Click the button on the prompt to have your system rebase in the background. Reboot when it is finished and you should have a new version available in the GRUB menu.

### 5.2 Using the CLI

The command to search for available branches is `ostree remote refs fedora`. This should print out a list of possible rebase targets. The one you will want is likely called `fedora:fedora/XX/x86_64/silverblue` - where `XX` is the next major version number - for example `39`.

The command to execute the rebase is: `rpm-ostree rebase <target>`. For example, to rebase to Silverblue version 39: `rpm-ostree rebase fedora:fedora/39/x86_64/silverblue`

Once this has run you should have a new deployment to boot into the next time you power on your computer.

### 5.3 Upgrading Toolboxes

Upgrading Toolboxes is technically possible via `sudo dnf update --releasever=XX`, where `XX` is the next version number.

However, it is typically recommended that, if possible, you create a new Toolbox and re-install your software inside of that.

## 6.0 FAQs

### I Rebased to test the Beta, do I need to do anything when it becomes stable?

No, if you rebased to a branch _without_ the word `testing` in, this branch will become the stable branch of the new version when it releases. Just carry on updating your system as normal.

### How do I get videos to play in Firefox?

At the time of writing, the system Firefox does not come with necessary media codecs to play all online videos. This is due to licensing / patent issues. 

The simplest fix is to use the Flathub version of Firefox. Install this by running `flatpak install flathub org.mozilla.firefox`. If you don't want two version of Firefox installed, you can remove the one from the OSTree image with `rpm-ostree override remove firefox firefox-langpacks`.

If this is not an acceptable solution, you will need to add the RPMFusion repository. The <a href="https://rpmfusion.org/Configuration">official documentation</a> has instructions for Silverblue.

Once you have followed this guide, it is then recommended to run <a href="https://discussion.fedoraproject.org/t/simplifying-updates-for-rpm-fusion-packages-and-other-packages-shipping-their-own-rpm-repos/30364?u=siosm">some further commands</a> to ensure your repo is version-agnostic, which simplifies rebases.

With RPMFusion installed, install `ffmpeg` and/or `libavcodec-freeworld` to fix video playback.

### How do I edit a file in /etc?

Both `/etc` and `/var` are always writeable. You can edit configuration files in `/etc` as normal!

### How do I install something without rebooting?

Pass `-A` or `--apply-live` to the install command, e.g. `rpm-ostree install -A inkscape`.

### How do I keep a deployment around forever?

Pin your deployment with `sudo ostree admin pin 0`. When you no longer need it, `sudo ostree admin pin --unpin 2`. The numbers 0 and 2 refer to deployment indexes. To figure these out, run `rpm-ostree status` to list your available deployments. From the top down, the deployments are numbered starting from 0. For example, if you have three deployments available, the first one listed will be 0, the middle will be 1, and the bottom will be 2. 

If the deployment you are unpinning is not the third one down in this list, substitute the `2` from the unpin command with its index.

### How do I add things from Toolbox into my menu?

There are multiple ways to achieve this:

- A flatpak called <a href="https://flathub.org/apps/org.bluesabre.MenuLibre">MenuLibre</a> will let you edit your menu.
- A <a href="https://github.com/Dvlv/toolbox-gui">graphical manager</a> for Toolboxes, written by me, features an "Add to Menu" button.
- A <a href="https://github.com/Dvlv/tb2dt">CLI tool</a>, written by me, allows for exporting of .desktop files from a toolbox.

You can also do this manually, if you'd prefer:

- Search in `/usr/share/applications` for the `.desktop` file for the application you wish to export.
- Copy that to `~/.local/share/applications`.
- Edit that file and change any lines beginning with `Exec=` to run the command from inside the relevant toolbox.
    - For example, if the line is `Exec=inkscape` and your toolbox is `graphics`, change the line to `Exec=toolbox run -c graphics inkscape`

### How do I add Gnome Extensions?

You can either:

- Layer the extensions with `rpm-ostree`. You can search the Fedora repos for `gnome-shell-extension` to see what's available.
- Install the <a href="https://flathub.org/apps/com.mattjakeman.ExtensionManager">Extension Manager</a> flatpak, and use it to find and install extensions.

### How do I get the Nvidia Drivers working?

First you will need to add the RPMFusion Repo. The <a href="https://rpmfusion.org/Configuration">official documentation</a> has instructions for Silverblue.

Once you have followed this guide, it is then recommended to run <a href="https://discussion.fedoraproject.org/t/simplifying-updates-for-rpm-fusion-packages-and-other-packages-shipping-their-own-rpm-repos/30364?u=siosm">some further commands</a> to ensure your repo is version-agnostic, which simplifies rebases.

Now you can follow the <a href="https://rpmfusion.org/Howto/NVIDIA#Silverblue">instructions on RPMFusion's website</a>.

-------

## Glossary

- **Branch** -> An individual, separate, OSTree filesystem. 
- **Flatpak** -> A tool used to install independent, sandboxed applications (typically Graphical) on top of a Linux system.
- **Deployment** -> An OSTree image with your layered packages installed on top, ready to boot into from GRUB.
- **GRUB** -> The menu that appears upon booting your machine containing the available deployments to boot into.
- **Image** -> A full filesystem pulled from an OSTree repo.
- **Immutable Distro** -> A distribution which has certain system-level folders mounted read-only, and a way to access older versions of the system without affecting the current, running version.
- **Layer** / **layering** -> An RPM package installed via `rpm-ostree` on top of the base system.
- **Pinning/Unpinning** -> The commands for keeping a particular deployment saved to your machine indefinitely, until it is manually unpinned.
- **Pulling** -> Fetching the latest version of the OSTree filesystem.
- **Rebasing** -> Swapping the OSTree branch your machine pulls from, allowing you to switch to another desktop (Kinoite / Sericea) or to upgrade to the next major version.
- **Toolbox** -> A Fedora container which is integrated with the base-system to allow easy running of both CLI and Graphical applications
- **ostree** -> A technology providing complete, bootable filesystems to act as the base of an operating system.
- **rpm-ostree** -> The tool used to layer additional packages on top of the base system.

-------

This guide is released under the CC-0 Creative Commons licence. You are free to reproduce and redistribute as much of it as you'd like.

Last updated: **2023-04-29**

