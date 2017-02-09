CentOS 7
========

CentOS 7 General Tips
---------------------

### https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7

Various packages have been rebased. Some of those are samba, squid, **systemd**, krb5, gcc-libraries, binutils, gfs-utils, libreoffice, GIMP,SELinux, firewalld, libreswan, tomcat and open-vm-tools.

Commonly used utilities such as ifconfig/netstat have been marked as deprecated for some considerable time and the 'net-tools' package is no longer part of the @core group so will not be installed by default. Use nmcli c up ifname <interfacename> to get your network up and running and use yum to install the package if you really need it. Kickstart users can pull in the net-tools package as part of the install.

Many people have complained that Ethernet interfaces are not started with the new default NetworkManager tool/have to be explicitly enabled during installation. See CentOS-7 FAQ#2.


### https://wiki.centos.org/FAQ/CentOS7

FAQ#1 <snip> The network installation disk image is named netinstall.iso and can now be found only in the isos/ directory, together with all other installation images.

--

FAQ#2 Why does my Ethernet not work unless I log in and explicitly enable it? .. and why are the interface names all "messed up" compared to prior practice? This violates the Unix rule of "not breaking expectations".

Upstream has changed the default configuration to use NetworkManager and interfaces are (somewhat inexplicably in the case of Ethernet) not enabled by default. This can be worked around at install time where you have the possibility to enable your network card at the main installer screen, where the installer asks for your language/keyboard/storage devices/software installation. On this screen is a button labeled "Network & Hostname". Click that button, select the Ethernet connection you want to edit and click the "Off"-Button on the top right corner. Assuming DHCP is available you will see the connection in question getting a network address. If you have to configure your network settings by hand, press the "Configure" button, enter the desired values and save them. You probably have to disable and re-enable the just configured NIC for the changes to take affect. Now press "Done" and that's it. You can also make these changes with NetworkManager (System; Preferences; Network Connections or right click on the little network icon in the notification area and Edit Connections...) after the installation is complete.

If you are not using NetworkManager, the same result can be obtained by editing the configuration file for the network interface ( normally /etc/sysconfig/network-scripts/ifcfg-eth0 ) and changing:  ONBOOT=no to ONBOOT=yes Some setups seem to also require the addition of a line: BOOTPROTO=dhcp where a DHCP setup is in play; Static IP setups would take: BOOTPROTO=static of course

The ONBOOT edit may be performed (as root) and assuming the relevant device is eth0:

    # cd /etc/sysconfig/network-scripts/
    # sed -i -e 's@^ONBOOT="no@ONBOOT="yes@' ifcfg-eth0

As to "breaking expectations": The foregoing example uses a 'traditionally' named network device of: eth0 Other device names are also possible, including for example: em1 or p3p1 and such. Like it or not, this change in approach in interface naming is the future path for Linux. It was previewed in in the upstream's "testing distribution". See also the materials at: Dell's writeup and a blog post from an insider there.

--

