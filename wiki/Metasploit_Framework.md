Metasploit Framework
====================

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
| -   1 Overview                                                           |
| -   2 Installation                                                       |
| -   3 Updating                                                           |
| -   4 Interfaces                                                         |
| -   5 See Also                                                           |
+--------------------------------------------------------------------------+

Overview
--------

Consider the MSF to be one of the single most useful auditing tools
freely available to security professionals today. From a wide array of
commercial grade exploits and an extensive exploit development
environment, all the way to network information gathering tools and web
vulnerability plugins. The Metasploit Framework provides a truly
impressive work environment. The MSF is far more than just a collection
of exploits, it's an infrastructure that you can build upon and utilize
for your custom needs. This allows you to concentrate on your unique
environment, and not have to reinvent the wheel. Currently, metasploit
requieres you to setup and configure postgresql on your system to work.
This wiki will show you how to get metasploit-git working with a
postgresql database.

Installation
------------

Install metasploit or metasploit-svn or metasploit-git from the AUR.

Updating
--------

If you're using metasploit you can update the framework from within the
msfconsole with:

    msf> svn update

but the updates won't be tracked by pacman.

A better solution is using metasploit-svn and updating via a makepkg or
an AUR Helper.

Interfaces
----------

There are serveral interfaces you can use with MSF although msfconsole
is the one that can provide the most features available in MSF. To run
it, go to terminal and run:

    # /opt/metasploit/msfconsole

or put the following alias into your .bashrc

    alias msfconsole='/opt/metasploit/msfconsole'

Note that metasploit is installed in /opt/metasploit/* and
metasploit-svn in /usr/src/metasploit/* so for the svn version you do
not need the alias.

Other interfaces are: msfcli and msfgui

See Also
--------

-   Metasploit Unleashed Free information security training.

Retrieved from
"https://wiki.archlinux.org/index.php?title=Metasploit_Framework&oldid=248917"

Categories:

-   Networking
-   Security
