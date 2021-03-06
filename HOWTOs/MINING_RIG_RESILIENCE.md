# Improving the resilience of your GPU mining rigs

The resilience of a mining rig is much improved if it properly handles GPU crashes, hung tasks, network or pool outages and
any other failure conditions that cause it not to do as the user ordered. This is a judicious mixture of prevention and cure; 
software solutions will not stop you from running into hardware problems. This page looks at some common failure cases (for sgminer under Linux) and some options for automated responses or workarounds, as 
well as any other factors that may become relevant to the resilience of a small scale mining operation.

## 1. Reboot proof your rigs

By "reboot proof" we mean that the rig successfully goes from power on to mining, with the proper configuration if multiple 
configs are in use. There are various stages at which this can fail:

- power must be supplied to the rig, which must be switched on
- UEFI needs to boot the correct disk and operating system
- GRUB needs to load the correct kernel and initrd
- DKMS needs to have built and installed the proper kernel module (at least for amdgpu-pro)
- OpenCL needs to reach the intended GPUs on the intended OpenCL platform
- sgminer needs to start up and to find or build the proper kernel
- pool names must be resolved and stratum connections established
- GPUs and OpenCL kernels must initialise correctly
- for ethash, DAG generation must initialise correctly
- valid shares must be found, transmitted to and accepted by our pools

and broadly two types of failure condition, for the purpose of prevention and reaction:

- reliable failures 
- intermittent faults

### 1.1 System configuration

- Ensure that UEFI is configured to switch on following a power cut
- Test that GRUB boots Linux without user intervention

### 1.2 Run sgminer on system startup

Telling the system to run sgminer on startup is as simple as putting a command into /etc/rc.local, but as that command will be run by the system and without a terminal we must do one of:

- launch the sgminer process in the background
   - with output to file, pipe or syslog
   - runtime control to be done using the API
- launch the sgminer process using a backgrounded instance of screen
   - with ability to reattach to the curses interface (or run in text mode if desired)
   - runtime control to be done using the API

Generally this works well, provided DNS resolution and GPU initialisation work first time. Where they don't, sgminer doesn't
handle this too gracefully and we may need to provide workarounds.

## 2. Handle failure conditions during sgminer startup

### 2.1 DNS resolution failures



### 2.2 GPU initialisation failures


### 2.3 Detecting and responding to intermittent faults


## 3. Handle failure conditions during mining

### 3.1 Network outages

#### 3.1.1 Pool outages

sgminer inherently handles pool failures very well; rather than a simple failover/failback approach, the use of quota-based load balancing can divide hashrate among two or three pools by default. An outage on any one pool simply divides hashrate amongst those remaining, providing a "hot spare" effect if something goes down. The utility of this will depend on the payment strategies of the pools you use and the length of any outage, but sgminer is good at keeping your hashrate connected to something.

Additionally, one can have quota-based load balancing *and* failover by declaring a couple of pools as enabled with a quota of zero. These will be used only if pools with a positive quota are unavailable. Thus, if I am based in Europe, I can specify two or three European servers among which to divide hashrate by preference, but list a couple of US servers so that if European routing fails and a US server is reachable, I'll use it. 

#### 3.1.2 ISP outages

They happen, not much that can usefully be done about it. Failover connectivity with dual WAN connections is a nice idea, but unlikely to justify the cost of two connections for the typical home mining setup. What we can usefully do is to test that an apparent failure of the Internet connection is in fact on the ISP's side, out of our direct control or influence, and not a local problem as described below.

#### 3.1.3 Router outages

### 3.2 GPU faults, hangs and crashes

#### 3.2.1 Event handling in sgminer

sgminer does a good job of handling a non-responsive GPU through its events framework, which can detect when a GPU ceases to work properly and trigger some action to resolve it, typically a GPU reset or a reboot. Generally this is quite successful, with some caveats:

- with the amdgpu-pro driver, rebooting is the only option as the GPU reset function is not stable in kernel 4.4.x
- problems on shutdown (or startup) can mean the risk of going from 5/6 GPUs working to 0/6 GPUs working
    - trying to interact with a hung GPU will typically hang the system, preventing soft reboot
- forced restarts may be unwelcome or intolerable on systems not dedicated to mining
- restarting automatically could simply mask a problem that should be fixed or prevented

##### 3.2.1.1 GPU 'events' in sgminer

The events framework defines two event hooks, 'gpu_sick' and 'gpu_dead', which are called two minutes and ten minutes respectively after a GPU becomes unresponsive. As with amdgpu-pro we don't yet have the means to reset a GPU without rebooting we don't have much use for the latter, so we'll trigger a reboot in response to a GPU being declared sick. In their simplest form, the command line switches to achieve this are

```
--event-on gpu_sick --event-reboot-delay 10
```

but see also `--event-runcmd` if greater flexibility is required, which may be used to call an arbitrary command or script in response to the same event. That can be used simply to provide a more elaborate incantation of the `shutdown` command, or an arbitrary sequence of actions that might include fault logging, analysis of logs to determine repetition and actions such as reconfiguring sgminer to reduce intensity, drop a failing GPU, or even reboot into a secondary operating system image via grub-reboot. Such things are left as an exercise for the reader.

##### 3.2.1.2 Avoid trying to restart a sick GPU

Default behaviour when the 'gpu_sick' event is enabled is that sgminer tries to restart the GPU, a fine idea where the kernel and driver support doing so. For amdgpu-pro on Ubuntu 16.04.1 this is not the case, and in fact, attempting to restart an unresponsive GPU can often lock up the kernel, preventing a soft reboot from happening. The somewhat ambiguously named `--no-restart` flag will disable this attempt to restart the GPU, actively facilitating the restart of the system, and is a recommended addition when using the events framework.





### 3.3 System faults, hangs and crashes
#### 3.3.1 Crashed processes
#### 3.3.2 Zombie processes
#### 3.3.3 System lock-ups
#### 3.3.4 Excess GPU fault logging

Under certain conditions (typically of overloading the virtual memory, but not too well defined) the amdgpu-pro driver throws vast quantities of GPU faults (VM_CONTEXT1_PROTECTION_FAULT), with a range of potential adverse outcomes for mining rig operation. These errors are produced by the GPU driver (reporting errors produced by the hardware) and can be triggered to varying extents by a wide range of mining software, including (but not limited to) sgminer. This can cause a range of different impacts, depending upon the frequency and the extent to which it occurs.

##### 3.3.4.1 System performance issues

The issue is that the amdgpu driver logs three to four lines of fault address and status information for each and every one of multiple sequential memory accesses, generating tens or sometimes hundreds of lines of logging per second when the fault occurs. Further, the default rsyslog configuration in Ubuntu logs these errors to two files, /var/log/kern.log and /var/log/syslog, thus doubling the already substantial amount of log data before writing to disk.

In extreme cases, i.e. when the fault is being triggered constantly, the load imposed by the flurry of logging will make a system almost completely unresponsive, and can reach an extent of filling up the root partition with tens of gigabytes of logs, although this extreme is uncommon and where this happens, the problem becomes highly visible in the course of setting up the rig.

##### 3.3.4.2 Mining performance issues

Short of an extent of locking up the system or filling up the disk, it is possible for the same faults to arise on one particular GPU and to persist in a fashion that severely hurts mining performance and system performance, but which is not detected by sgminer's events framework and can leave a system limping along in a poor state. 

The precise impact will vary by the frequency and extent of the errors, but by way of example, one of my 6 x RX470 rigs started throwing bursts of errors from one of its six GPUs with brief 'pauses for breath' in between. This was not detected as a GPU failure in sgminer because the GPU was still hashing at a few hundred Kh/sec, a couple of percent of its usual hashrate. The impact on performance for that particular rig was to reduce the overall hashrate not just by the losses to faults on that GPU but by consuming sufficient system resources as to hurt the hashrate of the other 5 GPUs by a large margin, hobbling a 165Mh/s rig down to an output of 98Mh/s. 

