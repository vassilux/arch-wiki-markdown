Erch
====

Erch is Embedded Arch
---------------------

Being able to build a small image of arch for embedded purposes.

Build chroot
------------

Start by building a chroot via the devtools package.

    # mkarchroot -n -C pacman.conf -M makepkg-i686.conf $HOME/erch/rootfs base

After this completes then you need to package it up.

    $ cd rootfs/
    $ tar -zcf ../rootfs.tgz .

Now we create an ext[2,3,4] image

    dd if=/dev/zero of=rootfs.ext3 bs=1MB count=0 seek=1024 # seek=1024 = 1GB while seek=2048 = 2GB etc
    mkfs.ext3 -F rootfs.ext3
    mkdir mnt
    sudo mount -o loop rootfs.ext3 mnt
    tar -C mnt -zxf rootfs.tgz

This is the root file system of our arch image.

To test I used qemu.

    qemu -hda rootfs.ext3 -kernel rootfs/boot/vmlinuz-linux -initrd root/boot/initramfs-linux.img append "root=/dev/sda"

It took a while to boot and ran into some errors about a read only file
system.

What's next
-----------

Getting a minimal group (pacman -S minimal)

Different categories for minimal:

  minimal-   packages
  ---------- ----------
  busybox    busybox
  uclibc     uclibc

-   Making build scripts to ease the pain of all these commands. I
    really like the installer for Arch so maybe something similar to
    that.
-   getting ABS going to rebuild the system to makepkg specs.

Retrieved from
"https://wiki.archlinux.org/index.php?title=Erch&oldid=254482"

Category:

-   Getting and installing Arch
