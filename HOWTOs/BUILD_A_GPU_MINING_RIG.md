# Building a dual-boot Linux / Windows 10 GPU mining rig

As I'm building another Linux GPU rig that will ultimately run sgminer, I thought I'd document it in a half-HOWTO, half photo journal. It's a fairly minimalist setup in an open crate (i.e. with no case or frame), the hardware is fairly typical of that used for Ethereum mining in early 2017 although the principles should translate to different cards, algorithms and currencies. This document covers the building of the hardware up to the point of operating system installation; from there we'll explore dual-booting with Windows and Ubuntu 16.04.1, setting up the GPUs and getting the rig mining. 

## Chapter 1: Assembling the Mining Rig Hardware

This is not a treatise on the perfect hardware for mining, which varies according to time and place, but the list of hardware I'm using in this rig is as follows:

### Rig components

- Corsair AX1200i power supply
- ASRock H81 Pro BTC motherboard
- Intel Celeron G1840 processor
- 2GB PC-1333 memory
- ADATA 120GB solid state drive
- 6 x Sapphire RX470 Nitro+ graphics cards
- 6 x PCIe 1x-16x powered risers

### Other components used during setup

- VGA monitor
- keyboard
- mouse
- USB flash disks x 2
    - Windows installer
    - Ubuntu live installer
- Edimax SP2101-W mains power monitoring plug
    - Android device for monitoring power usage at the wall

### Assemble CPU, cooler, motherboard and memory

Here we have an ASRock H81 Pro BTC, to which I've fitted 2GB of RAM, an Intel Celeron G1840 CPU and the stock cooler. The switch on the white wire is a simple push button switch that acts as a power switch when connected to the relevant "PWRBTN" pins on the motherboard. 

![Motherboard](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170104_170838.JPG "AsRock H81 Pro BTC")

### Set up the motherboard before use

#### Replace CMOS battery and set 'Clear CMOS' jumper (optional)

If you bought the board second-hand, as I did, it's no bad idea to set the "Clear CMOS" jumper before you ever power it up, or don't be surprised when you can't access UEFI thanks to Fast Boot mode. Usually you need to boot once with this set, then restore it to the default position so as to preserve the settings you make. I also took the opportunity to replace the CMOS backup battery as I had some on hand, although in practice they last for several years.

#### Install SATA cables etc. to the motherboard (optional at this stage)

We're about to turn this nice, clean picture into a forest of cables, through which it won't get any easier to install things to the motherboard. If you need SATA cables, USB headers for Corsair Link or whatever, it may be easiest to install them at this stage.

Tip: if you have a spare SATA cable, extra to the one you'll need for the primary SSD, it's no bad idea to install it onto the second SATA3 port and leave it accessible. Makes life much easier if you later want to clone or replace the disk.

### Assemble PSU, motherboard power cables and optionally GPU power cables

Similarly, if you're using a modular PSU, it's sometimes wise to assemble the required cables to the PSU before you're working in a confined space. In the photo below, the cables in the foreground are those needed by the motherboard, and in the background are cables providing 6 x Molex for the PCIe risers and 6 x 8-pin PCIe connectors for the GPUs.

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170104_161000.JPG "Corsair AX1200i")

You might alternatively take the view (which, having tinkered with this setup, could have been a better idea) that there's more connections to make at the GPU end than at the PSU end, and that GPU installation is constrained by the available power cables, in which case assemble at this stage only what you need for the motherboard. It may prove easier (later) to connect the power cables to the GPUs and risers away from the rig, move and install the GPUs in such clusters (probably of 2 or 3) as suit the available power connections, then connect up the PSU end of those cables and the USB data cables from the PCIe slots.

Tip: my view on this changed when it occurred to me how much easier it was for assembly purposes to stand the Corsair PSU on end, modular connections uppermost, with the mains cable and rocker switch just overhanging the edge of the desktop to allow it to stand up. Makes fitting the modular connections vastly easier than with the unsecured PSU moving about in any other orientation. 

### Connect the necessary power cables to the motherboard

This board takes 24-pin ATX, 8-pin EPS12V CPU connector and two Molex for extra power supply to the PCIe slots.

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170104_182233.JPG "Cabled up")

