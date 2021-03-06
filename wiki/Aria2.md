Aria2
=====

aria2 is lightweight, command-line download manager featuring support
for HTTP/HTTPS, FTP, BitTorrent and Metalink protocols. aria2 is
generally regarded as one of the lightest and fastest download managers
available and across multiple platforms. Some of the more outstanding
features of aria2 include great flexibility of layout through the use of
numerous options and configuration files and a built-in XML-RPC and
JSON-RPC management interface.

+--------------------------------------------------------------------------+
| Contents                                                                 |
| --------                                                                 |
|                                                                          |
| -   1 Installation                                                       |
| -   2 Execution                                                          |
| -   3 Configuration                                                      |
|     -   3.1 aria2.conf                                                   |
|     -   3.2 Example .bash_alias                                          |
|     -   3.3 Example aria2.conf                                           |
|         -   3.3.1 Option Details                                         |
|             -   3.3.1.1 Example Input File #1                            |
|             -   3.3.1.2 Example Input File #2                            |
|                                                                          |
|         -   3.3.2 Additional Notes                                       |
|                                                                          |
|     -   3.4 Example aria2.rapidshare                                     |
|         -   3.4.1 Option Details                                         |
|         -   3.4.2 Additional Notes                                       |
|                                                                          |
|     -   3.5 Example aria2.bittorrent                                     |
|         -   3.5.1 Option Details                                         |
|                                                                          |
|     -   3.6 Frontends                                                    |
|                                                                          |
| -   4 Useful Tips & Tricks                                               |
|     -   4.1 pacman XferCommand                                           |
|     -   4.2 Custom Minimal Build                                         |
|         -   4.2.1 Minimal PKGBUILD Example                               |
|                                                                          |
|     -   4.3 aria2 sometimes stops downloading without exiting            |
|                                                                          |
| -   5 See also                                                           |
+--------------------------------------------------------------------------+

Installation
------------

Install aria2 from official repositories.

You may also want to install aria2-systemd.

Execution
---------

The executable name for the aria2 package is aria2c. This legacy naming
convention has been retained for backwards compatibility.

Configuration
-------------

> aria2.conf

aria2 looks to ~/.aria2/aria2.conf for a set of global configuration
options by default. This behavior can be modified with the --conf-path
switch:

-   Download aria2.example.rar using the options specified in the
    configuration file /file/aria2.rapidshare

    $ aria2c --conf-path=/file/aria2.rapidshare http://rapidshare.com/files/12345678/aria2.example.rar

If ~/.aria2/aria2.conf exists and the options specified in
/file/aria2.rapidshare are desired, the --no-conf switch must be
appended to the command:

-   Do not use the default configuration file and download
    aria2.example.rar using the options specified in the configuration
    file /file/aria2.rapidshare

    $ aria2c --no-conf --conf-path=/file/aria2.rapidshare http://rapidshare.com/files/12345678/aria2.example.rar

If ~/.aria2/aria2.conf does not yet exist and you wish to simplify the
management of configuration options:

    $ touch ~/.aria2/aria2.conf

> Example .bash_alias

    alias down='aria2c --conf-path=${HOME}/.aria2/aria2.conf'
    alias rapid='aria2c --conf-path=/file/aria2.rapidshare'

> Example aria2.conf

    continue
    dir=${HOME}/Desktop
    file-allocation=none
    input-file=${HOME}/.aria2/input.conf
    log-level=warn
    max-connection-per-server=4
    min-split-size=5M
    on-download-complete=exit

This is essentially the same as if running the following:

    $ aria2c dir=${HOME}/Desktop file-allocation=none input-file=${HOME}/.aria2/input.conf on-download-complete=exit log-level=warn FILE

Note:The example aria2.conf above may incorrectly use the $HOME
variable. Some users have reported the curly brace syntax to explicitly
create a separate ${HOME} subdirectory in the aria2 working directory.
Such a directory may be difficult to traverse as bash will consider it
to be the $HOME environment variable. For now, it is recommended to use
absolute path names in aria2.conf.

