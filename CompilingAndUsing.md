# Compiling and using ramzswap #


---


### This page is for compcache-0.5.x. For version 0.6.x refer CompilingAndUsingNew ###

If you using kernel 2.6.28 or newer, you should use compcache-0.6.x instead.

  * Download source from [here](http://code.google.com/p/compcache/downloads/list).
  * **Optional (HIGHLY RECOMMENDED)**:
    * Apply patch found in the source directory (patch\_swap\_notifier\_generic\_2.6.xx.diff) and just compile the kernel as usual -- currently, patch is against 2.6.25 but it should apply to slightly older/newer kernels too. This will enable 'swap free notify' feature. This allows kernel to send callback to ramzswap as soon as a swap slot becomes free. So, we can immediately free memory allocated for this page, eliminating any stale data in (compressed) memory. _This patch is available only for compcache-0.5.4 or higher_.
    * **Uncomment '#define CONFIG\_SWAP\_NOTIFIERS'** in compat.h before compiling compcache against this patched kernel. Otherwise, this swap notify callback will not be used.
  * Compile:
    * run 'make' in directory where you checked-out or untar'ed sources.
  * Usage:
    * Loading: run 'use\_ramzswap.sh `[disksize(KB)|memlimit(KB)] [backing swap device]`' to load all required modules and setup swap device.
    * **NOTE:** This script contains detailed documentation on all parameters.

> If backing swap device is not given then first parameter is taken as ramzswap disk size. Otherwise, its taken as ramzswap memory limit (see use\_ramzswap.sh script for explanation of these parameters).

### NOTE ###
  * You must swapoff backing swap device before use\_ramzswap.sh. This device is managed by ramzswap module itself.
  * backing\_swap must be a valid swap partition i.e. you must have done mkswap on that partition.
  * backing\_swap must be a block device. Swap files cannot be used as a backing swap.
  * ramzswap memlimit cannot be greater than backing swap disk size. System will only see /dev/ramzswap0 swap device of size equal to backing swap disk size. When memlimit is exhausted, further swap writes are forwarded to backing swap.

### Examples ###

```
swapoff /dev/sda2
use_ramzswap.sh 10240 /dev/sda2
```

sets ramzswap limit as 10MB and /dev/sda2 as backing swap device. **NOTE:** backing device (/dev/sda2 in this example) **must** be a valid swap partition. Also, you must do swapoff /dev/sda2 as its now managed by ramzswap itself.

```
use_ramzswap.sh 10240
```

sets ramzswap disk size as 10MB.

```
swapoff /dev/sda2
use_ramzswap.sh /dev/sda2
```

/dev/sda2 is used as backing swap device and memory limit is set to default (15% of RAM size).

```
use_ramzswap.sh
```

Same as (2) but here, ramzswap disk size is set to default (25% of RAM size). No backing swap device used.

  * Unloading: run 'unuse\_ramzswap.sh' to unload all modules and turn-off ramzswap swap device.


---


If you get any problem using (un)use\_ramzswap scripts, you can do these steps manually as:
  * Load kernel modules:
    * 4 kernel modules are created on compiling. You need to load all three of them (ramzswap.ko at last)
      * modprobe lzo\_compress
      * modprobe lzo\_decompress
      * insmod xvmalloc.ko
      * insmod ramzswap.ko `[disksize_kb=<size>)|memlimit_kb=<limit>] [backing_swap=<backing_swap_path>]`
        * Examples:

```
insmod ramzswap.ko memlimit_kb=10240 backing_swap=/dev/sda2
```

sets ramzswap limit as 10MB and /dev/sda2 as backing swap device. **NOTE:** /dev/sda2 **must** be a valid swap partition.

```
insmod ramzswap.ko disksize_kb=10240
```

sets ramzswap disk size as 10MB.

```
insmod ramzswap.ko backing_swap=/dev/sda2
```

/dev/sda2 is used as backing swap device and memory limit is set to default (15% of RAM size).

```
insmod ramzswap.ko
```

same as (2) - ramzswap disk size will be set to default (25% of RAM size)

  * device node /dev/ramzswap0 should be automatically created after loading ramzswap module. If not, then manually create it as:
    * mknod /dev/ramzswap0 b 253 0 (replace 253 with whatever major no. is assigned to this device: cat /proc/device | grep ramzswap)
  * Add ramzswap as swap device
    * swapon /dev/ramzswap0 -p 1 (give this swap device the highest priority).
  * Verify that ramzswap is active using: cat /proc/swaps. Its should show entry of /dev/ramzswap0.

Now, when system starts swapping, ramzswap will compress and store these pages in RAM itself! :)