Once we put it all into into a crate, it'll look a bit like this:

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170104_183125.JPG "Crated up")

### Hardware issues to postpone until later

The first-time rig builder will be understandably eager to get the graphics cards and risers installed, and who'd blame them? In practice, if you're building with a mixture of new and second-hand parts (or even if not), it may not make much sense to start unboxing the GPUs until you have a working rig onto which to install them. You may also find it quicker and easier to install operating system(s) and driver(s) without multiple GPUs connected.

For the same reason, I prefer to leave issues of the physical rig layout open-ended until the rig is working. Photos abound of neatly-framed rigs with GPUs lined up a couple of inches apart; my Sapphire Nitros vent hot air through the top of the cards as well as the ends, so 6 cards in a row means quite a temperature gradient as one card heats up the next. Many ways to handle that later. So, let's crack on with getting the machine ready.

### Prepare your working environment to set up a new machine 

Though the rig will eventually run headless, we'll need keyboard, video and mouse whilst configuring UEFI and installing an operating system, as well as network and power connections. At this stage, what we're looking to do is to test that what we've done works and that we can reach and configure the UEFI interface.

#### Display

The motherboard has VGA and HDMI outputs; I'm already using the only HDMI-capable display I have, so a 17" 1024x768 screen is what I'll be using initially. You could use an existing monitor, or even an HTDV depending on what your motherboard supports.

#### HID, network and power

Any old keyboard and mouse should do. I'm using a USB mouse and a PS/2 keyboard, as that's what I had. Wired Ethernet is by far the easiest when installing operating systems, so a cable is plugged in from the router.

AC power is supplied to the PSU through a power-monitoring wall plug; an Android tablet is set up to display the power usage. The PSU's Corsair Link connector is connected from a USB header on the motherboard to the PSU, for power monitoring and statistics when in Windows. This done, time to flick the switch and see what goes bang.

### First power on & UEFI configuration

This will usually involve a certain amount of messing around, checking connections, pulling and replugging until you stir the system into life; when you do you'll be greeted with the UEFI interface. Have a look around, make sure that all is as expected. Optionally, but recommended:

- update the UEFI firmware
- set onboard adapter as the primary GPU regardless of the PCIe slots
- set the state after a power outage to ON; you want it to come back to life.

#### Install SSD or primary hard disk

Or whatever other storage, but cheap SATA SSDs are plentiful. I'm using a 120GB ADATA SP550, more than adequate. If you're faced with a roughly cost-neutral choice between two smaller SSDs or one larger one, having two separate SSDs can be no bad thing for dual boot setups, swap files and other such, but that's a nicety not a necessity. The instructions in this HOWTO are carried out using a single SSD.

#### Insert your UEFI OS installer medium

Which normally means plug in a UEFI-capable USB stick containing the installer files for your operating system, though you could use a DVD-ROM or any other means. The point is: get it connected so we can set UEFI up accordingly. Information on how to make or where to buy a USB install key for a given operating system are available from the publishers.

#### Configure your UEFI boot devices

Having installed the drives, you'll want to set the order of boot devices in UEFI, and potentially make any settings relevant to a solid state drive if you have one. When you reboot this time, you should be ready to install an operating system. Or two, if you like. 

