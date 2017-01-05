# Install Win 7, upgrade to Win 10 & dual boot Ubuntu

Installing and using Windows is not a priority aspect of setting up a Linux mining rig, but if you want the ability to flash 
RX470s and RX480s in place with ATiFlash, or use the Polaris Bios Editor, or simply evaluate both Windows and Linux on the same 
hardware, a dual boot setup may well be indicated.

To achieve that, we install Windows first and Linux second. This page looks at the process of installing Win 7 Professional 
with an OEM licence, updating it, taking a clone of it for future convenience and then upgrading it to Windows 10 Professional,
followed by getting that to dual-boot.




## Windows 7 Professional OEM installation notes

### Install disk

You'll need the correct install disk according to the licence that you have and the computer that you intend to install it upon.
For retail licences, we can use Microsoft's Media Creation Tool but this will not supply install disks for OEM licences, so it may
be necessary to source an OEM install iso compatible with your architecture, and use [Rufus](https://rufus.akeo.ie/) to produce a UEFI USB key with it to
install Windows 7 onto a UEFI system.

### Issues with Win 10 upgrade
- network driver
- time and date
- IE 11
- hung up for ages checking updates


#### Windows 10 Upgrade Assistant 
failure to complete http://superuser.com/questions/1087029/windows-10-upgrade-assistant-stuck-at-99

Process then hangs on restart.


### Updating Windows 7 (the old fashioned way)

#### Device Drivers

In addition to the Realtek network card, which required a driver from the outset the USB3.0 controller also needs a driver to function with Windows 7, and I take
the opportunity to install the Intel display driver, all from ASRock's website. Newer Intel drivers don't seem to support the
Celeron G1840.

#### Offline Updates

When installing an old operating system, there are usually offline installers available for some of the larger updates, as discussed
in [this HowToGeek article](http://www.howtogeek.com/255435/how-to-update-windows-7-all-at-once-with-microsofts-convenience-rollup/). All
told, starting from a Win7 x64 SP1 install, these were the useful ones:

- KB3020369 Service Stack
- KB3125574 Convenience Update (May 2016) + restart
- KB3172605 Update rollup
- KB3185278 Update rollup Sept 2016
- KB3207752 Security Update Dec 2016

#### Windows Update

Following the 5 offline updates above, I had another go with Windows Update, installing a further 67 updates in the process. 
Then another round for 1 more, and again for 2 more, and yet again for 1 more. I'm starting to remember why I don't like 
Windows! After that, the Action Centre still complains of the lack of a virus scanner, so in goes Microsoft Security Essentials
as well. Then Windows Update pops up with another 10 updates, of which 4 succeed and 6 fail, requesting a restart. Restarted, 5 
new of which 4 succeed, 1 requires restart. Restart, try again. 

Finally, I get that one installed, and we're done with updates to the Win 7 Pro install.

### Backup the Windows 7 install (optional)

If you are proceeding to upgrade to Windows 10 post haste, this may not be worthwhile, but I saw fit to take a backup of the
installed Windows 7 system. Prior to doing so, I ran the Disk Cleanup tool from within Windows to clean up after upgrades, then 
the Disk Defragmenter, then scanned the disk for errors and shut down cleanly.

Backup can be achieved in all sorts of ways; I wanted a full image backup onto a Linux machine. So, I booted the Win7 machine 
with an Ubuntu 16.04.1 USB key in live mode ("Try Ubuntu without installing"). Then, on the destination Linux machine, I ran

`nc -l 19000|dd bs=16M status=progress of=/share/disk-raptor-win7.img`

to have netcat listen on TCP port 19000 and pipe what it gets to `dd`, which will write out the data to the file specified. Over
on the source machine, I issued a 

`dd bs=16M status=progress if=/dev/sda | nc 192.168.1.118 19000`

to have it read /dev/sda in 16MB chunks and send them across the network. This achieved a reasonably decent 97MB/sec, backing up
the whole 120GB disk in just over 20 minutes, and it provides me with the ability to roll back or to clone it, deactivating and 
relicensing as needed, if I should later have cause.



