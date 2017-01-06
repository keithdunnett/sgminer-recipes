# Install Windows & dual boot with Ubuntu

Installing and using Windows is not a priority aspect of setting up a Linux mining rig, but if you want the ability to flash 
RX470s and RX480s in place with ATiFlash, or use the Polaris Bios Editor, or simply evaluate both Windows and Linux on the same hardware, a dual boot setup may well be indicated.

To achieve that, we install Windows first and Linux second. This page examines the process I used of installing Win 7 Professional with an OEM licence, updating it, taking a clone of it for posterity and then upgrading it to Windows 10 Professional, followed by getting Windows 10 to dual-boot with Ubuntu 16.04.1 LTS. Those who have Windows 10 installed may wish to skip to the section on setting up dual boot.

# Section 1: Windows 7 install

## Windows 7 Professional x64 OEM installation notes

### Installation media

You'll need the correct install disk according to the licence that you have and the computer that you intend to install it upon.
For retail licences, you can use Microsoft's Media Creation Tool but this will not supply install disks for OEM licences, so it may
be necessary to source an OEM install iso compatible with your architecture and use [Rufus](https://rufus.akeo.ie/) to produce a UEFI USB key with it to
install Windows 7 onto a UEFI system. 

To save yourself some time and hassle later, once you've made your Win 7 SP1 UEFI USB key, also find and download to the same USB key any drivers you'll need early on - especially for the network card, which will be needed in order to download anything else. You'll also do well to grab the five offline installers linked in the 'Offline Updates' section below, which will bring your Windows 7 install up to speed with most updates to late 2016.

Tip: if you're going to require UEFI installation media for Ubuntu 16.04.1, you can also produce that with  [Rufus](https://rufus.akeo.ie/) from an Ubuntu ISO image. Not a bad idea if you're doing the same for Windows 7.

### Windows 7 installation issues

Windows 7 Professional installed OK, but needed the Ethernet drivers from ASRock to be able to access the network. As a newly installed motherboard without useful hardware clock data, it had to be instructed to get time info from the network to set the correct time and date before SSL certificates would validate.

#### Windows 7 activation

My Windows 7 OEM licence required telephone activation, but this was painless enough through Microsoft's automated telephone activation service, and worked as expected first time.

#### Windows 7 update issues

The Windows 7 SP1 ISO from which I installed is old, with a vast amount of changes to Windows 7 since. The initial effect of this was that everything broke in spectacular fashion:

- the Windows 10 Upgrade adviser performed the download, but got stuck at 99% preparing the install files.
- Windows Update updated itself, but then refused to do more than to spend an eternity "checking for updates".
- installing Internet Explorer 11 resulted in a browser that wouldn't launch.

Fed up with that, and realising the age of the operating system, I resorted to the expedient of downloading standalone installers to a USB key and running each in turn.  

### Updating Windows 7 with standalone installers

#### Device Drivers

In addition to the Realtek network card, which required a driver from the outset the USB3.0 controller also needs a driver to function with Windows 7, and I took
the opportunity to install the Intel display driver, all from ASRock's website. As with the offline updates below, it may be worth having these on your USB key when you install Windows 7.

#### Offline Updates

When installing an old operating system, there are usually offline installers available for some of the larger updates, as discussed
in [this HowToGeek article](http://www.howtogeek.com/255435/how-to-update-windows-7-all-at-once-with-microsofts-convenience-rollup/). All
told, starting from a Win7 x64 SP1 install, these were the useful ones and should be applied in this order:

- [KB3020369 Service Stack](http://www.catalog.update.microsoft.com/Search.aspx?q=3020369)
- [KB3125574 Convenience Update (May 2016)](http://www.catalog.update.microsoft.com/Search.aspx?q=3125574) + restart
- [KB3172605 Update rollup](http://www.catalog.update.microsoft.com/Search.aspx?q=3172605)
- [KB3185278 Update rollup Sept 2016](http://www.catalog.update.microsoft.com/Search.aspx?q=3185278)
- [KB3207752 Security Update Dec 2016](http://www.catalog.update.microsoft.com/Search.aspx?q=3207752)

These brought the system to a state where Windows Update and Internet Explorer 11 were usable, so I proceeded with online updating from there.

#### Windows Update

Following the 5 offline updates above, I had another go with Windows Update, installing a further 67 updates in the process. 
Then another round for 1 more, and again for 2 more, and yet again for 1 more. I'm starting to remember why I don't like 
Windows! After that, the Action Centre still complains of the lack of a virus scanner, so in goes Microsoft Security Essentials
as well. Then Windows Update pops up with another 10 updates, of which 4 succeed and 6 fail, requesting a restart. Restarted, 5 
new of which 4 succeed, 1 requires restart. Restart, try again. 

Finally, I get that one installed, and we're done with updates to the Win 7 Pro install.

### Backup the Windows 7 install via Linux (optional)

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
relicensing as needed, if I should later have cause. That done, I booted back into Windows 7 and ran the Windows 10 Upgrade Adviser anew.

# Section 2: Upgrade from Win 7 Pro to Win 10 Pro

## Windows 10 free upgrade for customers who use assistive technologies

The free upgrade offer expired July 29th 2016; availability of the free Windows 10 upgrade thereafter is, as Microsoft explains, aimed at users of assistive technologies. The author has dyspraxia and makes routine use of both voice recognition and text-to-speech technologies, so has no ethical reservations in availing of Microsoft's continuing offer, but it's not necessary to have a disability in order to use and to benefit from assistive technologies. 

## Windows 10 upgrade procedure

With a fully up to date Win 7 Pro install, running the Windows 10 Upgrade Advisor proceeded to download and prepare the Windows 10 install files. Instructed to go ahead, it duly installed Windows 10 (with a couple of reboots in the process) and by and large, it behaved itself. It lost my keyboard settings and defaulted me to English(US) but that was fairly easily fixed in the Windows settings.

## Windows 10 drivers

Drivers were installed from the ASRock website for Windows 10. The generic drivers shipped with the OS do a reasonable job of compatibility with the ASRock H81 Pro BTC, but there are more specific drivers for some parts of the chipset and certain on-board peripherals.

## Windows 10 issues

I had a couple of unexplained issues in the course of the upgrade to Windows 10 and setting up afterwards. These were not sufficiently relialy 

- The machine froze a couple of times, requiring a hard reset.
- An Ubuntu 16.04.1 USB key suffered damage to its EFI partition and would not boot.



# Section 3: Shrink Windows 10 in preparation for dual boot

We assume here that the intention is to fit the Windows 10 

# Section 4: Install & configure Ubuntu 16.04.1 LTS

## Install Ubuntu 16.04.1 LTS

### Installation media

To install in UEFI mode, you will need a UEFI-ready USB key with the Ubuntu installer on it. One of the easiest ways to produce this is to download the ISO under Windows and use Rufus as for Windows installs. Make sure when creating the USB key to select "GPT partitioning schema for UEFI" to have it install in UEFI mode

### Running the Ubuntu installer

Installing and configuring Ubuntu should be more or less the same whether you're running it as dual-boot or on its own; boot into the installer and follow the prompts. The default options are largely safe; make sure you **don't** tell it to erase the disk if you are sharing a single disk between Windows and Ubuntu; the default will make use of the free space. When it asks about bootloader configuration, install alongside Windows Boot Manager if you're going for a dual-boot setup. Fairly soon you should be booted into your new Ubuntu install.

### Updating Ubuntu software packages

There are graphical front-ends to Ubuntu package management, but few of them are an improvement on the speed and flexibility of the command line interface. From the dash (button top left, with Ubuntu logo) search for Terminal, and run that when you find it. Then, in terminal, something like this should be sufficient to update most Ubuntu packages.

```
sudo su -
apt update
apt --list-upgradable
apt upgrade
```
### Enable SSH access



### Install AMDAPPSDK3.0 & amdgpu-pro 16.50 (or newer)





# Section 5: 