Note that you may need to revisit this later after installing both operating systems in a dual-boot configuration, as Windows 10's efforts to ensure its startup doesn't break will reconfigure UEFI to load the Windows Boot Manager by default, unless and until you expressly configure UEFI to favour the boot entry created by Ubuntu. We'll come back to that in due course, when setting up dual boot if we're doing so (it's optional, the instructions herein should work for installing a plain Ubuntu mining rig, though you'll have to skip over a lot of information about dual boot configuration and Windows if you do.

## Install your operating system(s) and drivers before returning to hardware

It is generally wise to get your operating system(s) installed and working at this point, before adding your GPUs to the system. Discussion of operating system installation, driver installation and software configuration can be found in the next section; we finish looking at building the hardware first for readability, but that does not suggest that all the hardware should be completed before you get an operating system up and running.

## Install your GPUs

When we come to install the GPUs, it should be among the last stages of the hardware build, after having installed the initial operating system(s) and any necessary drivers. It is possible to do things out of turn, but adding 6 GPUs to a rig involves quite a number of connections, a forest of cabling you'll prefer not to disturb too often and a mass of new logical devices and their various ports for the operating system. Installing the GPUs won't be the *last* thing you do in setting up a rig, far from it, but for an easy life: rig first, GPUs second.

### Unboxing and setting up the GPUs

Building your first "proper mining rig" isn't perhaps quite as exciting as setting up your first couple of GPUs, because it involves a certain amount of repetitive manual labour. Such as extricating this lot from its packaging and getting it all wired up.

![GPUs](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170114_223801.JPG "Sapphire RX470 pre install")

We're keeping the rig layout as loose as possible until we see how everything works, but that doesn't mean the easiest approach is to handle each card individually. Have the GPUs out of their boxes and bags, and store the retail boxes if you plan to resell the GPUs later. You won't need driver CDs or other paraphernalia, just the GPUs, to which fit your 1x-16x PCIe risers. 

### Cabling and connections

Once you have your GPUs and risers plugged together, each will present three connectors that will need cabling:

- a PCIe power connector on the GPU itself (8-pin PCIe on the Sapphire Nitro series)
- a power connector on the PCIe riser, Molex for preference but variants with SATA and floppy power connectors exist
    - most powered risers on the market have an onboard Molex connector with a SATA adapter
    - choose ones with 60cm USB A-to-A cables. You don't want a bunch of GPUs within 30cm of cable from the motherboard.
- a data connector (physically of USB3.0 type A) used to extend the PCIe lane to the riser 

Of these, the power connections are likely to be more awkward and more restrictive of the hardware layout than the USB connections, so assuming that you're using a modular PSU (who isn't, these days?) it often makes sense to connect up your power cables to the GPUs and risers first, then take those assemblies to the PSU and motherboard, otherwise you'll end up fighting to fit the power cables in confined space. I also now install the other end of the data connection (the PCIe adapters and USB A to A cables) to the PCIe slots in advance, so the data cables are ready to connect up when I move the GPUs into place. This seems easier and more effective than trying to make the power connections in place without disturbing other connections.

### Consider how to handle GPU flashing (and BIOS editing)

This document describes the installation of a dual-boot rig with Windows 10 and Ubuntu, so it assumes that the GPUs can be installed first and flashed in situ, optionally via a Remote Desktop connection. If you're installing a Linux only rig, you'll want to give a thought to flashing the GPUs before you connect them to it or to your ability to run Windows on it, as the flashing tool requires it and no longer works from DOS, including in the command line version.

Depending upon what you are doing and with what hardware - but very certainly for mining Ethereum on Sapphire RX470s - you will effectively be all but forced to flash a modified BIOS to run the rig efficiently, or in some cases at all using stock BIOS without turning down the intensity or the number of GPU threads you might otherwise run. On the rig depicted here, mining Ethereum with stock BIOS at intensity 20 and one thread per GPU produced 141Mh/s for 1136W at the wall, power usage of 8.05W/Mh/s, whilst with two threads per GPU it sought to draw over 1200W and triggered the PSU's overload protection.

