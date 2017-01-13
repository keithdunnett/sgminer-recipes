# Configure sgminer at startup (without config file)

Details a number of methods for passing configuration settings to sgminer besides the regular config file, which does not 
work well for all situations. Sgminer has a habit for searching out and using any saved configuration file it can lay hands on, whether or not this 
was specified at startup. Couple that with the fact that "experimental" features like quota support aren't saved when
sgminer writes out a configuration file and you have a fine recipe for messing up the configuration. 

### Start sgminer without a config file

At the expense of a brief timeout, you can force sgminer not to read any config files it has lying around by passing the  option `-c /dev/null`. 
It will sit for a few seconds, then launch with just the parameters you provided at startup. This can be useful for 
defining a static set of default configurations in your startup script; you can then control running instances via 
curses interface or the API.

### Start sgminer from /etc/rc.local on startup

Provided your startup script launches sgminer in the background, you can have it startup along with the system by 
listing your startup script in /etc/rc.local, which is run on system startup. 

To do this and yet retain the ability to connect to the curses interface, or run other things alongside it, use `screen`; 
you can find an executive summary of how to use it [here](http://www.darkcoding.net/strategy/gnu-screen-basics-quick-reference/) or via `man screen`.

Am example of a working startup script that can be used in this way is discussed in the next few sections.

### Write out a custom startup script

Why? Putting settings into and taking them out of long command lines isn't much fun. We can put our desired options into
a script, commenting them as needed, then process that into a command line we can feed to a shell interpreter, or into the
final script we run to start the program. Besides making the static configuration easier, it also provides the potential for 
dynamic configuration at startup time, perhaps based on what's profitable (though that comes later).

Here's an example of the script I'm using to start my rigs. The settings themselves may or may not be of use to another setup;
the aim is to show *how* this can be done as opposed to the detail of what I do with it. Don't just copy and paste without 
editing the whole thing; you may not want my overclocking settings for a start.

This allows us to have a list of pools effectively as long as you like; the maximum length of a single command line is a 
couple of MB on modern *nix systems, more than enough. This example defines a wallet and a handful of servers for each of 
Ethereum and Ethereum Classic, leaving the latter disabled until I require them. It then specifies a load-balance strategy 
and defines the weight to give each pool (I mine one currency at a time, this is just to split hashrate out across a couple 
of pools.). 

#### Example script to generate and run batch file to launch sgminer

```
#!/bin/bash

set -e

WALLET="0xc91B3f6a889a9963c7eaE2d5BD5Ca88a6Ae2d2EA";
WORKER="`hostname`";
POOL="stratum+tcp://eu1.ethermine.org:4444";
POOL2="stratum+tcp://pool.alpereum.ch:3004";
POOL3="stratum+tcp://eth-eu2.nanopool.org:9999";
POOL4="stratum+tcp://eth-eu1.nanopool.org:9999";
POOL5="stratum+tcp://us1.ethermine.org:4444";

ETCWALLET="0x2699d8531BDE9606F257387fdf0EC7d498F6197c";
ETCWORKER="`hostname`";
ETCPOOL="stratum+tcp://etc-eu1.nanopool.org:19999";
ETCPOOL2="stratum+tcp://etc-eu2.nanopool.org:19999";
ETCPOOL3="stratum+tcp://eu1-etc.ethermine.org:4444";
ETCPOOL4="stratum+tcp://eu1-etc.ethermine.org:4444";

date "+%s" > /dev/shm/miner.start

#Write out a startup script to /dev/shm/sgminer.tmp
#Reinitialise temp and startup batch files with the shebang

echo "#!/bin/bash" > /dev/shm/sgminer.tmp

#Write the desired arguments out into a temp file

cat << EOF | while read line; do echo "$line " >> /dev/shm/sgminer.tmp ; done; echo "" >> /dev/shm/sgminer.tmp
/usr/bin/nice -n -10 /usr/local/bin/sgminer 
# sgminer seems determined to write config files and reuse them, this should tell it otherwise
--default-config=/dev/null
--event-on gpu_sick
--event-reboot-delay 30
#--verbose 
#--debug 
#--protocol-dump 
#--text-only 
#--per-device-stats
--log 5
--tcp-keepalive 30 
--queue 0
--gpu-threads 2 
--intensity 20 
--worksize 256 
--scan-time 7 
--expiry 28 
--api-listen
--api-allow W:192.168.1.0/24,W:127.0.0.1/32
--api-description `hostname`
--api-network
--api-port 4028
--api-mcast
--api-mcast-addr 224.0.0.116
--api-mcast-code sgminer
--api-mcast-des "`hostname`"
--api-mcast-port 4028
# quotas are arbitary and are used in proportion to the sum of all active quotas
# thus you can make quotas add up to 100 if you want them to be a percentage of your rig total
# or start them all at 100 if you want quotas as percentage of fair share
# the approach used by the author below is based on total quota of 100 per currency 
--load-balance 
--disable-rejecting
-U "40;$POOL2" -O $WALLET.$WORKER:x -p x -k ethash --no-extranonce --state enabled
-U "30;$POOL" -O $WALLET.$WORKER:x -p x -k ethash --no-extranonce --state enabled
-U "30;$POOL3" -O $WALLET.$WORKER:x -p x -k ethash --no-extranonce --state enabled
-U "0;$POOL4" -O $WALLET.$WORKER:x -p x -k ethash --no-extranonce --state enabled
-U "0;$POOL5" -O $WALLET.$WORKER:x -p x -k ethash --no-extranonce --state enabled
-U "50;$ETCPOOL3" -O $ETCWALLET.$ETCWORKER:x -p x -k ethash --no-extranonce --state disabled
-U "50;$ETCPOOL2" -O $ETCWALLET.$ETCWORKER:x -p x -k ethash --no-extranonce --state disabled
-U "0;$ETCPOOL" -O $ETCWALLET.$ETCWORKER:x -p x -k ethash --no-extranonce  --state disabled
-U "0;$ETCPOOL4" -O $ETCWALLET.$ETCWORKER:x -p x -k ethash --no-extranonce --state disabled
2>/dev/shm/sgminer.log
EOF

# Now build our actual startup script
echo "#!/bin/bash" > /dev/shm/sgminer


# Insert the desired startup incantations to make settings to your GPUs

cat << 'EOF' | while read line; do echo "$line" >> /dev/shm/sgminer ; done;
export GPU_FORCE_64BIT_PTR=1
export GPU_USE_SYNC_OBJECTS=1
export GPU_MAX_ALLOC_PERCENT=100
export GPU_SINGLE_ALLOC_PERCENT=100
export GPU_MAX_HEAP_SIZE=100
ls -1 /sys/class/drm/card?/device/pp_sclk_od | while read gpu; do echo 15 > $gpu; done
ls -1 /sys/class/drm/card?/device/hwmon/hwmon?/pwm1 | while read gpu; do echo 100 > $gpu; done
EOF

# Now cat that batch file to another removing lines commented out which would break sgminer startup.
# This allows us to use a line-by-line approach above yet feed the parameters listed above and
# minus comments into our single-line invocation of sgminer.

cat /dev/shm/sgminer.tmp | grep -v "^#" | grep -v "^$" | while read line; do echo -n "$line " >> /dev/shm/sgminer ; done

# Make it executable and run it

chmod 700 /dev/shm/sgminer

echo "Attempting to start sgminer with the following startup script: "
echo ""
cat /dev/shm/sgminer
echo ""
echo ""
echo "Be patient. This will take a few seconds."

# Little hack to avoid hangups when DNS resolution isn't working at startup

sleep 1; test=`ping -c1 www.google.com || /bin/true`;

echo 60 > /tmp/gputemp.target
`which screen` -AdmS sgminer -t gputemp `which watch` /usr/local/bin/gputemp
`which screen` -AS sgminer -X screen -t sgminer /bin/bash /dev/shm/sgminer 
```

Running that script (from console or from rc.local) will bring up sgminer in a screen session in the background; you can 
connect to it with `screen -dr` for the curses interface, or you can use the software of your choice to talk to the sgminer 
API. Later, we'll look at using that with data from pools, exchanges and profitability calculators. But, first...

## Improving rig uptime and resilience (at OS level)

This section does not cover how to get the best performance and stability out of your video cards, discussed at length in the Ethereum forums and 
no doubt those of other currencies likewise. It looks mainly at responding to failures, although monitoring, analysis and prevention
will be considered as opportunity allows. I'll divide this section in two, firstly for dedicated mining rigs and then for
desktop computers that also run a mining rig, where user requirements will tend to be different.

### Dedicated mining rigs

#### GPU brownouts

When a GPU drops out, sgminer can detect this and reboot the machine; this works well when triggered by event 
"gpu_sick" after two minutes. Support for soft resetting the GPUs is being worked into kernel 4.10, but for the time being 
it needs a reboot. Sgminer can oblige, when you're happy that everything restarts. Note that the commands listed in the 
script above tell the machine to reboot abruptly and without warning, 30 seconds after detecting a GPU dropout. Desktop 
users may prefer to look at using `--event-runcmd` along with `shutdown -r 60` so it doesn't come out of the blue.

#### Hung tasks

This happens some time after a card has dropped out or on trying to interact with a card that has dropped; the task blocks at 
kernel level whilst waiting for a response from the driver and becomes a zombie process. This isn't often observed with 
sgminer, partly because it hangs one GPU as opposed to the whole program and partly because rebooting as above is an effective
way out. It gets a mention here because attempts to interact with a hung GPU tend to freeze the whole system and can sometimes lock up
a soft reboot. It is possible (if again something for a mining rig not a desktop) to have the kernel panic and force
a reboot, for which [instructions are here](https://forum.z.cash/t/miner-zogminer-linux-silentarmy/2152/114). Stability should
be better than to need it much, if ever.

Crashed or killed tasks are also rare when all is working normally, but certainly possible if either the process segfaults or the box runs out 
of memory. I haven't found the need, but on a remote system it would make sense to implement a check from cron that the 
process is running as expected and to restart it if not. This could also be checked remotely using the API when it works and 
SSH when it doesn't.

### Desktop computers

Desktop computers have a lot of the same problems, often made worse by the coexistence of Xorg on a GPU used for mining, but random reboots and kernel panics
will not be in order on a working computer. If a GPU drops out, sgminer will often manage to continue mining with the others 
until the user fixes it, so it's usually more helpful that the user is made aware of a problem and left to act on it manually 
than for the machine to sort it out autonomously. 

#### Monitoring in the GNOME or MATE desktop panel

### Auto-config: a (very) simple profit-switching approach

During the past week, the returns from ETC have been ~10% higher than from ETH. Currently I switch between them manually 
when it's obviously indicated; within some limits I'd like to make this happen whether I'm paying attention or not. 

#### Pools and the cost of switching

The cost of switching between pools and currencies is variable; a pool with a 2h PPLNS scheme such as Ethermine takes two hours before it pays
in proportion to the hashrate used, effectively losing an hour's mining. If I did that once daily, it would mean hidden fees of 
~4.16% on top of the 1% pool fee; intended to deter switching of this sort. Their competitors offer a range of payout schemes with 
Nanopool doing 15-min PPLNS, Dwarfpool doing HBPPS and so on. For the sake of argument, assume a PPLNS window of 15 minutes and each
such change wastes half that in costs, or ~0.5% of a day's output. Thus, we don't want to be switching back and forth over infinitesimally small gains and losses, or too frequently. If we're mining ETH
and ETC is worth ~4% more on the day, we need to stay put for 3 hours to break even or 6-12 hours+ to show a profit. 

#### Tolerances and triviality

To decide when to make scripted decisions and when not to, we need a "don't care" threshold.