Disabling the offending GPU via the curses interface as a temporary measure allowed the hashrate from the remaining 5 GPUs to climb back to 138Mh/s; the adverse impact of a GPU limping along in that fashion is greater than that of losing two GPUs altogether.

##### 3.3.4.3 Causes and remedies

There does not seem to be (or the author is unaware of) a unique cause of GPU memory faults or a single solution to the problem. Besides the possibility of a physical fault with the GPU, there appears to be correlation between these faults and, in no particular order:

- the intensity of work attempted by the mining software
- the extent to which the GPU memory is overclocked
   - relative to the ASIC quality of each individual GPU and the voltage at which the memory is running
      - variance is noted among ostensibly identical GPUs running the same BIOS
   - relative to the memory timings specified in the BIOS
   - relative to the overall workload undertaken by the GPU
      - depends on mining software, algorithm, worksize, intensity, GPU clock speed, etc.
   - relative to the amount of cooling provided to the GPU
- the age of the GPU driver; software bugs sometimes get fixed over time (and occasionally new ones introduced)

The potential remedies depend on whether your software, configuration settings and video BIOS are otherwise fit for the intended usage. If so, i.e. you have a particular GPU that does not play nicely with the same settings and video BIOS as is known to work well for other identical GPUs, a lower than average ASIC quality is the prime suspect, to which there are a number of likely responses:

###### Increase cooling (where memory is overclocked)

Always verify whether any GPU memory problem is related to cooling, even where at first glance this seems implausible. When overclocking memory, in particular, be aware that the core GPU temperature as reported by the video driver is no longer a good indicator for the health of the memory or the need for cooling. Setting up a new rig with BIOS settings that worked well for identically specified RX470s on another rig, four cards out of six produced a stable 27.7Mh/s apiece and remain stable at any reasonable fan speed and GPU temperature.

Two cards out of six had their problems with those BIOS settings. In one case, a GPU would start out in line with the others, then slowly lose around 1-1.2Mh/sec of hash rate compared with the rest, reporting the GPU temperature as 48-49 degrees and fan speed (scripted based on GPU temperature) as 70% of a possible 2,550rpm. These figures would not suggest a cooling problem and from this card I did not get an obvious flurry of GPU faults, just occasional dropouts, but forcing the fan speed to 100% (reducing the GPU temperature by all of 3 degrees) got most (not all) of the lost performance back. This was still insufficient at a 2150MHz memory to restore the full hashrate, or entirely to prevent the card from dropping out, suggesting that there would be a benefit to even higher fan speeds if running the GPUs at 2150MHz, or to a lower memory clock if preferring to keep fan speeds and noise moderate.

The other card behaved more variably, sometimes working, sometimes dropping out and sometimes getting itself into a state of throwing tons of GPU errors. Merely increasing the fan speeds (within a 2550rpm limit) did not seem to solve stability issues here and it proved necessary to reduce the memory clock speed (see below). However, the intermittent and run time dependent nature of the faults would support overheating as a possible cause here too; it might be interesting to establish whether further pushing up the fan speeds would (at the expense of some noise) allow that GPU to support running at 2150MHz. 

###### Reduce memory clock speeds

Barring a hardware problem, this is the almost guaranteed solution to GPU faults. The 8GB RX470s ship with a memory clock speed of 2000MHz; I've not come across one that won't handle 2100MHz using the 1750MHz timings, but 2150MHz is pushing it for some of my cards and 2200Mhz is pushing it for most of them, even with fan speeds increased by 15% over stock settings. Cutting back the memory clock from 2150MHz to 2100MHz on the least stable card meant a drop in hashrate of 0.4Mh/s, but it put an end to the GPU faults at a stroke whilst still delivering a performance of 27.3Mh/s from that card, an acceptable tradeoff.

###### Increase memory voltage (with great caution)









## 4. Logging, monitoring and responding

## 5. Preventative measures










