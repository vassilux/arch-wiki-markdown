XFS
===

XFS is a high-performance journaling file system created by Silicon
Graphics, Inc. XFS is particularly proficient at parallel IO due to its
allocation group based design. This enables extreme scalability of IO
threads, filesystem bandwidth, file and filesystem size when spanning
multiple storage devices.

  ------------------------ ------------------------ ------------------------
  [Tango-document-new.png] This article is a stub.  [Tango-document-new.png]
                           Notes: please use the    
                           first argument of the    
                           template to provide more 
                           detailed indications.    
                           (Discuss)                
  ------------------------ ------------------------ ------------------------

+--------------------------------------------------------------------------+
| Contents                                                                 |
| --------                                                                 |
|                                                                          |
| -   1 Data corruption                                                    |
|     -   1.1 Repair XFS Filesystem                                        |
|                                                                          |
| -   2 Performance                                                        |
|     -   2.1 Stripe Size and Width                                        |
|     -   2.2 Disable Barrier                                              |
|     -   2.3 Access Time                                                  |
|     -   2.4 Defragmentation                                              |
|         -   2.4.1 Inspect fragmentation levels                           |
|         -   2.4.2 Perform defragmentation                                |
|                                                                          |
| -   3 External Links                                                     |
+--------------------------------------------------------------------------+

Data corruption
---------------

If for whatever reason you experience data corruption, you will need to
repair the filesystem manually.

> Repair XFS Filesystem

First unmount the XFS filesystem.

    # unmount /dev/sda3

Once unmounted, run the xfs_repair tool included in the pacman package
xfsprogs.

    # xfs_repair -v /dev/sda3

Performance
-----------

The best way to get performance out of XFS is to plan ahead before you
create the filesystem. Where is the journal stored?

Warning:Disabling barriers, disabling atime, and other performance
enhancements make data corruption and failure much more likely.

For more info read: Maximizing Performance

> Stripe Size and Width

If this filesystem will be on a striped RAID you can gain significant
speed improvements by specifying the stripe size to the mkfs.xfs
command.

See How to calculate the correct sunit,swidth values for optimal
performance

> Disable Barrier

You can increase performance by disabling barrier usage for the
filesystem by adding the nobarrier mount option to the /etc/fstab file.

> Access Time

On some filesystems you can increase performance by adding the noatime
mount option to the /etc/fstab file. For XFS filesystems the default
atime behaviour is relatime, which has almost no overhead compared to
noatime but still maintains sane atime values. All Linux filesystems use
this as the default now (since around 2.6.30), but XFS has used
relatime-like behaviour since 2006, so no-one should really need to ever
use noatime on XFS for performance reasons.

Also, noatime implies nodiratime, so there is never a need to specify
nodiratime when noatime is also specified.

> Defragmentation

Although the extent-based nature of XFS and the delayed allocation
strategy it uses significantly improves the file system's resistance to
fragmentation problems, XFS provides a filesystem defragmentation
utility (xfs_fsr, short for XFS filesystem reorganizer) that can
defragment the files on a mounted and active XFS filesystem. It can be
useful to view XFS fragmentation periodically.

xfs_fsr improves the organization of mounted filesystems. The
reorganization algorithm operates on one file at a time, compacting or
otherwise improving the layout of the file extents (contiguous blocks of
file data).

Inspect fragmentation levels

To see how much fragmentation your file system currently has:

    # xfs_db -c frag -r /dev/sda3

Perform defragmentation

To begin defragmentation, use the xfs_fsr command which is included with
the "xfsprogs" package.

    # xfs_fsr /dev/sda3

External Links
--------------

-   XFS FAQ
-   Improving Metadata Performance By Reducing Journal Overhead
-   XFS Wikipedia Entry

Retrieved from
"https://wiki.archlinux.org/index.php?title=XFS&oldid=217965"

Category:

-   File systems
