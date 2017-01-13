# Mining rig resilience

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

### 3.2 GPU hangs and crashes

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





### 3.3 System hangs and crashes
#### 3.3.1 Crashed processes
#### 3.3.2 Zombie processes
#### 3.3.3 System lock-ups
#### 3.3.4 Excess fault logging

## 4. Logging, monitoring and responding

## 5. Preventative measures









