# sgminer-recipes

##Documents, configuration and helper scripts for sgminer v5.4.0 (and newer) under Linux

Maintainer:	Keith Dunnett <'keith' at 'dunnett' dot 'name'>

Welcome!

This is a collection of scripts and utilities intended to ease the use of sgminer under Linux, pitched
at small and medium scale home miners who don't want substantial infrastructure to manage a few mining
rigs, as well as those still debating whether to run mining operations under Linux.

It's not a polished repository of code, rather some scripts, notes, tips, tricks and quasi-tutorials 
that seek to work with what we've got. There is no Makefile and no install script; to install a 
scripts into /usr/local/bin so you can run it like any other program, just copy it there (assuming you
checked the files out from git, which preserves permissions). If you downloaded a script with curl or
wget, you can make it executable with 'chmod 755 /path/to/file'.

You are advised to READ the scripts and MODIFY them to your purposes; the GPU temperature management
uses what I consider sane settings for my Sapphire Nitro RX4*0 cards but that doesn't mean they'll be
precisely what you need. Likewise, the startup script is an example of *my* configuration and startup 
script and you should investigate all of it, not just pools and payment addresses. One of these days
I'll write a program to ask you everything you didn't know you could and should configure, but until
then there's an example of what I do and the, err, 'famous' manual. 

I have commented out any settings that would obviously be unsafe on another system, such as applying 
overclocking, but the script contains most of the settings I actually use on a working rig (such as
reboot automatically if a GPU becomes unresponsive and open up an API to the local network), which 
may not be what you want in all cases. These are recipes, not prescriptions.  


##gputemp	- monitor temperatures and control fans via the amdgpu sysfs interface 

A helper script to be run alongside sgminer, monitors reported GPU temperatures and modulates the fan
speeds to try and maintain a target temperature, which can be configured on the fly by setting the
value in "/tmp/gputemp.target". Suggested method of running this alongside sgminer is to use 'watch'
to run this script every couple of seconds, and 'screen' to put both in a console-based window manager
for convenience.

This functionality can, should and likely will be integrated into sgminer at some point; this scripted
approach is one I've used with an arbitrary assortment of mining software and, since ADL doesn't work
with amdgpu-pro and its Linux replacement is yet to be seen, this does the job for now. 

Note that when overclocking your GPU memory, the GPU's own temperature (which is of the processor, not
of the memory) isn't a great indicator for the stability or health of the card. Or, more accurately, 
it's a fine indicator provided that you offset your target temperatures to reflect the memory getting 
relatively hotter. This will also interact with GPU speed and power use. 

For example, a rig with six Sapphire RX470 8GB (memory clocks 2150MHz, GPU clocks at 1155MHz) will 
remain stable and fairly quiet with the target GPU temperature at 75 degrees (producing ~26.5MH/s per 
card). The same rig can produce an extra ~1Mh/s per card (for a higher power consumption) with the GPU 
clocks at 1265MHz. In this case, the GPU temperatures stay sane but targeting an otherwise reasonable 
GPU temperature of 70-75 degrees allows the memory to overheat and the cards crash because of it. 
Setting the target temperature to 60 degrees and thereby inviting the fans to spin up to full pelt, 
the latter combination becomes stable enough for an additional 6MH/sec at the expense of some fan noise 
and power consumption.

Thus a helper script for controlling fans by GPU temperature should (and does) come with a warning; if
historically you've set your fans to a static speed that allowed for your memory overclocking, beware
of setting a GPU temperature target that does not. 

Finally, do also note that manufacturers tend to set their fan speeds low to avoid cards being slated 
as 'noisy'; the cards mentioned above have a default speed of 2300rpm as the maximum fan speed (i.e.
the fan RPM that you get at 255/255), but this can and should be increased in the vBIOS and controlled
by software more than hardware. Fans are relatively cheap and disposable; if you're overclocking your
hardware by 10% then don't be scared to overdrive a fan motor by more than that. 


##sgminer-start-screen-example - batch file to start sgminer in a screen session

This is more or less what I use to start my rigs, it contains a lot of my default settings and you will 
need to edit it to your own requirements. This script writes out a startup script for sgminer and then
runs it, meaning that we can specify a bunch of command line arguments one per line and include them or
comment out as desired. 

Note: on my particular systems and network, running this from /etc/rc.local will cause sgminer to start
before the network is ready, and if sgminer fails to resolve any of the available pool servers it will
tend to hang rather than do the obvious and try again. The way around that (for me) was to force a couple
of things in /etc/rc.local prior to calling this script:

/sbin/dhclient || /bin/true;
/bin/sed -i s/127.0.1.1/192.168.1.1/ /etc/resolv.conf

Crude, but the first line ensures we've made an attempt to get DHCP information (the /bin/true is to
avoid stopping script execution if dhclient throws an error), whilst the latter forcibly overwrites
Ubuntu's default DNS recursor (wasn't working well for me) with the one on my router. I've left this
out of the startup script as it's likely specific to my setup, but I document it in case others run
into the same problem.

Subject to a valid sgminer command line, the relevant dependencies and a working network connection,
this will start a screen session running the fan control script above (which it expects to find in
/usr/local/bin/gputemp), and then add our sgminer instance to this instance of screen. The effect is
that the output of 'watch gputemp' can be found in screen tab 0 and sgminer can be found in tab 1.

Access your screen session (at console or via SSH) with 'screen -dr'; switch tabs with Ctrl-A then
the tab number (so Ctrl-A, 0 for gputemp output), detach from screen leaving it in the background 
with Ctrl-A, then d (for detach). For more than that, 'man screen'. You can add anything else you
might care to monitor into additional screen tabs as you please.


## sgminer-api-client - simple bash command line client to use the sgminer API via netcat and jq

Linux has everything you need to access the sgminer API at command line, but the examples provided with
sgminer are in PHP, Ruby and C, whilst most third-party implementations involve Javascript and Node.js.
Nothing wrong with that, but here's a bash variant that merely requires netcat and jq, both available
through the package management of most Linux distributions. As of the initial version, it defaults to
sending API commands in JSON format and does no processing of the raw response other than to pipe it
through 'jq .' which formats the output and adds colour.

###Recursive API commands to enable and disable pools

The bash client adds recursion to one or two commands, such that if you have pools 0-4 configured for
one thing and pools 5-8 configured for another but disabled, as in the above startup script, you can

sgminer-api phoenix 4028 disablepools 0-4
sgminer-api phoenix 4028 enablepools 5-8
sgminer-api phoenix 4028 changestrategy 3

and the script will issue the necessary 'disablepool' and 'enablepool' commands recursively for the
range of values specified. It seems to be necessary to reiterate the change management strategy as
'load balance' afterwards (even where set already) in order to have sgminer act promptly on the new 
configuration. Note that this does not change or add to the API protocol, simply make recursive use
of it for a range of values.

With pools configured as in the startup script example, this can be used to switch an instance of 
sgminer from mining Ethereum on five stratum servers at three pools to mining Ethereum Classic on
four servers at two pools. Or anything else you care to configure, but that's the first thing I 
had in mind for it. 

###Scripted processing of sgminer API data

To extract specific data from the API output requires further JSON and/or text processing; this will 
not be written into this API client (well, mostly not) as it can be done using existing Linux tools. 

A few quick examples:

#### Use jq to filter an individual datum "MHS av" from a JSON array[] called SUMMARY 

sgminer-api phoenix 4028 summary | jq .SUMMARY[].\""MHS av\""
164.929

See 'man jq' for what you can do with it; note the square brackets to access JSON array elements and
the escaped quotes to keep both bash and JSON happy about spaces. For something more involved, such
as GPU data, it may also be helpful to have a json2csv parser available. At the time of writing, this
doesn't appear directly as a package at least on Ubuntu, but a Javascript version can be installed 
through Node.js. On an Ubuntu distribution this should do it:

apt install npm, npm install json2csv, ln -sf /usr/bin/nodejs /usr/bin/node

Some examples of what one might do with it:

##### Use jq to extract response and json2csv to present the data (-n is no header row)

root@precision:~/mining-dev/sgminer-recipes# sgminer-api precision 4028 devs | jq .DEVS | json2csv -n
0,"Y","Alive",0,0,0,0,0,0,0,0,27.5409,27.5417,27541,27542,3759,0,395,2.3049,"20",0,0,4,1480352802,2694996.5578,44318,2754986903327,0,154214451,1480352842,0.8834,0,97854
1,"Y","Alive",0,0,0,0,0,0,0,0,27.5484,27.5551,27548,27555,3826,0,386,2.3459,"20",0,0,2,1480352844,2695730.561,44344,2737107031393,0,5000000000,1480352845,0.863,0,97854
2,"Y","Alive",0,0,0,0,0,0,0,0,27.4653,27.5348,27465,27535,3666,0,452,2.2478,"20",0,0,4,1480352798,2687598.8541,44598,2660688552019,0,154214451,1480352841,1.0033,0,97854

