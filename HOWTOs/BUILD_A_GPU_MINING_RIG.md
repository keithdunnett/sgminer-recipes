# Building a GPU mining rig - WIP JAN 2017

As I'm building another Linux GPU rig that will ultimately run sgminer, I thought I'd document it in a half-HOWTO, half photo journal. It's a minimalist setup in an open crate (i.e. with no case or frame), hardware is fairly typical of that used for Ethereum mining in early 2017. This page covers the building of the hardware up to the point of operating system installation; from there we'll explore dual-booting with Windows and Ubuntu 16.04.1, setting up the GPUs and getting the rig mining. 

## Chapter 1: Hardware Assembly

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

### Assemble CPU, motherboard and memory

Here we have an ASRock H81 Pro BTC, to which I've fitted 2GB of RAM, an Intel Celeron G1840 CPU and the stock cooler. The switch on the white wire is a simple push button switch that acts as a power switch when connected to the relevant "PWRBTN" pins on the motherboard. 

![Motherboard](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170104_170838.JPG "AsRock H81 Pro BTC")

### Set up the motherboard before use

#### Replace CMOS battery and set 'Clear CMOS' jumper (optional)

If you bought the board second-hand, as I did, it's no bad idea to set the "Clear CMOS" jumper before you ever power it up, or don't be surprised when you can't access UEFI thanks to Fast Boot mode. Usually you need to boot once with this set, then restore it to the default position so as to preserve the settings you make. I also took the opportunity to replace the CMOS backup battery as I had some on hand, although in practice they last for several years.

#### Install SATA cables etc. to the motherboard (optional at this stage)

We're about to turn this nice, clean picture into a forest of cables, through which it won't get any easier to install things to the motherboard. If you need SATA cables, USB headers for Corsair Link or whatever, it may be easiest to install them at this stage.

Tip: if you have a spare SATA cable, extra to the one you'll need for the primary SSD, it's no bad idea to install it onto the second SATA3 port and leave it accessible. Makes life much easier if you later want to clone or replace the disk.

### Assemble PSU and power cables

Similarly, if you're using a modular PSU, it's wise to assemble the required cables to the PSU before you're working in a confined space. In the photo below, the cables in the foreground are those needed by the motherboard, and in the background are cables providing 6 x Molex for the PCIe risers and 6 x 8-pin PCIe connectors for the GPUs.

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170104_161000.JPG "Corsair AX1200i")

### Connect power cables to the motherboard

This board takes 24-pin ATX, 8-pin EPS12V CPU connector and two Molex for extra power to the PCIe slots.

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170104_182233.JPG "Cabled up")

Once we put it all into into a crate, it'll look a bit like this:

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/HOWTOs/_20170104_183125.JPG "Crated up")

### Hardware issues to postpone

The first-time rig builder will be understandably eager to get the graphics cards and risers installed, and who'd blame them? In practice, if you're building with a mixture of new and second-hand parts (or even if not), it may not make much sense to start unboxing the GPUs until you have a working rig onto which to install them. You may also find it quicker and easier to install operating system(s) and driver(s) without multiple GPUs connected.

For the same reason, I prefer to leave issues of the physical rig layout open-ended until the rig is working. Photos abound of neatly-framed rigs with GPUs lined up a couple of inches apart; my Sapphire Nitros vent hot air through the top of the cards as well as the ends, so 6 cards in a row means quite a temperature gradient as one heats up the next. So, let's crack on with getting the machine ready.

### Prepare your environment to set up a new machine 

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

#### Install SSD

Or whatever other storage, but cheap SATA SSDs are plentiful. I'm using a 120GB ADATA SP550, more than adequate.

#### Insert UEFI OS install medium

Which normally means plug in a UEFI-capable USB stick containing the installer files for your operating system, though you could use a DVD-ROM or any other means. The point is: get it connected so we can set UEFI up accordingly. Information on how to make or where to buy a USB install key for a given operating system are available from the publishers.

#### Configure UEFI boot devices

Having installed the drives, you'll want to set the order of boot devices in UEFI, and potentially make any settings relevant to a solid state drive if you have one. When you reboot this time, you should be ready to install an operating system. Or two, if you like.

## Install operating system(s) and drivers

It is generally wise to get your operating system(s) installed and working at this point, before adding your GPUs to the system. Discussion of operating system installation and software configuration can be found in the next section; 

## Install your GPUs


# Chapter 2: Dual Boot Win10 + Ubuntu mining rig

We are, essentially, building a Linux rig. However, for the AMD RX470 and RX480 at least, you'll need Windows for the Polaris Bios Editor and also for ATiFlash 2.74, so you're faced with the choices of making Windows available on the rig itself or moving the GPUs to a Windows rig in order to flash them. For my purposes I'm going to set this rig up to dual-boot between Windows 10 Pro (which will mainly be used for flashing) and Ubuntu 16.04.1 for general mining use.

