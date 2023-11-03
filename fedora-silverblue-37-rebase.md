Title: Rebasing to Fedora Silverblue 37 With Firefox Removed
Date: 2022-11-16
Tags: Linux
Category: Linux

## TL;DR

```bash
rpm-ostree override reset firefox
(reboot)
rpm-ostree rebase fedora:fedora/37/x86_64/silverblue
(reboot)
rpm-ostree override remove firefox firefox-langpacks
```



## Explanation

With Fedora 37, the Firefox package has been split into two, `firefox` and `firefox-langpacks`.

Some people choose to remove the base Firefox in favour of the Flatpak, but this will cause an error like the following when trying to rebase:

```bash
error: Could not depsolve transaction; 1 problem detected:
 Problem: package firefox-langpacks-106.0.4-1.fc37.x86_64 requires firefox = 106.0.4-1.fc37, but none of the providers can be installed
  - conflicting requests
```

The easiest solution to this is to re-add the base Firefox, then rebase, then remove it (along with the langpacks) afterwards.