##### As above but use sed to preface the csv output with the rig name and port that we queried. Good for interrogating a larger farm.

root@precision:~/mining-dev/sgminer-recipes# h="precision"; p="4028"; sgminer-api $h $p devs | jq .DEVS | json2csv -n | sed -e "s/^/${h},${p},/g"
precision,4028,0,"Y","Alive",0,0,0,0,0,0,0,0,27.5412,27.5982,27541,27598,3791,0,398,2.3125,"20",0,0,4,1480353332,2708995.0474,44568,2771459122406,0,154214451,1480353350,0.8851,0,98362
precision,4028,1,"Y","Alive",0,0,0,0,0,0,0,0,27.547,27.4982,27547,27498,3850,0,386,2.3485,"20",0,0,4,1480353278,2709565.4728,44590,2749499749315,0,154214451,1480353349,0.8582,0,98362
precision,4028,2,"Y","Alive",0,0,0,0,0,0,0,0,27.4655,27.4764,27466,27476,3683,0,452,2.2466,"20",0,0,4,1480353331,2701550.1578,44860,2671001768784,0,154214451,1480353349,0.9975,0,98362

##### Some fun with awk, just because this wouldn't be complete without.
 
From 'stats' output (which convert to CSV), grep only those lines containing the string "POOL".
Then feed those lines into awk, using a comma as our field separator, and for each record processed. divide 
the content of fields 27 and 28 (send and receive bytes per pool) by field 3 (uptime in seconds) to arrive
at bytes/sec figures for send and receive, adding these to the values of sum and sum2 respectively for the
cumulative bandwidth in each direction. When we reach the end of the stream, if we had 1 or more records 
then print the values of sum and sum2, with labels before, units afterwards and a newline in between
One of these days I must actually *learn* awk and sed...

sgminer-api phoenix 4028 stats | jq .STATS | json2csv | grep "POOL" | awk -F , '{ sum += $27 / $3 } { sum2 += $28 / $3 } END { if (NR > 0) print "send: "sum" bytes/sec\nrecv: " sum2 " bytes/sec"}'
send: 38.3627 bytes/sec
recv: 138.281 bytes/sec

##Ideas, todos, exercises maybe left for the reader and other brain farts

Various build, debug and deployment scripts might yet be cleaned up and shared
Logging, log analysis and management?
Maybe some config (or API command) generators/helpers for pools who publish info to make that easy 
Possibly an improved sgminer code base if or when my C is fit for purpose
Possibly some more enhancements to the bash API client script
Better info on preventing and responding to a wider range of typical failure cases for mining rigs, 
to include crashed and hung processes as well as things that sgminer can handle on its own. Also some
approach to a heartbeat mechanism, i.e. validate that we are working properly and respond to unknown
failures as well as known ones.
Some notes and docs on GPU mining rig setup, possibly.
Similar JSON parsing techniques could usefully be applied to pool APIs, exchange APIs, and data APIs
from sites like whattomine.com. Whether and how far to rely on this for controlling miners is open to
debate and to individual responsibility, of course.
Expand on Linux command line text and JSON processing as needed

##Comments? Suggestions?

Very much welcomed, though my time and attention to computing is variable and my Inbox is in enough of
a mess that I don't want contributions lost in e-mail. Please add any bugs, todos or whatever to the 
issue tracker on GitHub: https://github.com/magick777/sgminer-recipes/issues. If there's anything you
believe I should have written, please have a go at implementing it and submit a pull request if you
succeed, or describe the issue and how you're looking to tackle it if not.

##Goodwill, appreciation, beer money...

Users of these scripts should not feel in any way obliged to tip; they are just examples of how one
might configure and use free, open-source software and they are shared without obligation. Equally 
if you're feeling generous or if you can derive real benefit from anything I've made available, well,
then please feel free to buy me a beer. Encouragement is always welcome. Tip jars:

BTC:	1HNzp4txnTNrUzh6Tp5bC1nYyjMJQtgj9E
DASH:	XbKxET19ngWZtgWHHFfB7ejbz4dcqXRi7v 
ZEC:	t1VrUVLqET4RBL1JYJgVsk1erMYbgDaYTwi
ETH:	0x750743a731e4148aA9070FC2e0D5F422aA44B9C1
ETC:	0x958970b854F35cD2990c3Ca2E213C24d0c3A0ae9

Revision: 28 Nov 2016
