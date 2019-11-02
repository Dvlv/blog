:Title: How to Mount a 9p Share in a Centos 7 or 8 KVM Guest
:Date: 2019-09-29
:Tags: Linux
:Category: Linux

Just a short post as this is something I've been needing for a while, but it doesn't come up when searching online.

If you try and use 9p in a vanilla centos install, you'll see ``Unknown filesystem type 9p``.

In your Centos guest, run this:

``sudo yum --enablerepo centosplus install kernelplus``

Now you can mount 9p shares using the commands found `in tutorials like this. <https://www.linux-kvm.org/page/9p_virtio>`_
