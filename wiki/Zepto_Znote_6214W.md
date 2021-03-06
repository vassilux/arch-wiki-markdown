Zepto Znote 6214W
=================

  

+--------------------------------------------------------------------------+
| Contents                                                                 |
| --------                                                                 |
|                                                                          |
| -   1 Introduction                                                       |
|     -   1.1 What does work?                                              |
|     -   1.2 Not tested                                                   |
|                                                                          |
| -   2 Installation                                                       |
| -   3 Configurations                                                     |
|     -   3.1 Kernel boot options                                          |
|     -   3.2 rc.conf                                                      |
|     -   3.3 Graphic Card and Xorg                                        |
|     -   3.4 Wireless                                                     |
|     -   3.5 Sound                                                        |
|                                                                          |
| -   4 External Links                                                     |
+--------------------------------------------------------------------------+

Introduction
============

The is a nice laptop, or rather a really small and light desktop
replacement.

It is build upon the Intel NAPA platform with i945PM Chipset, Core Duo
CPU and ipw3945 wireless.

Remember to update the BIOS on ftp.zepto.dk/Firmware/Bios/6214W

What does work?
---------------

3D Acceleration using the latest nvidia driver

Wireless using ipw3945

Wired network using r1000 (included in arch stock kernel)

Suspend to swap. though it was really slow to suspend.

20/2-07:Texas Instrument card reader works, some drivers are loaded but
to make the card reader working just add tifm_sd in the module section
from rc.conf

20/2-07:TV output is fine with nvidia driver and an appropriate
xorg.conf, either dual screen or twinview mode.

These worked for me nearly ootb

    Frequency-Scaling with acpi-cpufreq
    Tv-Out
    Bluetooth 

Not tested
----------

Texas Intrument card reader, there's currently a developement running to
make it work here and here

Installation
============

Installation is straigt forward. You might have to select IDE-Legacy in
the first Grub menu, otherwise you might get a kernel panic. Drivers for
the wired and wireless network cards are included.

Configurations
==============

Kernel boot options
-------------------

Boot the kernel with noapic because the BIOS is a little buggy. This
prevents random freezes when watching DVD's, gaming etc.

rc.conf
-------

My MODULES looks like this:

    MODULES=(!ata_piix ahci !hw_random r1000 ipw3945)

This makes sure eth1 is wired network and eth2 is wireless. The
hw_random module seems to fail on this laptop so I blacklist it.

Graphic Card and Xorg
---------------------

This laptop has a GeForce Go 7600 graphic card and works only with the
proprietary nvidia driver (or vesa). Native resolution is only possible
with the nvidia driver.

The monitor runs at 1280x768 60Hz, so remember to add "1280x768" to
Modes

Wireless
--------

You have to use ipw3945 in order to make your wifi connection work.

    pacman -Sy ipw3945

will install the necessary packages (for the stock kernel that is)

Then add ipw3945d to DAEMONS in rc.conf. See wireless setup for
additional information on wireless networking

Sound
-----

The sound will work out of the box, make sure you have:

    pacman -Sy alsa-lib alsa-utils 

packages installed, and add yourself to audio group.

As of kernel 2.6.26 you might not be able to adjust the PCM volume, as
there is no slider. You can workaround this with setting the following
in your /etc/modprobe.d/sound

    options snd-hda-intel model=lenovo enable=1 index=0
    alias snd-card-0 snd-hda-intel
    alias sound-slot-0 snd-hda-intel

To get volume hotkeys to work create ~/.Xmodmap with:

    keycode 160 = XF86AudioMute 
    keycode 174 = XF86AudioLowerVolume
    keycode 176 = XF86AudioRaiseVolume

(If you use openbox do not forget to map the XF86xxxxxx keys in rc.xml)

External Links
==============

-   This report has been listed in the Linux Laptop and Notebook
    Installation Survey: Zepto.

Retrieved from
"https://wiki.archlinux.org/index.php?title=Zepto_Znote_6214W&oldid=238914"

Category:

-   Laptops
