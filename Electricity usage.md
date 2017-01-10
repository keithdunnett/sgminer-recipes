# Electricity use and BIOS notes

## Base system power consumption

### Rig hardware

- ASRock H81 Pro BTC
- Intel Celeron G1840
- ADATA 120GB SSD 
- 2GB DDR3

### Rig software

- Ubuntu 16.04.1 LTS
- AMDGPU-PRO video driver v16.40-348864
- AMD APP SDK v3.0
- sgminer-gm v5.5.4

30W baseline consumption (doing nothing), 48W compiling with 3 parallel threads.

## Ethereum with 6 x Sapphire Nitro+ RX470 8GB OC GPUs 

### Stock BIOS: 1260MHz GPU, 2000MHz memclock
- 150W baseline consumption (doing nothing)
- 1136W avg. for ethash at 141Mh/s, 23.5Mh/s per card, 8.05W/Mh/s

#### Notes

- The above is at one thread per GPU. Stock BIOS doesn't mix well with my stock config (2 threads per GPU, intensity 20) which is
what I use to good effect on a similar rig - it pushes power use beyond the point of triggering the PSU's overload protection and power is
unceremoniously cut.
- It's also a strong indicator of the need to modify the BIOS; that's a dismal figure for power efficiency. Below 6W/Mh/s is achievable
from this hardware.

### Mod BIOS #1: 1100MHz GPU, 2150MHz memclock, 1750MHz memory strap, 2550rpm max fan

- 120W baseline consumption (doing nothing, min GPU clock lowered from 300 to 250MHz)
    - 30W for base system
    - 90W for 6 GPUs at idle

#### 1100MHz memclock (default in this BIOS)

- 925W avg. for ethash at 156.8Mh/s, 26Mh/s per card, 5.9W/Mh/s (one thread per GPU)
- 931W avg. for ethash at 157.6Mh/s, 26.1Mh/s per card, 5.91W/Mh/s (two threads per GPU)
    - Highly marginal difference in output (~0.5%) but reproducible for me on other rigs, good work utility.
    
#### 1210MHz memclock

- 962W avg. for ethash at 162Mh/s, 27Mh/s per card, 5.94W/Mh/s (two threads per GPU, 5% overdrive)
    
#### 1265MHz memclock

- 992W avg. for ethash at 165Mh/s, 27.5Mh/s per card, 6.01W/Mh/s (two threads per GPU, 15% overdrive)



#### Stability

Rig stability depends heavily on cooling, certainly when overclocking memory. An aggressive fan control profile, a disproportionately low temperature target and an 
increase over stock fan speeds will reliably be required. On my current settings:

- target GPU temperatures of 60-62 degrees weren't low enough to avoid fairly regular crashes
- target of 50 degrees has the GPU fans in a range 60-100% (of increased max. fan speed 2550rpm)
    - reported GPU temperatures range from 50-58 degrees with most fans staying at 100%

Currently, these settings are not "instant crash" material but leave something to be desired for stability; the new rig typically sees a GPU drop out after a few hours. Similar settings are stable enough on an identical rig, but close enough to the limits that the layout of the rig and the proximity of cards to one another makes a difference; it's anticipated that further tweaking may be needed. 

### Mod BIOS #2: 1100MHz GPU, 2150MHz memclock, 1750MHz memory strap, 2550rpm max fan, 1050mV memory

#### 1265MHz memclock

- 1004W avg. for ethash at 165Mh/s

Still wasn't stable. Looking at the BIOS from a working rig, I see that I increased the TDP/mac current/max power from 85W/116A/130W to 95W/125A/140W, and replicate the same settings here.

### Mod BIOS #3: 1100MHz GPU, 2150MHz memclock, 1750MHz memory strap, 2550rpm max fan, 1050mV memory, powertune 95/125/140

This brings an apparent improvement in stability (early days yet!) with little change in performance or power consumption. For reasons I don't yet understand, power usage is ~3% higher than on an identical rig; this may partly be down to running the fans at full tilt. 

#### 1265MHz memclock

- 1004W avg. for ethash at 165Mh/s



 
 
    
    
