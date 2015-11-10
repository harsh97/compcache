  * NOTE
    * It requires kernel version 2.6.28 or higher.
    * Kernel 2.6.33 will have partial support for compcache. So, if you are using this kernel, please do not use in-tree compcache version. Use compcache version as available from this project's [download](http://code.google.com/p/compcache/downloads/list) page. Kernel 2.6.34 will (hopefully!) will have complete support.

# Introduction #

compcache-0.6 brings some new features which require some changes to the way you use it. Some of the new features include:
  * Support for _file_ as backing swap. Earlier versions (0.5.x) only allow using a block device as backing swap.
  * Support for multiple /dev/ramzswapX devices.
  * New _rzscontrol_ utility to manage various ramzswap devices: setting parameters, initializing, resetting, stats collection.
  * Support for _swap free notify_ callback. This will give ramzswap a callback as soon as a swap page becomes free -- stale data, no more!
    * A separate patch will be included that adds hooks in kernel swap path to enable this feature.
    * Rest of ramzswap will remain as separate modules. It will be usable on kernels with or without this patch. Of course, without patch, ramzswap will have this feature disabled.

Its known to work on x86, x86\_64 and ARM.

# Details #

## Compiling ##

  * make: This will compile all modules against your kernel
  * make doc: This will compile rzscontrol manual page: sub-projects/rzscontrol/man/rzscontrol.1

  * Optional **(HIGHLY RECOMMENDED)**:
    * Apply patch found in compcache/patches/ directory and just compile the kernel as usual -- currently, patch is against 2.6.33 but it should apply to slightly older kernels too. This will enable 'swap free notify' feature. This allows kernel to send callback to ramzswap as soon as a swap slot becomes free. So, we can immediately free memory allocated for this page, eliminating any stale data in (compressed) memory.
    * **Uncomment '#define CONFIG\_SWAP\_FREE\_NOTIFY'** in compcache/compat.h before compiling compcache against this patched kernel. Otherwise, this swap notify callback will not be used.

**NOTE**: ramzswap will work on kernels with or without this patch. Of course, this feature will be disabled if compiled against kernel without this patch.

## Using ##

Above compilation gives following modules:
  * ramzswap.ko (virtual block device driver)
  * rzscontrol (userspace utility to setup individual ramzswap devices)

Following shows a typical sequence of steps for using ramzswap.

  * Load Modules:

```
# load dependency modules
modprobe lzo_compress
modprobe lzo_decompress
```

```
# example1: load ramzswap module
insmod ramzswap.ko num_devices=4
```

This creates 4 devices (/dev/ramzswap{0,1,2,3}) which are left uninitialized.

```
# example2: load ramzswap module and initialize the first device
insmod ramzswap.ko num_devices=4 disksize_kb=20480
```

This initializes first device (/dev/ramzswap0) with disksize of 20MB. Other 3 devices (/dev/ramzswap{1,2,3}) are left uninitialized.

```
# example3: load ramzswap module and initialize the first device
insmod ramzswap.ko backing_swap=/dev/sda2 memlimit_kb=20480
```

This initializes the first device with a backing swap and memlimit of 20MB. Here only one device is created (num\_devices=1 by default). If memlimit\_kb was absent, default value of 15% of RAM would have been used.

Initialization of first device directly from insmod parameters is useful for embedded systems where shipping an additional binary (rzscontrol) might not be desirable. However, without rzscontrol, you cannot reset and reconfigure devices without module reload. Viewing ramzswap statistics is also possible only through rzscontrol utility.

  * Initialize:
> Use rzscontrol utility to configure and initialize individual ramzswap devices. Example:
```
rzscontrol /dev/ramzswap0 --init # uses default value of disksize_kb
```
> See **[rzscontrol manpage](http://compcache.googlecode.com/hg/sub-projects/rzscontrol/man/rzscontrol.html)** for more details and examples.

  * Activate:
```
swapon /dev/ramzswap2 # or any other initialized ramzswap device
```

  * Stats:
```
rzscontrol /dev/ramzswap2 --stats
```

  * Deactivate:
```
swapoff /dev/ramzswap2
```

  * Reset:
```
rzscontrol /dev/ramzswap2 --reset
```

  * Unload Modules:
```
rmmod ramzswap
```


**NOTE: You must issue reset after swapoff for any ramzswap device. The reset causes all the (per-device) memory to be freed and performs lots of cleanups.**

(ramzswap module unload calls reset for all initialized devices).

Following state diagram shows various ramzswap device states. The rzscontrol utility sends various ioctls to the ramzswap module to get/set information about individual devices. In the following figure, ioctl(--blah) means action taken when 'rzscontrol /dev/ramzswapX --blah' is done for /dev/ramzswapX device.

![http://wiki.compcache.googlecode.com/hg/files/ramzswap_states.png](http://wiki.compcache.googlecode.com/hg/files/ramzswap_states.png)

**Figure 1**: ramzswap device states.

_`*` Changing memlimit for an active device is not yet implemented._

## Common Problems ##
  * If you get lots of compiler errors, make sure you have the package for kernel source installed. For e.g., on Fedora its 'kernel-devel' package.


---


The second system is the most dangerous system a man ever designs.... The general tendency is to over-design the second system, using all the ideas and frills that were cautiously sidetracked on the first one.

F.P.Brooks, Jr.
The Mythical Man-Month (1975)