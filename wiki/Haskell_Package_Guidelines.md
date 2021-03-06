Haskell Package Guidelines
==========================

Package creation guidelines

* * * * *

CLR – Cross – Eclipse – Free Pascal – GNOME – Go – Haskell – Java – KDE
– Kernel – Lisp – MinGW – Nonfree – OCaml – Perl – Python – Ruby – VCS –
Web – Wine

Haskell is well supported on Arch Linux. GHC and a few core packages are
available in the official repositories. For more serious Haskellers, the
ArchHaskell community project provides many packages from Hackage, and
the number is growing.

See the ArchHaskell community page for contact details and ways to help.

+--------------------------------------------------------------------------+
| Contents                                                                 |
| --------                                                                 |
|                                                                          |
| -   1 Haskell Packages                                                   |
|     -   1.1 [haskell-core]                                               |
|     -   1.2 [haskell-web]                                                |
|     -   1.3 Last resorts                                                 |
|                                                                          |
| -   2 Improving ArchHaskell                                              |
|     -   2.1 Community                                                    |
|     -   2.2 Overview                                                     |
|     -   2.3 [haskell-core] maintenance                                   |
|     -   2.4 Other repo maintenance                                       |
|     -   2.5 Creating another repo                                        |
|     -   2.6 List of satellite repos                                      |
+--------------------------------------------------------------------------+

Haskell Packages
----------------

To use Haskell on Arch Linux, you have two mutually exclusive options:

1.  Use packages from the official Arch Linux repositories. These are a
    well maintained small subset of all Haskell packages. Just install
    them the way you would install anything else on Arch Linux. Examples
    of what is available: in the extra and community repositories. The
    packages here should satisfy people who just want to use the Haskell
    Platform. You may also combine this option with other packages from
    unofficial sources like the AUR.
2.  Use the ArchHaskell project's unofficial repositories. These contain
    a much larger subset of what's available on Hackage. As a community
    effort, we often need volunteers to help maintain and add more
    packages to these repositories. Read on for information about using
    them. A note for xmonad users: if you switch to this option, you
    need to install the haskell-xmonad package instead of xmonad (which
    is in the official community repo and has different dependencies).

> [haskell-core]

The [haskell-core] repository is the base repository of packages
maintained by the ArchHaskell team. [haskell-core] can be accessed by
adding the following entry to /etc/pacman.conf (above [extra]):

    [haskell-core]
    Server = http://xsounds.org/~haskell/core/$arch

or

    [haskell-core]
    Server = http://www.kiwilight.com/haskell/core/$arch

The set of packages in the [haskell-core] repository is derived from the
habs tree officially located here. A tool called cblrepo is used to keep
the habs tree synchronized with the official Haskell packages from
Hackage.

Putting [haskell-core] above [extra] will ensure that the packages from
[haskell-core] take precedence, in case of duplicate packages in the two
repositories.

The repositories provide both file listings (by using repo-add --files),
package deltas (repo-add --delta), and both packages and the database
are signed. The fingerprint of the key used for signing is:

     pub   2048D/4209170B 2012-12-26
           Key fingerprint = F310 4992 EBF2 4EB8 72B9  7B9C 32B0 B453 4209 170B
     uid                  ArchHaskell (Magnus Therning) <magnus@therning.org>
     sub   2048D/A418C0FE 2012-12-26

If you use SigLevel = Required TrustedOnly in /etc/pacman.conf for
[haskell-core], then you need to do sudo pacman-key --lsign-key 4209170B
to add Magnus Therning's key.

> [haskell-web]

The [haskell-web] repository builds on [haskell-core], providing several
more packages, especially those useful for web applications.

    [haskell-web]
    Server = http://archhaskell.mynerdside.com/$repo/$arch

Add it after [haskell-core].

> Last resorts

-   Haskell packages in the AUR
-   cabal-install directly

Unfortunately, many of the packages in the AUR are outdated due to a
lack of resources. If you have the time, it is recommended to use
cblrepo and create something like [haskell-web], which can then be added
to the collection of haskell-providing repositories.

Improving ArchHaskell
---------------------

> Community

See the ArchHaskell community page and get in touch via the mailing list
or the IRC channel.

> Overview

The plan is to have one user-facing repository, [haskell], which merges
the packages available in various satellite repositories (like
[haskell-web]), thereby distributing the maintenance load. One satellite
repo is special, the [haskell-core] repository, which provides packages
that are dependencies of all the other satellites.

> [haskell-core] maintenance

Ensure:

-   [haskell-core] is an Arch repo hosted at kiwilight and xsounds.
-   [haskell-core] is in sync with the habs cblrepo database.

> Other repo maintenance

For example, for haskell-foo, ensure:

-   haskell-foo is a cblrepo database, possibly using packages from
    [haskell-core] as DistroPkgs.
-   Whenever [haskell-core] is updated, haskell-foo's database is
    updated to match within a reasonable time.

> Creating another repo

> List of satellite repos

Retrieved from
"https://wiki.archlinux.org/index.php?title=Haskell_Package_Guidelines&oldid=255112"

Category:

-   Package development
