Bluetooth GPRS Howto
====================

  ------------------------ ------------------------ ------------------------
  [Tango-mail-mark-junk.pn This article or section  [Tango-mail-mark-junk.pn
  g]                       is poorly written.       g]
                           Reason: please use the   
                           first argument of the    
                           template to provide a    
                           brief explanation.       
                           (Discuss)                
  ------------------------ ------------------------ ------------------------

This Howto will describe, how to set up a GPRS Internet connection using
bluetooth device and compatible mobile phone.

+--------------------------------------------------------------------------+
| Contents                                                                 |
| --------                                                                 |
|                                                                          |
| -   1 Requirements                                                       |
| -   2 Instructions                                                       |
| -   3 Connection                                                         |
|     -   3.1 pppd                                                         |
|     -   3.2 wvdial                                                       |
|                                                                          |
| -   4 Use Blueman and NetworkManager                                     |
| -   5 Use Bluez and NetworkManager                                       |
+--------------------------------------------------------------------------+

Requirements
------------

1.  Linux compatible bluetooth device
2.  Bluetooth enabled mobile phone - I'm using Nokia N70

Instructions
------------

1.  Install bluez-utils, bluez-libs and dbus (install also ppp if you
    haven't):

        pacman -S bluez-utils bluez-libs dbus

2.  Start dbus and bluetooth service:

        /etc/rc.d/dbus start
        /etc/rc.d/bluetooth start

3.  Scan for nearby bluetooth devices:

        hcitool scan
        Scanning ...
                 00:19:79:89:13:8E      Penpen N70

    Now, mark that above address, it's the bluetooth hardware address of
    your mobile phone (provided that the name in the right matches what
    you have written in the phone).

4.  Scan, which channel is used for Dial-Up Networking:

        sdptool search --bdaddr 00:19:79:89:13:8E DUN
        Inquiring ...
        Searching for DUN on 00:19:79:89:13:8E ...
        Service Name: Dial-Up Networking
        Service RecHandle: 0x10007
        Service Class ID List:
          "Dialup Networking" (0x1103)
        Protocol Descriptor List:
          "L2CAP" (0x0100)
          "RFCOMM" (0x0003)
            Channel: 3
        Language Base Attr List:
          code_ISO639: 0x454e
          encoding:    0x6a
          base_offset: 0x100
        Profile Descriptor List:
          "Dialup Networking" (0x1103)
            Version: 0x0100

    Now check the line beginning with "Channel:" - it contains the
    channel which is used for Dial-Up Network and it is not necessarily
    1 and so is the case with my Nokia N70; my phone uses channel 3.
    Check also that you are reading the right results; the bluetooth
    hardware address must match your phone's.

5.  Now edit /etc/bluetooth/rfcomm.conf , first uncomment the rfcomm0
    device section. Then add/change the lines as this (if necessary):

        bind yes;
        device 00:19:79:89:13:8E;
        channel 3;
        comment "GPRS dialup";

    Of course, you should use your device address and the appropriate
    channel.

6.  Edit the bluetooth conf file in /etc/conf.d to make the rfcomm start
    when bluetooth system is started:

        # Activate rfcomm ports (default: false)
        RFCOMM_ENABLE="true"

7.  Restart the bluetooth service:

        /etc/rc.d/bluetooth restart

8.  Look, whether the bluetooth device is bound to the phone. It should
    look like this:

        ls -l /dev/rfcomm0
        crw-rw---- 1 root tty 216, 0 2007-08-10 21:32 /dev/rfcomm0

9.  Now comes the interesting part. You see, there must be an agreed
    password for connecting to the phone's modem. This password is set
    up in the following way: Open up another root terminal and execute

        bluez-simple-agent

    (if there is an error concerning a .service file, just stop
    bluetoothd, restart dbus and start bluetooth again). This should
    give you a message saying the device is ready, do not do anything
    else here for now, but go to the first terminal and type in

        cat /dev/rfcomm0

    and press Return. Shortly after the phone should be asking for a
    code for pairing with the computer. Use whatever code you wish on
    the phone. Then in the bluez-simple-agent a prompt appears asking
    for this code. After you have entered it and cofirmed via Return you
    can CTRL-c it and close the terminal. The phone is now paired with
    the computer.

Connection
----------

> pppd

Edit the bottom of /etc/ppp/ip-up as following:

    echo -e "#generated by ip-up\nsearch local\nnameserver $DNS1\nnameserver $DNS2" > /etc/resolv.conf
    chmod a+r /etc/resolv.conf

This change is used to set correct DNS settings after connection. Now
you need scripts to actually use the phone's modem and call your ISP. My
ISP is the Finnish saunalahti, mainly because of it's predefined fee for
unlimited data access. You must substitute saunalahti for your ISP
accordingly through the rest of the document. First we edit the modem
setup script for my isp. I'm using script named
/etc/ppp/peers/saunalahti and having the following content:

    show-password 
    noauth
    /dev/rfcomm0
    115200
    connect /etc/ppp/peers/saunalahti-connect
    disconnect /etc/ppp/peers/saunalahti-disconnect
    defaultroute
    #replacedefaultroute
    noipdefault 
    #nodetach
    usepeerdns
    crtscts
    local
    ipcp-accept-local
    #lcp-echo-interval 0
    maxfail 10
    lcp-echo-failure 0
    lcp-echo-interval 0
    #lcp-max-configure 0
    #lcp-max-failure 10000
    #lcp-max-terminate 0
    #lcp-restart 30000 
    novj
    nobsdcomp
    novjccomp
    nopcomp
    noaccomp
    linkname saunalahti
    mtu 1500
    mru 1500

Some remarks: You should possibly first uncomment the nodetach-option,
so you can track, whether the call succeeds. Without the option you just
get no confirmation.

Some Carrier needs special username and password for connection. In this
case you should add:

    user "username"
    password "passwd"

A good list of Carrier's and ther username/password pairs can be found
here: http://wiki.openmoko.org/wiki/GPRS

The lines starting with "connect" and "disconnect" are locations for
connect and disconnect chat scripts. Chat scripts are used to "talk"
with the modem. Lets take a look at them next.

Now, we edit the connect script. Mine is at
/etc/ppp/peers/saunalahti-connect and is like the following:

    #!/bin/sh
    exec chat                                               \
            TIMEOUT         5                               \
            ECHO            ON                              \
            ABORT           '\nBUSY\r'                      \
            ABORT           '\nERROR\r'                     \
            ABORT           '\nNO ANSWER\r'                 \
            ABORT           '\nNO CARRIER\r'                \
            ABORT           '\nNO DIALTONE\r'               \
            ABORT           '\nRINGING\r\n\r\nRINGING\r'    \
            ''              \rAT                            \
            TIMEOUT         12                              \
            SAY             "Press CTRL-C to close the connection at any stage!"    \
            SAY             "\ndefining PDP context...\n"   \
            OK              ATH                             \
            OK              ATE1                            \
            OK              'AT+CGDCONT=1,"IP","internet.saunalahti","",0,0'        \
            OK              ATD*99#                         \
            TIMEOUT         22                              \
            SAY             "\nwaiting for connect...\n"    \
            CONNECT         ""                              \
            SAY             "\nConnected." \
            SAY             "\nIf the following ppp negotiations fail,\n"   \
            SAY             "try restarting the phone.\n"

Also some remarks about these: In the place of internet.saunalahti you
should have your mobile operators access point name. That can be checked
from Settings->Connection->Access points . Hilight the Internet access
point and select edit. Go down to section "Access point name". There you
have it. One more thing. The modem command "ATD*99#"'s number "*99#" is
a special number (at least in Nokia phones) which is used to "call to
Internet access point". Pretty clever I'd say; no fiddling around with
actual numbers, let the phone handle everything behind the scenes. Edit
the disconnect script (mine's is in
/etc/ppp/peers/saunalahti-disconnect). You should have something like
this:

    #!/bin/sh
    exec /usr/sbin/chat -V -s -S    \
    ABORT           "BUSY"          \
    ABORT           "ERROR"         \
    ABORT           "NO DIALTONE"   \
    SAY             "\nSending break to the modem\n"        \
    ""              "\K"            \
    ""              "\K"            \
    ""              "\K"            \
    ""              "+++ATH"        \
    ""              "+++ATH"        \
    ""              "+++ATH"        \
    SAY             "\nPDP context detached\n"

Set executable atribute for files saunalahti-connect and
saunalahti-disconnect

    chmod +x /etc/ppp/peers/saunalahti*connect

Ok, you have completely set up bluetooth + gprs connection. Yay! Next
thing to do is to test it. If you let the "nodetach" option uncommented
in section 12, running (again, substitute yourisp for, well your ISP :)

    pon yourisp

should return something like this:

    Press CTRL-C to close the connection at any stage!
    defining PDP context...
    rAT
    OK
    ATH
    OK
    ATE1
    OK
    AT+CGDCONT=1,"IP","internet.saunalahti","",0,0
    OK
    waiting for connect...
    ATD*99#
    CONNECT
    Connected.
    If the following ppp negotiations fail,
    try restarting the phone.
    Serial connection established.
    Using interface ppp0
    Connect: ppp0 <--> /dev/rfcomm0
    kernel does not support PPP filtering
    Cannot determine ethernet address for proxy ARP
    local  IP address 85.77.231.188
    remote IP address 10.6.6.6
    primary   DNS address 195.197.54.100

    secondary DNS address 195.74.0.47

If the output is something similar, you have a working connection . If
this is the case, you can press ctrl-c to abort the connection and
uncomment the "nodetach" option from section 12.

Later when you want connection, you can just run

    pon yourisp

and when disconnecting

    poff yourisp

You can add "dbus bluetooth" to the DAEMONS section in /etc/rc.conf so
you can connect more easily after reboot (be aware that hal already
starts dbus). All done, enjoy!

  

> wvdial

Bluez-simple-agent must be running in another terminal for this step
even if you already paired your devices. Edit the file /etc/wvdial.conf
and put the following line on it

    [Dialer Defaults]
    Modem = /dev/modem
    Baud = 115200
    Init1 = ATZ
    Init2 = ATE0

    [Dialer pin]
    Init1 = AT+CPIN=YOURPIN

    [Dialer YOURISP]
    Modem = /dev/rfcomm0
    Init3 = AT+cgdcont=1,"IP","YOURISPAPN"
    Phone = *99***1#
    Stupid Mode = 1
    Username = username
    Password = password

Replace YOURISP/YOURPIN/YOURISPAPN/username/password with the right data
and use

    # wvdial YOURISP

for connect, if you need to pass the PIN number, use

    # wvdial pin

Use Blueman and NetworkManager
------------------------------

As of blueman 1.02-1 and networkmanager 0.70-1, there is no need to edit
any configuration files anymore, so that we can easily set up bluetooth
GRPS dialup purely in GUI. Tested with Nokia N70.

1.  Click bluetooth applet and right click in blank area to select
    "Setup", then Bluetooth Manager will automatically search and setup
    devices for you, and you can choose to "connect to DialUp
    networking" if your device support this. After this, check out if
    you've got /dev/rfcomm0, if so you've got paired.
2.  Right click on nm-applet and choose "Edit Connections", choose
    Mobile Broadband tab, click "add" it will pop you up to choose a
    Mobile Broadband Connection, there you can see your bluetooth
    device.
3.  Edit properties of the connection, remember to change up to your own
    network setting. Normally, you only need to provide number and APN,
    leave others blank. And uncheck "Use authentication" in PPP tab,
    otherwise dialup would fail.
4.  Select and connect to dialup service just like other connection type
    with networkmanager.

Use Bluez and NetworkManager
----------------------------

You can set up a GPRS connection purely using GUI. Install
prerequisites:

    # pacman -S modemmanager networkmanager bluez gnome-bluetooth gnome-control-center

Firstly, add your phone to the known devices list using gnome-bluetooth.
This includes pairing the computer and the cell phone. Then run
gnome-control-center and select "Bluetooth" section there. Choose your
phone you have just added. In the right panel select "Acces the Internet
using your mobile phone (DUN)". Follow the steps of determining correct
mobile network operators. Restart the NetworkManager daemon. Now you
will see "Mobile Broadband" section in the list of connections. Just
click it to establish the connection. You can check for the
configuration files (/etc/NetworkManager/system-connections/<Mobile
operator - connection name>)

    # cat /etc/NetworkManager/system-connections/<Mobile operator - connection name>

    [connection]
    id=<Mobile operator - connection name>
    uuid=434e7d89-94df-43dd-8d12-e7bb3cfcd98f
    type=bluetooth
    autoconnect=false

    [bluetooth]
    bdaddr=00:AA:BB:AA:XX:XX
    type=dun

    [gsm]
    number=*99#
    apn=internet

    [serial]
    baud=115200

Check for the newly created network interface (ppp0):

    # ip addr show dev ppp0

You should disable all other connections and check the routing table:

    $ ip route

    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    0.0.0.0         10.6.6.6        0.0.0.0         UG    0      0        0 ppp0
    10.6.6.6        0.0.0.0         255.255.255.255 UH    0      0        0 ppp0

Retrieved from
"https://wiki.archlinux.org/index.php?title=Bluetooth_GPRS_Howto&oldid=249635"

Category:

-   Bluetooth