Installing and using Windows is not generally a priority aspect of setting up a Linux mining rig, but if you want the ability to flash RX470s and RX480s in place with ATiFlash, or use the Polaris Bios Editor, or simply evaluate both Windows and Linux on the same hardware, a dual boot setup may well be indicated.

To achieve that, we install Windows first and Linux second. This page examines the process I used of installing Win 7 Professional with an OEM licence, updating it, taking a clone of it for posterity and then upgrading it to Windows 10 Professional, followed by getting Windows 10 to dual-boot with Ubuntu 16.04.1 LTS. Those who have Windows 10 installed may wish to skip to the section on setting up dual boot.

# 2.1 Installing Windows 7 Professional

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

# 2.2: Upgrade to Windows 10 Professional

## 2.2.1 Windows 10 free upgrade for customers who use assistive technologies

The free upgrade offer for general use expired July 29th 2016; availability of the free Windows 10 upgrade thereafter is, as Microsoft explains, aimed at users of assistive technologies. 

The author has dyspraxia and makes *bona fide* use of both voice recognition and text-to-speech technologies, so has no ethical reservations in availing of Microsoft's continuing offer, but it's not necessary to have a disability in order to use such technologies. Users wondering if they can take advantage for a free upgrade should consider whether they use such things as voice recognition; it does seem to be open to anyone who chooses to avail of it.

## 2.2.2 Windows 10 upgrade procedure

With a fully up to date Win 7 Pro install, running the Windows 10 Upgrade Advisor proceeded to download and prepare the Windows 10 install files. Instructed to go ahead, it duly installed Windows 10 (with a couple of reboots in the process) and by and large, it behaved itself. It lost my keyboard settings and defaulted me to English(US) but that was fairly easily fixed in the Windows settings.

## 2.2.3 Windows 10 drivers

Drivers were installed from the ASRock website for Windows 10. The generic drivers shipped with Windows 10 do a reasonable job of compatibility with the ASRock H81 Pro BTC, but there are more specific drivers for some parts of the chipset and certain on-board peripherals.

## 2.2.4 Windows 10 issues

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

## 3.5 Configure Windows 10 for GPU mining (MAYBE, NOT MY AREA OF EXPERTISE)




We assume here that the intention is to fit the Windows 10 

# Section 4: Install & configure Ubuntu 16.04.1 LTS

This section describes configuring Ubuntu 16.04.1 LTS from install to running sgminer as a headless GPU mining rig. 

## 4.1 Install Ubuntu 16.04.1 LTS

### Installation media

To install in UEFI mode, you will need a UEFI-ready USB key with the Ubuntu installer on it. One of the easiest ways to produce this is to download the ISO under Windows and use Rufus as for Windows installs. Make sure when creating the USB key to select "GPT partitioning schema for UEFI" to have it install in UEFI mode.

Fixme: Linux instructions

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

### 4.2.1 Configure GRUB default behaviour

For my purposes I want to boot Ubuntu by default and boot Windows only when I say so; this is the default behaviour as configured by Ubuntu, and it can allow some flexibility for toggling between operating systems (see below). However, if for any reason you do actually want to boot Windows more than once (!) you can, optionally, set

```
GRUB_SAVEDEFAULT=true
GRUB_DEFAULT=saved
```

in /etc/default/grub and then `update-grub` to make choices persistent, i.e. continue to boot Ubuntu until I manually switch to Windows and vice versa. Unless you're aiming to run Windows most of the time, you probably don't want this, as defaulting to Ubuntu and changing that where required with `grub-reboot` is more flexible.

### 4.2.2 Be aware of the `grub-reboot` command in Linux

This allows you to specify a particular GRUB menu entry (by name or number) to be selected at next reboot, one time only. Thus a command such as `grub-reboot "Windows Boot Manager (on /dev/sda1)" && reboot` will have the system reboot to Windows 10; rebooting from Windows 10 will default to Linux. With adequate remote access to both operating systems, this can be used to facilitate remote-access dual-boot with remote network computing, but that's getting fancy.

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

### 4.5.1 Install AMD graphics driver & Accelerated Parallel Processing SDK

You'll need to download these directly from AMD - though by the time you're looking at a rig of this sort, you knew that. They'll install easily enough over SSH. Don't expect them to find anything just yet, of course. We'll need a GPU installed first, but before we go back to playing around with the hardware, we may as well try to get the rest of the software we need installed.

### 4.5.2 Install sgminer and any dependencies

This being a fresh install of Ubuntu 16.04.1, we'll uncover any dependencies that need to be installed. Note that installing amdgpu-pro pulled in some of the compiler components that we need.

[![asciicast](https://asciinema.org/a/br5nu6il2bryxrn2pwg1gpg9c.png)](https://asciinema.org/a/br5nu6il2bryxrn2pwg1gpg9c)






# Section 5: Setting up your GPUs


