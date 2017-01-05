# Building a GPU mining rig - WIP UNFINISHED

As I'm building another Linux GPU rig that will ultimately run sgminer, I thought I'd document it. It's a minimalist setup in an open crate, hardware is fairly typical of that used for Ethereum mining in early 2017.

This first page covers the building of the hardware up to the point of operating system installation.

## Assemble the hardware

### Assemble the CPU, motherboard and memory

Here we have an ASRock H81 Pro BTC, to which I've fitted 2GB of RAM, an Intel Celeron G1840 CPU and the stock cooler. The switch on the white wire is a simple push button switch that acts as a power switch when connected to the relevant "PWRBTN" pins on the motherboard. 

![Motherboard](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/_20170104_170838.JPG "AsRock H81 Pro BTC")

### Set up the motherboard before use

#### Replace CMOS battery and set 'Clear CMOS' jumper (optional)

If you bought the board second-hand, as I did, it's no bad idea to set the "Clear CMOS" jumper before you ever power it up, or don't be surprised when you can't access UEFI thanks to Fast Boot mode. Usually you need to boot once with this set, then restore it to the default position so as to preserve the settings you make. I also took the opportunity to replace the CMOS backup battery as I had some on hand, although in practice they last for several years.

#### Install SATA cables etc. to the motherboard (optional at this stage)

We're about to turn this nice, clean picture into a forest of cables, through which it won't get any easier to install things to the motherboard. If you need SATA cables, USB headers for Corsair Link or whatever, it may be easiest to install them at this stage.

Tip: if you have a spare SATA cable, extra to the one you'll need for the primary SSD, it's no bad idea to install it onto the second SATA3 port and leave it accessible. Makes life much easier if you later want to clone or replace the disk.

### Assemble the PSU and power cables

Similarly, if you're using a modular PSU, it's wise to assemble the required cables to the PSU before you're working in a confined space. In the photo below, the cables in the foreground are those needed by the motherboard, and in the background are cables providing 6 x Molex for the PCIe risers and 6 x 8-pin PCIe connectors for the GPUs.

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/_20170104_161000.JPG "Corsair AX1200i")

### Connect the power cables to the motherboard

This board takes 24-pin ATX, 8-pin EPS12V CPU connector and two Molex for extra power to the PCIe slots.

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/_20170104_182233.JPG "Cabled up")

Once we put it all into into a crate, it'll look a bit like this:

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/_20170104_183125.JPG "Crated up")

### Prepare your environment to set up a new machine 

Though the rig will eventually run headless, we'll need keyboard, video and mouse whilst configuring UEFI and installing an operating system, as well as network and power connections. At this stage, what we're looking to do is to test that what we've done works and that we can reach and configure the UEFI interface.

#### Display

The motherboard has VGA and HDMI outputs; I'm already using the only HDMI-capable display I have, so a 17" 1024x768 screen is
what I'll be using initially. You could use an existing monitor, or even an HTDV.

#### HID, network and power connectiond

Any old keyboard and mouse should do. I'm using a USB mouse and a PS/2 keyboard, as that's what I had. Wired Ethernet is by far the easiest when installing operating systems, so a cable is plugged in from the router.

AC power is supplied to the PSU through a power-monitoring wall plug; an Android tablet is set up to display the power usage. The PSU's Corsair Link connector is connected from a USB header on the motherboard to the PSU, for power monitoring and statistics when in Windows. This done, time to flick the switch and see what goes bang.

### Initial power on

This will usually involve a certain amount of messing around, checking connections, pulling and replugging until you stir the system into life; when you do you'll be greeted with the UEFI interface. Have a look around, make sure that all is as expected. Optionally, but recommended:

- update the UEFI firmware
- set onboard adapter as the primary GPU regardless of the PCIe slots
- set the state after a power outage to ON; you want it to come back to life.

### Install SSD

Or whatever other storage, but cheap SATA SSDs are plentiful. I'm using a 120GB ADATA SP550, more than adequate.

### Insert UEFI OS install medium

Which normally means plug in a UEFI-capable USB stick containing the installer files for your operating system, though you could use a DVD-ROM or any other means. The point is: get it connected so we can set UEFI up accordingly. Information on how to make, or where to buy, a USB install key for a given operating system are available from the publishers.

### Configure UEFI boot devices

You'll want to set the order of boot devices in UEFI, and potentially make any settings relevant to a solid state drive if you have one. When you reboot this time, you should be ready to install an operating system. Or two, if you like.

## Install your operating system(s) 
