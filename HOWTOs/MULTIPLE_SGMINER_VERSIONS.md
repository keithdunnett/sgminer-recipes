# Configure multiple co-existing versions of sgminer

It is possible to run sgminer from the directory in which it was built without ever installing it system-wide. This does not,
however, make life simple if you want to switch easily between multiple releases and test builds whilst maintaining a central 
configuration. 

The alternative option, which this HOWTO describes, is to configure each version of sgminer to install to a directory of its own, and to have your startup
script run any given sgminer version from within its own directory to generate and load the .bin file specific to that 
version.

## Install sgminer to a version-specific install path

Prerequisite is that your sgminer source code does in fact compile and build on your target machine, i.e. that any dependencies 
are met and that your invocations of `./configure` and `make` duly succeed in compiling the binary. The commands in this HOWTO 
are complete insofar as they concern building and installing sgminer, but do not cater to the prior installation of build 
dependencies or the specifics of how you may wish to configure sgminer for your particular requirements.

Configuring sgminer to use a different install path is as easy as giving the `--prefix` option to `./configure`. For example, the following commands build sgminer from the current 'master' branch in git and install it under 
/opt/sgminer-5.5.4 on my Ubuntu system:

```
git clone https://github.com/genesismining/sgminer-gm.git
cd sgminer-gm
git submodule init
git submodule update
autoreconf -i
CFLAGS="-Os -Wall -march=native -I/opt/AMDAPPSDK-3.0/include" LDFLAGS="-L/opt/amdgpu-pro/lib/x86_64-linux-gnu" ./configure --disable-git-version --disable-adl --prefix=/opt/sgminer-5.5.4
# The Bash-ism below should parallelise your build to (number of CPU cores + 1) threads. You can just use 'make', if need be.
make $(if $(THREADS="-j$(($(tail -c 2 /sys/devices/system/node/node0/cpulist 2>/dev/null)+2))"); then echo $THREADS; fi)
test -x ./sgminer && make install
```
## Building older sgminer versions from git

Let us assume, for sake of example, that we also wish to build sgminer v5.4.0 and install it under /opt/sgminer-5.4.0, in case we
have problems with the current git version. We can do this from within the same git directory by checking out an older version,
redoing autoconf with orders to overwrite the previous configuration files, cleaning up the working directory and building anew. Looking at the tags available
in the repository, we now want a copy of the repository as it was at tag '5.4.0-gm-alpha': 

```
git checkout 5.4.0-gm-alpha
autoreconf -fi
CFLAGS="-Os -Wall -march=native -I/opt/AMDAPPSDK-3.0/include" LDFLAGS="-L/opt/amdgpu-pro/lib/x86_64-linux-gnu" ./configure --disable-git-version --disable-adl --prefix=/opt/sgminer-5.4.0
make clean
# The Bash-ism below should parallelise your build to (number of CPU cores + 1) threads. You can just use 'make', if need be.
make $(if $(THREADS="-j$(($(tail -c 2 /sys/devices/system/node/node0/cpulist 2>/dev/null)+2))"); then echo $THREADS; fi)
test -x ./sgminer && make install
```

## Select your sgminer working directory (and version) at run time

It is assumed that you will use a shell script to make the desired settings and start sgminer; an [example including rudimentary support for multiple sgminer versions](https://github.com/magick777/sgminer-recipes/blob/master/sgminer-start-screen-example) is available in this repository. For those modifying existing startup scripts, the key changes to support multiple parallel installs were:

- provide the startup script with the path to the instance of sgminer it should run
    - initially this is just a variable containing the suffix of the installation directory to use
    - version could trivially be passed to the script at command line
    - version could be determined using arbitrary logic in the startup script
        - possibly with analysis of prior successes or failures
- change directory to the chosen install path before starting sgminer
- modify the invocation of sgminer to call the binary in that directory with `./sgminer` 

### Maintain a standard path with symlinks at runtime (optional)

### Add working directory path to $PATH environment variable (optional)




## Ensure config compatibility with available sgminer versions (WIP)
