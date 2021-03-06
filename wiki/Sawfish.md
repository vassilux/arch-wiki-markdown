Sawfish
=======

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
| -   1 Introduction                                                       |
| -   2 Installation                                                       |
| -   3 Starting Sawfish                                                   |
| -   4 Customization                                                      |
|     -   4.1 Themes                                                       |
|         -   4.1.1 Adding new                                             |
|                                                                          |
|     -   4.2 Menus                                                        |
|         -   4.2.1 xfce4-menu-to-sawfish.xslt                             |
|                                                                          |
| -   5 Additional resources                                               |
+--------------------------------------------------------------------------+

Introduction
------------

Sawfish is a highly customizable window manager. Formerly it has been
the standard Window manager of the GNOME desktop, but because of a
change of maintainership this is no longer true. This article will show
you how to install and configure it for standalone use (w/o GNOME).

Installation
------------

Sawfish can be installed with the sawfish package, available in the AUR.

Starting Sawfish
----------------

If you use startx, just put

     exec sawfish

at the end of your ~/.xinitrc. For other cases, see ... ?

Customization
-------------

Out-of-the box Sawfish provides a menu accessible by clicking Mouse-2
(middle button) on the root window. From there, you can launch the
customization app.

If you change any settings, they are stored in ~/.sawfish/custom.

> Themes

You can select a window theme from the customization app. There is a
small bug: if the theme is parametrizable, its configuration window will
only appear after you restart the customization app.

Adding new

Create the directory ~/.sawfish/themes/ and drop there any theme files.
You do not need to uncompress them. There are 500+ themes to choose from
in the Sawfish site.

> Menus

Arch doesn't provide an automatic menu generator for Sawfish, but you
can generate the menus using the XFCE4 ones. Here are the necessary
steps:

1. Create the directory ~/.sawfish/lisp/. This is where custom Sawfish
scripts are stored; the menu will be one of them.

2. Install libxslt

    # pacman -S libxslt

3. Copy the xslt stylesheet below; paste it into a file named
xfce4-menu-to-sawfish.xslt.

4. Now you need a XFCE4 menu. Maybe you have one already (where?) or you
can generate one with menumaker or xdg_menu.

4.1. If want to use menumaker...

4.1.1. Install it...

    # pacman -S menumaker

4.1.2. Generate the menu:

    $ mmaker -c xfce4 | xsltproc xfce4-menu-to-sawfish.xslt - > ~/.sawfish/lisp/arch-menu.jl

4.2. If you want to use xdg_menu...

4.2.1. Install it...

    # pacman -S archlinux-xdg-menu

4.2.2. Generate the menu:

    $  xdg_menu --format xfce4 --root-menu /etc/xdg/menus/arch-applications.menu | xsltproc xfce4-menu-to-sawfish.xslt - > ~/.sawfish/lisp/arch-menu.jl

5. Now you have a script ~/.sawfish/lisp/arch-menu.jl that defines a
variable arch-menu with a list of your applications. You need to link to
it from the root menu. You need to write the code for that to happen in
~/.sawfishrc. So, start your favorite editor, create ~/.sawfishrc, and
type:

    ;;; sawfish configuration file
    ; 1. Load defaults
    (require 'sawfish-defaults)
    ; 2. Load autogenerated arch-menu
    (require 'arch-menu)
    ; 3. Replace Sawfish's apps-menu with ours
    (setq apps-menu arch-menu)

6. And that's it. Restart Sawfish to effect the changes.

xfce4-menu-to-sawfish.xslt

    <?xml version="1.0" encoding="UTF-8" ?>
    <xsl:stylesheet version="1.0"
                    xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
      <xsl:output method="text"/>
      <xsl:strip-space elements="*"/>
      <xsl:param name="terminal">urxvt</xsl:param>
      <xsl:template name="indent">
        <xsl:text>
    </xsl:text>
        <xsl:for-each select="ancestor-or-self::*">
          <xsl:text>  </xsl:text>
        </xsl:for-each>
      </xsl:template>
      <xsl:template match="/">
        <xsl:text>;;; Arch applications menu for Sawfish, generated from xfce4 menu
    (defvar arch-menu nil)
    (setq arch-menu '(</xsl:text>
        <xsl:apply-templates/>
        <xsl:text>))
    </xsl:text>
      </xsl:template>
      <xsl:template match="menu[.//app]">
        <xsl:call-template name="indent"/>
        <xsl:text>("</xsl:text>
        <xsl:value-of select="@name"/>
        <xsl:text>"</xsl:text>
        <xsl:apply-templates/>
        <xsl:text>)</xsl:text>
      </xsl:template>
      <xsl:template match="app">
        <xsl:call-template name="indent"/>
        <xsl:text>("</xsl:text>
        <xsl:value-of select="@name"/>
        <xsl:text>" (system "</xsl:text>
        <xsl:if test="@term='true'">
          <xsl:copy-of select="$terminal"/>
          <xsl:text> -e </xsl:text>
        </xsl:if>
        <xsl:value-of select="@cmd"/>    
        <xsl:text> &"))</xsl:text>
      </xsl:template>
    </xsl:stylesheet>

Additional resources
--------------------

-   Sawfish wiki --- The official website
-   info sawfish --- Local documentation

Retrieved from
"https://wiki.archlinux.org/index.php?title=Sawfish&oldid=216333"

Category:

-   Dynamic WMs