FAQ#4 Many installations do not require the complexity of the NetworkManager tool, and use hand-edited configuration files instead. Here is a sample non NetworkManager DHCP interface configuration:

    [root@example ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE="eth0"
    BOOTPROTO=dhcp
    NM_CONTROLLED="no"
    PERSISTENT_DHCLIENT=1
    ONBOOT="yes"
    TYPE=Ethernet
    DEFROUTE=yes
    PEERDNS=yes
    PEERROUTES=yes
    IPV4_FAILURE_FATAL=yes
    IPV6INIT=yes
    IPV6_AUTOCONF=yes
    IPV6_DEFROUTE=yes
    IPV6_PEERDNS=yes
    IPV6_PEERROUTES=yes
    IPV6_FAILURE_FATAL=no
    NAME="eth0"
    [root@none ~]#

and a sample 'static assignment' configuration file:

    [username@hostname]$ cat /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE="eth0"
    HWADDR="00:21:70:10:7E:CD"
    NM_CONTROLLED="no"
    ONBOOT="yes"
    BOOTPROTO=static
    # BOOTPROTO=dhcp
    IPADDR=10.16.1.106
    NETMASK=255.255.255.0
    #
    #   the GATEWAY is sometimes in: /etc/sysconfig/network
    GATEWAY=10.16.1.1

and then common items such as hostname and DNS servers may optionally be placed in:

    [username@hostname]$ cat /etc/sysconfig/network
    HOSTNAME=acme.example.com
    DNS1=10.16.1.112
    DNS2=8.8.8.8
    ## DNS2=76.242.0.28
    SEARCH=example.com

The information there is 'optional' because a DHCP server can hand out these values. The initscripts are able to figure out hostname and so forth when a well-populated DNS environment exists, from PTR records and such, but some users need to manage such details manually. For more information, the full initscripts documentation files may be listed thus:

    rpm -qd initscripts

even in a environment lacking the man manual reading package and its dependencies.

--

FAQ#7 What have you done with ifconfig/netstat?

The ifconfig and netstat utilities have been marked as deprecated in the man pages for CentOS 5 and 6 for nearly a decade and Redhat made the decision to no longer install the net-tools package by default in CentOS 7. One reason to switch is that ifconfig does not show all details of ip addresses assigned to interfaces - use the ip command instead. The replacement utilities are ss and ip. If you really really need ifconfig and netstat back then you can yum install net-tools.


### https://wiki.centos.org/HowTos

#### disks
[Disk/filesystem optimization](https://wiki.centos.org/HowTos/Disk_Optimization)
In that article, if relevant, see especially the subheading "Raid Math"

#### networking
[How do I disable IPv6?](https://wiki.centos.org/FAQ/CentOS7#head-8984faf811faccca74c7bcdd74de7467f2fcd8ee) This is listed as part of the LOCKSS setup process. They don't explain why. See quote from pg 13 of their instructions below.
[Setting up iptables on CentOS](https://wiki.centos.org/HowTos/Network/IPTables)

#### security
[Hardening CentOS](https://wiki.centos.org/HowTos/OS_Protection)
[Encrypting your Filesystem](https://wiki.centos.org/HowTos/EncryptedFilesystem)
[Encrypting /tmp /swap & /home](https://wiki.centos.org/HowTos/EncryptTmpSwapHome)
[Securing SSH on your system](https://wiki.centos.org/HowTos/Network/SecuringSSH)


### https://wiki.centos.org/TipsAndTricks

[Virtual IP addresses](https://wiki.centos.org/TipsAndTricks/VirtualIPAddresses)
[Obtaining accurate server time](https://wiki.centos.org/TipsAndTricks/Server_Time)


Centos unofficial guides
------------------------

[Automatic Security Upgrades 1](https://serversforhackers.com/video/automatic-security-updates-centos)
[Automatic Security Upgrades 2](https://www.stephenrlang.com/2016/02/automatic-package-updates-on-centos-7/)
[Automatic Security Upgrades 3](https://linuxaria.com/howto/enabling-automatic-updates-in-centos-7-and-rhel-7)


Centos "service" command, and other results of migration to systemd
-------------------------------------------------------------------

### service (how lockss says to start up)

"service command doesn't work in CentOS 7 any more. Redhat based OS migrated to Systemd from SysVinit system...For sysVinit to systemd cheatsheet visit following link: [https://fedoraproject.org/wiki/SysVinit_to_Systemd_Cheatsheet](https://fedoraproject.org/wiki/SysVinit_to_Systemd_Cheatsheet)" ([http://unix.stackexchange.com/a/160247](http://unix.stackexchange.com/a/160247))

how to make "service" work in Docker (no reason to think this wouldn't work anywhere)
[https://github.com/CentOS/sig-cloud-instance-images/issues/28](https://github.com/CentOS/sig-cloud-instance-images/issues/28)

some people say, depending on our installation, that service will still be included, for backbacks compatibility, but that we will get a message to switch over. One example: [http://www.linuxbrigade.com/centos-7-rhel-7-systemd-commands/](http://www.linuxbrigade.com/centos-7-rhel-7-systemd-commands/)

### chkconfig (part of lockss installation instructions)

What about chkconfig? That changed too? Yes, now you want to use systemctl for the chkconfig commands also.. ([http://www.linuxbrigade.com/centos-7-rhel-7-systemd-commands/](http://www.linuxbrigade.com/centos-7-rhel-7-systemd-commands/))

Table of command equivalents [https://fedoraproject.org/wiki/SysVinit_to_Systemd_Cheatsheet](https://fedoraproject.org/wiki/SysVinit_to_Systemd_Cheatsheet)

from [https://fedoraproject.org/wiki/Systemd#Does_chkconfig_command_work_with_systemd.3F](https://fedoraproject.org/wiki/Systemd#Does_chkconfig_command_work_with_systemd.3F)
> Does chkconfig command work with systemd?
>
> Yes, for turning on/off services, compatibility has been provided both ways. chkconfig has been modified to call systemctl when dealing with systemd service files. Also systemctl automatically calls chkconfig when dealing with a traditional sysv init file.
>
> So either of the following commands does the same thing:
>
>     systemctl disable NetworkManager
>
> (or)
>
>     chkconfig NetworkManager off
>
> chkconfig --list doesn't list systemd services, only Sys V services. The output of chkconfig takes note of this, along with supplying additional information.
