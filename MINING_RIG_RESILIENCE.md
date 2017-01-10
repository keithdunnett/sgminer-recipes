# Mining rig resilience

The resilience of a mining rig is much improved if it properly handles GPU crashes, hung tasks, network or pool outages and
any other failure conditions that cause it not to do as the user ordered. This is a judicious mixture of prevention and cure; 
software solutions will not stop you from running into hardware problems. This page looks at some common failure cases (for sgminer under Linux) and options for automated responses or workarounds, as 
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

and broadly two types of failure condition for the purpose of prevention and reaction:

- reliable failures 
- intermittent faults

### 1.1 System configuration

### 1.2 Run sgminer on system startup

## 2. Handle failure conditions during sgminer startup

### 2.1 DNS resolution failures

### 2.2 GPU initialisation failures

## 3. Handle failure conditions during mining

### 3.1 Network outages

### 3.2 GPU outages

### 3.3 Zombie processes

### 3.4 Crashed processes

## 4. Logging and monitoring

## 5. Preventative measures