Option Details

 continue
    Continue downloading a partially downloaded file if a corresponding
    control file exists.
 dir=${HOME}/Desktop
    Store the downloaded file(s) in ~/Desktop.
 file-allocation=none
    Do not pre-allocate disk space before downloading begins. (Default:
    prealloc) 1
 input-file=${HOME}/.aria2/input.conf
    Download a list of line, or TAB separated URIs found in
    ~/.aria2/input.conf
 log-level=warn
    Set log level to output warnings and errors only. (Default: debug)
 max-connection-per-server=4
    Set a maximum of four (4) connections to each server per file.
    (Default: 1)
 min-split-size=5M
    Only split the file if the size is larger than 2*5MB = 10MB.
    (Default: 20M)
 on-download-complete=exit
    Run the exit command and exit the shell once the download session is
    complete.

Example Input File #1

-   Download aria2-1.10.0.tar.bz2 from two separate sources to ~/Desktop
    before merging as aria2-1.10.0.tar.bz2

    http://aria2.net/files/stable/aria2-1.10.0/aria2-1.10.0.tar.bz2    http://sourceforge.net/projects/aria2/files/stable/aria2-1.10.0/aria2-1.10.0.tar.bz2

Example Input File #2

-   Download aria2-1.9.5.tar.bz2 and save to /file/old as
    aria2.old.tar.bz2  &
-   Download aria2-1.10.0.tar.bz2 and save to ~/Desktop as
    aria2.new.tar.bz2

    http://aria2.net/files/stable/aria2-1.9.5/aria2-1.9.5.tar.bz2
      dir=/file/old
      out=aria2.old.tar.bz2
    http://aria2.net/files/stable/aria2-1.10.0/aria2-1.10.0.tar.bz2
      out=aria2.new.tar.bz2

Additional Notes

 1 --file-allocation=falloc
    Recommended for newer file systems such as ext4 (with extents
    support), btrfs or xfs as it allocates large files (GB) almost
    instantly. Do not use falloc with legacy file systems such as ext3
    as prealloc consumes approximately the same amount of time as
    standard allocation would while locking the aria2 process from
    proceeding to download.

Tip:See aria2c -help#all and the aria2 man page for a complete list of
configuration options.

> Example aria2.rapidshare

    http-user=USER_NAME
    http-passwd=PASSWORD
    allow-overwrite=true
    dir=/file/Downloads
    file-allocation=falloc
    enable-http-pipelining=true
    input-file=/file/input.rapidshare
    log-level=error
    max-connection-per-server=2
    summary-interval=120

Option Details

 http-user=USER_NAME
    Set HTTP username as USER_NAME for password-protected logins. This
    affects all URIs.
 http-passwd=PASSWORD
    Set HTTP password as PASSWORD for password-protected logins. This
    affects all URIs.
 allow-overwrite=true
    Restart download if a corresponding control file does not exist.
    (Default: false)
 dir=/file/Downloads
    Store the downloaded file(s) in /file/Downloads.
 file-allocation=falloc
    Call posix_fallocate() to allocate disk space before downloading
    begins. (Default: prealloc)
 enable-http-pipelining=true
    Enable HTTP/1.1 pipelining to overcome network latency and to reduce
    network load. (Default: false)
 input-file=/file/input.rapidshare
    Download a list of single line of TAB separated URIs found in
    /file/input.rapidshare
 log-level=error
    Set log level to output errors only. (Default: debug)
 max-connection-per-server=2
    Set a maximum of two (2) connections to each server per file.
    (Default: 1)
 summary-interval=120
    Output download progress summary every 120 seconds. (Default: 60) 3

Additional Notes

-   Because aria2.rapidshare the contains a username and password, it is
    advisable to set permissions on the file to 600, or similar.

    arch ~ $ cd /file
    arch /file  $ chmod 600 /file/aria2.rapidshare
    arch /file $ ls -l
    total 128M
    -rw------- 1 arch users  167 Aug 20 00:00 aria2.rapidshare

 3 summary-interval=0
    Supresses download progress summary output and may improve overall
    performance. Logs will continue to be output according to the value
    specified in the log-level option.

Tip:The example configuration file can also be applied to Hotfile,
DepositFiles, et.al.

Note:Command-line options always take precedence over options listed in
a configuration file.