Using a fairly roughly modified BIOS, under Linux and without much in the way of optimisations, it was readily possible to achieve 159Mh/s for 930W at the wall from the same hardware if seeking optimal power efficiency (5.85W/Mh/s, a saving of > 27% versus the stock BIOS), or if seeking hashrate at the expense of a modicum of efficiency, to achieve 166Mh/s for 995W at the wall (6W/Mh/s, an efficiency saving still > 25% versus the stock config. These figures are still not in themselves spectacularly efficient; the rig with no GPUs consumes 30W at idle or 48W with heavy CPU usage so the 6 GPUs are consuming ~160W apiece at the wall, but running the rig with the stock BIOS would be fairly hopeless.

Details of the BIOS settings that work best for a given application are best researched on a case by case basis, but most rigs will benefit substantially from a modified BIOS and therefore users are advised to consider making provision to flash the GPUs, either by equipping the rig itself with Windows 10 Professional (recommended for maximal flexibility) or connecting the cards to another system for flashing. 

### Initial layout of rig hardware

Really, do whatever works as long as you give the GPUs some space. Here's what my latest rig, 'raptor', looked like during initial setup with the first three GPUs installed. Yes, that is a stray Gigabit switch. Lazy I admit, but it's temporary. 

![GPUs installing](https://github.com/magick777/sgminer-recipes/blob/master/HOWTOs/_20170115_005539.JPG "raptor mid install")

and here's the state in which it's likely to spend the first few days or weeks of its life while I'm testing and until I get around to installing it properly somewhere:

![GPUs connected](https://github.com/magick777/sgminer-recipes/blob/master/HOWTOs/_20170115_005705.JPG "raptor with gpus installed")

Test that all connections are correctly made. With the Sapphire GPUs the LEDs are a good indication at power on and before we get to operating system level, but sooner or later you'll need to establish that Linux can actually see the requisite number of GPUs.

### FIXME MOVE to sw Check your GPUs are recognised
```
root@raptor:~# lspci | grep VGA
00:02.0 VGA compatible controller: Intel Corporation Xeon E3-1200 v3/4th Gen Core Processor Integrated Graphics Controller (rev 06)
01:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Device 67df (rev cf)
02:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Device 67df (rev cf)
03:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Device 67df (rev cf)
04:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Device 67df (rev cf)
05:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Device 67df (rev cf)
06:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Device 67df (rev cf)
```

and that those GPUs are recognised by your OpenCL platform. If using amdgpu-pro, it has its own copy of clinfo, so:

```
root@raptor:~# /opt/amdgpu-pro/bin/clinfo | grep PCI
  Device Topology:				 PCI[ B#1, D#0, F#0 ]
  Device Topology:				 PCI[ B#2, D#0, F#0 ]
  Device Topology:				 PCI[ B#3, D#0, F#0 ]
  Device Topology:				 PCI[ B#4, D#0, F#0 ]
  Device Topology:				 PCI[ B#5, D#0, F#0 ]
  Device Topology:				 PCI[ B#6, D#0, F#0 ]
```

or, if you've already built and installed sgminer against your OpenCL platform, then try:

```
root@raptor:~# sgminer --ndevs
[01:21:59] CL Platform vendor: Advanced Micro Devices, Inc.                    
[01:21:59] CL Platform name: AMD Accelerated Parallel Processing                    
[01:21:59] CL Platform version: OpenCL 2.0 AMD-APP (2117.10)                    
[01:21:59] Platform devices: 6                    
[01:21:59] 	0	Ellesmere                    
[01:21:59] 	1	Ellesmere                    
[01:21:59] 	2	Ellesmere                    
[01:21:59] 	3	Ellesmere                    
[01:21:59] 	4	Ellesmere                    
[01:21:59] 	5	Ellesmere                    
[01:21:59] 6 GPU devices max detected 
```

This is the output we want to see before sgminer is (hopefully) ready to start mining. 


# Chapter 2: Operating Systems: Dual Boot Win10 + Ubuntu

We are, essentially, building a Linux rig. However, for the AMD RX470 and RX480 at least, you'll need Windows for the Polaris Bios Editor and also for ATiFlash 2.74, so you're faced with the choices of making Windows available on the rig itself or moving the GPUs to a Windows rig in order to flash them. For my purposes I'm going to set this rig up to dual-boot between Windows 10 Pro (which will mainly be used for flashing) and Ubuntu 16.04.1 for general mining use.

To achieve that, we install Windows first and Linux second. This page examines the process I used of installing Win 7 Professional with an OEM licence, updating it, taking a clone of it for posterity and then upgrading it to Windows 10 Professional, followed by getting Windows 10 to dual-boot with Ubuntu 16.04.1 LTS. Those who have Windows 10 installed may wish to skip to the section on setting up dual boot.

## 2.1 Installing Windows 7 Professional (if needed)

### Windows 7 Professional x64 OEM installation notes

#### Windows 7 Installation media

You'll need the correct install disk according to the licence that you have and the computer that you intend to install it upon.
For retail licences, you can use Microsoft's Media Creation Tool but this will not supply install disks for OEM licences, so it may
be necessary to source an OEM install iso compatible with your architecture and use [Rufus](https://rufus.akeo.ie/) to produce a UEFI USB key with it to
install Windows 7 onto a UEFI system. 

To save yourself some time and hassle later, once you've made your Win 7 SP1 UEFI USB key, also find and download to the same USB key any drivers you'll need early on - especially for the network card, which will be needed in order to download anything else. You'll also do well to grab the five offline installers linked in the 'Offline Updates' section below, which will bring your Windows 7 install up to speed with most updates to late 2016.

Tip: if you're going to require UEFI installation media for Ubuntu 16.04.1, you can also produce that with  [Rufus](https://rufus.akeo.ie/) from an Ubuntu ISO image. Not a bad idea if you're doing the same for Windows 7.

#### Windows 7 installation issues

Windows 7 Professional installed OK, but needed the Ethernet drivers from ASRock to be able to access the network. As a newly installed motherboard without useful hardware clock data, it had to be instructed to get time info from the network to set the correct time and date before SSL certificates would validate.

##### Windows 7 activation

My Windows 7 OEM licence required telephone activation, but this was painless enough through Microsoft's automated telephone activation service, and worked as expected first time.

##### Windows 7 update issues

The Windows 7 SP1 ISO from which I installed is old, with a vast amount of changes to Windows 7 since. The initial effect of this was that everything broke in spectacular fashion:

- the Windows 10 Upgrade adviser performed the download, but got stuck at 99% preparing the install files.
- Windows Update updated itself, but then refused to do more than to spend an eternity "checking for updates".
- installing Internet Explorer 11 resulted in a browser that wouldn't launch.

Fed up with that, and realising the age of the operating system, I resorted to the expedient of downloading standalone installers to a USB key and running each in turn.  

#### Updating Windows 7 with standalone installers

##### Windows 7 Device Drivers

In addition to the Realtek network card, which required a driver from the outset the USB3.0 controller also needs a driver to function with Windows 7, and I took
the opportunity to install the Intel display driver, all from ASRock's website. As with the offline updates below, it may be worth having these on your USB key when you install Windows 7.

##### Windows 7 Offline Updates

When installing an old operating system, there are usually offline installers available for some of the larger updates, as discussed
in [this HowToGeek article](http://www.howtogeek.com/255435/how-to-update-windows-7-all-at-once-with-microsofts-convenience-rollup/). All
told, starting from a Win7 x64 SP1 install, these were the useful ones and should be applied in this order:

- [KB3020369 Service Stack](http://www.catalog.update.microsoft.com/Search.aspx?q=3020369)
- [KB3125574 Convenience Update (May 2016)](http://www.catalog.update.microsoft.com/Search.aspx?q=3125574) + restart
- [KB3172605 Update rollup](http://www.catalog.update.microsoft.com/Search.aspx?q=3172605)
- [KB3185278 Update rollup Sept 2016](http://www.catalog.update.microsoft.com/Search.aspx?q=3185278)
- [KB3207752 Security Update Dec 2016](http://www.catalog.update.microsoft.com/Search.aspx?q=3207752)

These brought the system to a state where Windows Update and Internet Explorer 11 were usable, so I proceeded with online updating from there.

##### Windows 7 Update - the final frontier

Following the 5 offline updates above, I had another go with Windows Update, installing a further 67 updates in the process. 
Then another round for 1 more, and again for 2 more, and yet again for 1 more. I'm starting to remember why I don't like 
Windows! After that, the Action Centre still complains of the lack of a virus scanner, so in goes Microsoft Security Essentials as well. Then Windows Update pops up with another 10 updates, of which 4 succeed and 6 fail, requesting a restart. Restarted, 5 new of which 4 succeed, 1 requires restart. Restart, try again. Finally, I get that one installed, and we're done with updates to the Win 7 Pro install.

#### Backup the Windows 7 install via Linux (optional)

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

## 2.2: Upgrade to Windows 10 Professional

### 2.2.1 Windows 10 free upgrade for customers who use assistive technologies

The free upgrade offer for general use expired July 29th 2016; availability of the free Windows 10 upgrade thereafter is, as Microsoft explains, aimed at users of assistive technologies. 

The author has dyspraxia and makes *bona fide* use of both voice recognition and text-to-speech technologies, so has no ethical reservations in availing of Microsoft's continuing offer, but it's not necessary to have a disability in order to use such technologies. Users wondering if they can take advantage for a free upgrade should consider whether they use such things as voice recognition; it does seem to be open to anyone who chooses to avail of it.

### 2.2.2 Windows 10 upgrade procedure

With a fully up to date Win 7 Pro install, running the Windows 10 Upgrade Advisor proceeded to download and prepare the Windows 10 install files. Instructed to go ahead, it duly installed Windows 10 (with a couple of reboots in the process) and by and large, it behaved itself. It lost my keyboard settings and defaulted me to English(US) but that was fairly easily fixed in the Windows settings.

### 2.2.3 Windows 10 hardware drivers

Drivers were installed from the ASRock website for Windows 10. The generic drivers shipped with Windows 10 do a reasonable job of compatibility with the ASRock H81 Pro BTC, but there are more specific drivers for some parts of the chipset and certain on-board peripherals.

### 2.2.4 Windows 10 issues

I had a couple of unexplained issues in the course of the upgrade to Windows 10 and setting up afterwards. These were not sufficiently reproducible to complain bitterly.

- The machine froze a couple of times, requiring a hard reset.
- An Ubuntu 16.04.1 USB key suffered damage to its EFI partition and would not boot. Windows 10 happily made another, though.


# Section 3: Install & configure Windows 10 Professional

## 3.1 Get Windows 10 installed

In my case, I've arrived at a Windows 10 Professional install by way of an upgrade as described above. You could equally well install Windows 10 directly and license it accordingly, or be implementing a small rig on an off-the-shelf PC with Windows 10 pre-installed. Whichever way, hereinafter we'll assume that Windows 10 is installed and running on your primary SSD. If you do need to install Windows 10, you can use Microsoft's [Media Creation Tool](https://www.microsoft.com/software-download/windows10) to produce a USB installer, or download ISO files from the same link under Linux.

## 3.2 Shrink Windows 10 in preparation for dual boot (optional)

This assumes you intend to boot Windows 10 and Ubuntu 16.04.1 from the same SSD, which is entirely feasible, if given to breaking your boot sooner or later. It would equally be possible to install a second SSD for a second operating system, in which case no need to shrink the Windows 10 install. If you do need to, you can do this in one of two ways.

### Windows 10 Disk Management

You can shrink your Windows partition using the inbuilt Disk Management tool; [instructions here](http://www.howtogeek.com/howto/windows-vista/resize-a-partition-for-free-in-windows-vista/).

### Linux Live USB + gparted

Or, from a live Linux USB (the one in the Ubuntu install ISO will do fine), run 'gparted' and shrink the partition from there. Either way, once your Windows 10 install is resized down to about half the disk, you can allow the Ubuntu installer to use the free space as it sees fit.

## 3.3 Configure Windows 10 for remote desktop access

### 3.3.1 Microsoft RDP (Remote Desktop)

Assuming you're using Windows 10 Professional, you can configure it to allow remote desktop access from around your local network [as described in this article](http://www.pcadvisor.co.uk/how-to/windows/how-use-remote-desktop-connection-in-windows-10-3632113/). If you have Windows 10 Home, unfortunately this version does not support the RDP server component and you will have to look to a third-party solution, such as RealVNC or similar.

Once you have enabled Remote Desktop on your rig's Windows 10 Professional install, configure an RDP client (or a multi-protocol remote access client, such as Remmina in Linux) with the rig's IP address or local DNS host name and the login credentials to use with Windows 10 and it should then be possible to access the rig's Windows 10 desktop from machines around your local network. 

When combined with the necessary Windows tools for BIOS editing and flashing and the dual-boot setup described below in the section on Ubuntu 16.04.1, this can provide an easy means of tweaking your GPU BIOS configuration and reflashing your GPUs as may be required. In essence, Linux can be set up to reboot into Windows 10 on command via grub-reboot (can be done remotely over SSH), then you can access your Windows 10 desktop across the network with RDP and carry out any BIOS editing or flashing required. Reboot Windows and the system will (unless configured otherwise) default to Linux on the next boot. Meaning that, until something breaks it (and something probably will), you can have a remote access dual boot that defaults to a Linux mining rig, but can be brought up running Windows 10 remotely on demand. Currently I'm using Windows only for BIOS editing and flashing, but it should be possible also to set it up for GPU mining, software testing or other applications as the case may be.

## 3.4 Configure Windows 10 for bios editing and flashing

You'll want the standard assortment of software for flashing and managing Polaris cards, namely

- [Polaris Bios Editor](https://github.com/caa82437/PolarisBiosEditor)
- [ATIWinFlash 2.74](link broken)

## 3.5 Configure Windows 10 for GPU mining (MAYBE, NOT MY AREA OF EXPERTISE)




# Section 4: Install & configure Ubuntu 16.04.1 LTS

This section describes configuring Ubuntu 16.04.1 LTS from install to running sgminer as a headless GPU mining rig. 

## 4.1 Install Ubuntu 16.04.1 LTS

### Installation media

To install in UEFI mode, you will need a UEFI-ready USB key with the Ubuntu installer on it. One of the easiest ways to produce this is to download the ISO under Windows and use Rufus as for Windows installs. Make sure when creating the USB key to select "GPT partitioning schema for UEFI" to have it install in UEFI mode.

Fixme: Linux instructions to generate USB media

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

## 4.2 Configure Ubuntu 16.04.1 for UEFI dual boot (optional)

Ubuntu will normally configure the proper entries in the GRUB menu where it detects another operating system present, and GRUB will happily hand off to the Windows Boot Manager where so requested, so sometimes there's not much configuration to be done.

### 4.2.1 Configure default behaviour of the GRUB bootloader

For my purposes I want to boot Ubuntu by default and boot Windows only when I say so; this is the default behaviour as configured by Ubuntu, and it can allow some flexibility for toggling between operating systems (see below). However, if for any reason you do actually want to boot Windows more than once (!) you can, optionally, set

```
GRUB_SAVEDEFAULT=true
GRUB_DEFAULT=saved
```

in /etc/default/grub and then `update-grub` to make choices persistent, i.e. continue to boot Ubuntu until I manually switch to Windows and vice versa. Unless you're aiming to run Windows most of the time, *you probably don't want this*, as defaulting to Ubuntu and changing that where required with `grub-reboot` is more flexible.

### 4.2.2 Be aware of the `grub-reboot` command in Linux

This allows you to specify a particular GRUB menu entry (by name or number) to be selected at next reboot, one time only. Thus a command such as `grub-reboot "Windows Boot Manager (on /dev/sda1)" && reboot` will have the system reboot to Windows 10; rebooting from Windows 10 will default to Linux. With adequate remote access to both operating systems, this can be used to facilitate remote-access dual-boot with remote network computing, a bit fancy perhaps.

## 4.3 Configure Ubuntu 16.04.1 for remote access

There is a policy decision to be taken at this point regarding how much use we want of a GUI on a Linux mining rig and how much prefer to be able to do over an SSH connection. There are arguments on both sides; as a simple example, it may prove simpler to load up Firefox and download the amdgpu-pro drivers and APP SDK on the machine itself. Unless you already have them on your desktop, as I do, in which case transferring the files with scp is no hassle.

So, it's horses for courses. In this HOWTO, I adopt an approach that does almost everything over an SSH connection and optionally allows for running without Xorg (as suits a headless mining rig). However, I also run a mixed-use desktop where Xorg on the integrated GPU co-exists with some AMD GPUs dedicated to mining, so we'll endeavour to persuade this rig to work with and without Xorg.

### 4.3.1 Enable SSH access

#### 4.3.1.1 Install the OpenSSH server

Having previously updated the packages, it's as easy as:

`apt install openssh-server`

#### 4.3.1.2 Enable root access by public-key based authentication

For a dedicated mining rig, we'll mostly want to work as root so as to have full access to logs, drivers, package installs, process priorities and suchlike. To log in as root, we allow access by public key not by password, so we need firstly to log in as an unprivileged user, then gain root privileges, create a .ssh/authorized_keys file in root's home directory, populate it with the public keys that should have access and set the proper file permissions, after which we should be able to log in as root from a machine with that key. If you've no idea how passwordless SSH access works, have a read of [this article](http://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/), or (for console newbies) for a 4m30s screencap of me setting it up with an existing SSH identity, mistakes and all, [try this](https://asciinema.org/a/7otbzhctnon4tje4xfmadu1ts). You may also need to `chmod 600 /root` if StrictModes is set.

## 4.4 Configure Ubuntu 16.04.1 for console-only operation (optional)

If you intend to run a headless mining rig, it's perfectly feasible to force Xorg onto the integrated GPU (and maybe we'll look at that later) but it's not in fact necessary to run Xorg at all. To boot into runlevel 3 (console) on a systemd distro such as Ubuntu 16.04.1, add "systemd.unit=multi-user.target" to GRUB_CMDLINE_LINUX_DEFAULT in /etc/default/grub, update grub and the system will boot to console. If we fancy keeping the choice available at boot time (I've not done this before!), it *should* be possible to default to console boot as above, and create another script in /etc/grub.d to generate parallel menu entries for a graphical boot target for each Linux kernel by processing the output of /etc/grub.d/10_linux and making a couple of changes to the output with sed. Adding something such as:

```
#!/bin/bash
set -e
source /etc/grub.d/10_linux | sed -e "s/multi-user.target/graphical.target/g" | sed -e "s/Ubuntu/Ubuntu\+GUI/g"
```
into a file 11_linux_gui in /etc/grub.d/ will do the trick. If you'd like to watch the (warts and all) transcript:

[![asciicast](https://asciinema.org/a/c2ily1pbz3jpq6cbc81eyhriq.png)](https://asciinema.org/a/c2ily1pbz3jpq6cbc81eyhriq)

So, whilst we still have Xorg installed, our default is now to boot to Ubuntu in runlevel 3 (multi-user, console) and we retain the options to boot Ubuntu with the GUI or Windows 10 Professional. So far, so good.

## 4.5 Configure Ubuntu 16.04.1 for OpenCL GPU mining

### 4.5.1 Install the AMD graphics driver & the Accelerated Parallel Processing SDK

You'll need to download these directly from AMD - though by the time you're looking at a rig of this sort, you knew that. They'll install easily enough over SSH. Don't expect them to find anything just yet, of course. We'll need a GPU installed first, but before we get back to playing around with the hardware, we may as well try to get the rest of the software we need installed.

#### 4.5.1.1 AMD GPU driver bugs (Linux)

The amdgpu-pro Linux driver is released as a beta, so expecting production-quality code might be optimistic, but AMD still has a lot of bugs to squash. 

##### amdgpu-pro v16.50

AMD's latest driver release contains a new and buggy OpenCL compiler version that makes a mess of compiling the kernel binaries from OpenCL sources, producing oversized binaries that merely throw hardware errors. In other respects the new driver carries some bug fixes, but as supplied it won't compile a working OpenCL kernel. Besides downgrading back to 16.40 (which AMD doesn't make straight-forward if you don't have a copy lying around), there are a couple of possible workarounds:

###### Use pre-compiled OpenCL binaries (this may happen by default if upgrading)

The bug is specific to compiling new kernels, so if you're upgrading a rig that has built a suitable kernel binary under 16.40, it will still be able to use that binary under 16.50. This may be adequate for established rigs that are not likely to change function, but will lead to undefined behaviours if any change of settings is made that requires a kernel compile.

###### Forcibly overwrite the OpenCL library with an older, working version

Alternatively you can persuade the 16.50 amdgpu-pro driver to work with the OpenCL ICD from version 16.40 by first installing version 16.50, then going to the install directory for 16.40 and running `dpkg -i opencl-amdgpu-pro-icd_16.40-348864*`, which will overwrite just the OpenCL ICD (responsible for the offending compiler) and permit the 16.50 core driver to function with the older OpenCL. 

##### amdgpu-pro v16.40



##### amdgpu-pro v16.30

Initial release. Had a few bugs, too long ago for me to remember. Versions 16.50 and 16.40 are the realistic options within Ubuntu 16.04.1


### 4.5.2 Install sgminer and any dependencies

This being a fresh install of Ubuntu 16.04.1, we'll uncover any dependencies that need to be installed. Note that installing amdgpu-pro pulled in some of the compiler components that we need. Here's a run-through of the initial install of sgminer:

[![asciicast](https://asciinema.org/a/br5nu6il2bryxrn2pwg1gpg9c.png)](https://asciinema.org/a/br5nu6il2bryxrn2pwg1gpg9c)


### 4.5.3 Optionally, configure multiple sgminer installs to coexist

### 4.5.4 Configure a startup script for sgminer


