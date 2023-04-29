# A Beginner's Guide to Fedora Silverblue

## 0.0 What is Fedora Silverblue?

Fedora Silverblue is a linux distribution very similar to Fedora Workstation, with the key difference of being an _Immutable Distro_.

### 0.1 What is an Immutable Distro?

An Immutable Distro typically exhibits two key features:

- Certain (root-level) directories are mounted read-only, preventing the user of a running system from making any changes to them.
- At least one previous "version" of the system is kept, allowing the user to revert to a previous state.

### 0.2 Why use an Immutable Distro?

The benefits of an Immutable Distro are twofold:

- Consistency - When distros are released, the system is likely tested "as-is" to ensure it is working. It is unfeasible for the mapr intainers to then test every possible combination of packages and configurations on top of what's provided. Therefore, the "closer" your system is to the default, the more likely it is to be tested and working, and the more likely it is that you can find another person with a similar system to help with any problems you encounter.

- Rollbacks - It is very easy to roll back any update which you do not want, for example if a program stops working or its interface changes in a way you were not expecting, you can revert your system to _exactly_ how it was before you updated.

## 1.0 How does Silverblue achieve Immutability?

Fedora Silverblue makes use of a technology called `ostree`, as well as a utility called `rpm-ostree`.

### 1.1 What is ostree?

OSTree is a tool which provides complete filesystems in a tree-like structure. It is similar in concept to `git`. The filesystem is often referred to as an "image", similar to container technologies. Acquiring a copy of an OSTree filesystem is known as "pulling". Each time you "pull" an "image" you are acquiring a complete and identical copy of the filesystem.

There can be multiple different, independent filesystems hosted by the same entity. These are known as "branches". Switching between these branches is known as "rebasing", and is easy to do (and undo!).

### 1.2 What is rpm-ostree?

`rpm-ostree` is a tool provided in Silverblue which allows a user to install RPM packages, including those from Fedora's repos, onto an OSTree system. This concept is known as "layering", as you can think of each additional RPM installed on top of Silverblue's OSTree image as adding a "layer" on top.

## 2.0 How is Silverblue different from Workstation?

Silverblue's use of OSTree means software is installed differently, the system is updated differently, and there are different packages available out-of-the-box compared with Workstation.

As well as differences in included software, some of the applications which come with a Silverblue install are installed as Flatpaks, compared to being installed via the regular repos in Workstation. Some examples are Weather, Maps, Connections and File Roller.

### 2.1 Updating Silverblue vs Workstation

Fedora Workstation updates using `dnf`. When you run `dnf update` individual RPM packages are fetched from Fedora's repo and each is installed separately. Notably, only software which has a higher version in the repos versus on your machine will be fetched and installed. Once installed, the newer versions of these packages are available on your running system right then-and-there.

By contrast, Silverblue is updated using `rpm-ostree`. When called, this fetches the latest version of the entire OSTree image, then individually re-layers all of your layered RPMs on top of this "fresh" image. This new image is then saved _independently_ of the image you are currently booted into. If this process fails for any reason, the OSTree image is not saved to disk, and your system will be the same as if you had not run the update at all. If the process succeeds, you will be able to boot into your new image by restarting or shutting down your machine (instantly-available installs / updates are possible, but more on that later; a reboot is needed by default).

If you would like to undo an update on Workstation, `dnf` is called to download older versions of the packages which were previously updated, and these are then re-installed in-place on your running machine, overwriting the updated packages. If an update caused the machine to not boot, this can be tricky to deal with, possibly involving the use of a live USB.

If you would like to undo an update on Silverblue, you can simply reboot and pick the previous image from GRUB. Since you will have been using this image to perform the update previously, you can be confident that this image will boot and function exactly as before the update.

### 2.2 Out of the box packages

To keep the OSTree image lean, some additional software that comes with Workstation is not included by default on Silverblue. This includes Gnome Boxes, LibreOffice and RhythmBox (TODO check these).

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

Open the application named "Software". On first launch, this may take a few minutes to scan for available applications. Once finished, you can use the magnifying glass in the top-left to search for what you want to install. 

Click on the 



### 3.2 Layering with `rpm-ostree`

Anything which needs access to the base system, such as drivers, must be layered via `rpm-ostree`. 

CLI programs are not typically distributed by Flatpak, so these are also good candidates for being layered.