> Example aria2.bittorrent

    bt-seed-unverified
    max-overall-upload-limit=1M
    max-upload-limit=128K
    seed-ratio=5.0
    seed-time=240

Option Details

 bt-seed-unverified=false
    Do not check the hash of the file(s) before seeding. (Default: true)
 max-overall-upload-limit=1M
    Set maximum overall upload speed to 1MB/sec. (Default: 0)
 max-upload-limit=128K
    Set maximum upload speed per torrent to 128K/sec. (Default: 0)
 seed-ratio=5.0
    Seed completed torrents until share ratio reaches 5.0. (Default:
    1.0)
 seed-time=240
    Seed completed torrents for 240 minutes.

Note:If both seed-ratio and seed-time are specified, seeding ends when
at least one of the conditions is satisfied.

> Frontends

In order to use frontends start aria2c with --enable-rpc option. Here is
a list of available UIs.

Warning: Note that configurations changed using these tools are not
stored.

-   Diana — A command line tool

https://github.com/baskerville/diana || diana

-   Webui — Html frontend

https://github.com/ziahamza/webui-aria2 ||

It is convenient to append a monitor function based on diana in your
shell configuration file:

    da(){ watch -ctn 1 "(echo -e '\033[32mGID\t\t Name\t\t\t\t\t\t\t%\tDown\tSize\tSpeed\tUp\tS/L\tTime\033[36m'; diana list| cut -c -112; echo -e '\033[37m'; diana stats)" }

Useful Tips & Tricks
--------------------

> pacman XferCommand

aria2c can be used as the default download manager for the pacman
package manager. See the ArchWiki article Improve Pacman Performance for
additional details.

> Custom Minimal Build

Gains in application response can be gleaned by removing unused features
and protocols. Further gains can be accomplished by removing support for
external libraries with a custom build. The following example creates an
aria2c executable with HTTP/HTTPS, FTP download and asynchronous DNS
support only. See the Arch Build System page for further details.

Minimal PKGBUILD Example

    pkgname=aria2
    pkgver=1.10.0
    pkgrel=100
    pkgdesc="Download utility that supports HTTP(S) and FTP"
    arch=('i686' 'x86_64')
    url="http://aria2.sourceforge.net/"
    license=('GPL')
    depends=('c-ares' 'gnutls' 'zlib' 'ca-certificates')
    source=(http://downloads.sourceforge.net/aria2/aria2-${pkgver}.tar.bz2)
    md5sums=('1386df9b2003f42695062a0e1232e488')

    build() {
      cd ${srcdir}/${pkgname}-${pkgver}

     ./configure --disable-bittorrent --disable-metalink \
     --disable-dependency-tracking --disable-xmltest --disable-nls \
     --without-sqlite3 --without-libxml2 --without-libexpat \
     --with-ca-bundle=/etc/ssl/certs/ca-certificates.crt --prefix=/usr

     make
    }

    package() {
    	cd ${srcdir}/${pkgname}-${pkgver}
    	make DESTDIR=${pkgdir} install
    }

> aria2 sometimes stops downloading without exiting

You can use this little script to restart the download every 30 minutes:

    #! /bin/bash

    if [ ! -x /usr/bin/aria2c ]; then
        echo "aria2c is not installed"
        exit 1
    fi

    if [ ! -d ~/.torrentdl ]; then
        mkdir ~/.torrentdl
    fi

    while :; do
        for i in $@; do
            MAGNET=false
    	if [[ "$i" = magnet* ]]; then
    	    MAGNET=true
    	elif [ ! -f "$i" ]; then
    	    echo "The file ('$i') is not given or does not exist"
    	    exit 1
    	fi
    	if ! aria2c --hash-check-only=true "$i" &> /dev/null; then
    	    aria2c "$i"
    	    sleep 30m && kill -9 $(pidof aria2c)
    	else
    	    echo "Torrent is downloaded!"
    	fi
        done
    done

See also
--------

-   aria2 Wiki - Official Site
-   aria2 Usage Examples - Official Site
-   Aria2c Downloader through VPN Tunnel - UnOfficial Site

Retrieved from
"https://wiki.archlinux.org/index.php?title=Aria2&oldid=256086"

Category:

-   Internet Applications
