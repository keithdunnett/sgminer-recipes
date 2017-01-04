# Building a GPU mining rig - WIP UNFINISHED

As I'm building an open case GPU rig that will ultimately run sgminer, I thought I'd document it. It's a minimalist setup in an open crate, hardware is fairly typical of that used for Ethereum mining in early 2017.

This first page covers the building of the hardware, up to the point of operating system installation.

## Assemble the hardware

### Assemble the CPU, motherboard and memory

Here we have an ASRock H81 Pro BTC, to which I've fitted 2GB of RAM, an Intel Celeron G1840 CPU and the stock cooler. The switch on the white wire is a simple push button switch that acts as a power switch when connected to the relevant "PWRBTN" pins on the motherboard. 

![Motherboard](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/_20170104_170838.JPG "AsRock H81 Pro BTC")

#### Replace CMOS battery and set 'Clear CMOS' jumper (optional)

If you bought the board second-hand, as I did, it's no bad idea to set the "Clear CMOS" jumper before you ever power it up, or don't be surprised when you can't access UEFI thanks to Fast Boot mode. Usually you need to boot once with this set, then restore it to the default position so as to preserve the settings you make. I also took the opportunity to replace the CMOS backup battery as I had some on hand, although in practice they last for several years.

#### Install SATA cables etc. (optional)

We're about to turn this into a forest of cables, through which it won't get any easier to install things to the motherboard. If you need SATA cables, USB headers for Corsair Link or whatever, it may be easiest to install them at this stage.

### Assemble the PSU and cables

![Ax1200i](https://raw.githubusercontent.com/magick777/sgminer-recipes/master/_20170104_161000.JPG "Corsair AX1200i")


### Prepare your environment to set up a new machine 

Though the rig will eventually run headless, we'll need keyboard, video and mouse whilst configuring UEFI and installing an operating system, as well as network and power connections. 
