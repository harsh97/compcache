# Introduction #

Each ramzswap device has its own compression buffer, memory pools and other metadata. All these buffers are protected by a per-device lock which are serious bottlenecks on multi-core machines.

Starting with compcache-0.6, we have support for creating multiple ramzswap devices, primarily to overcome this scalability problem. Divide and Conquer!

# Details #

If you have multiple cores, then using a single ramzswap can be a bottleneck. To avoid this problem, following configuration is recommended:
  * Check no. of cores you have and create equal number of ramzswap devices during module load. For quad core machine, you would do:
```
modprobe ramzswap num_devices=4
```
  * Initialize all of these devices. For example above:
```
# Assuming you need total disksize of 256MB.
# So, per-device size would be 256/4=64MB.
rzscontrol /dev/ramzswap0 -i --disksize_kb=65536
rzscontrol /dev/ramzswap1 -i --disksize_kb=65536
rzscontrol /dev/ramzswap2 -i --disksize_kb=65536
rzscontrol /dev/ramzswap3 -i --disksize_kb=65536
```
(you can add backing swap disk/files to any/all of above devices. See [rzscontrol manpage](http://compcache.googlecode.com/hg/sub-projects/rzscontrol/man/rzscontrol.html) for more information).
  * swapon all these devices with the **SAME PRIORITY**. This priority must be higher than any of the disk based swap devices, if present. For example above:
```
swapon /dev/ramzswap0 -p 10
swapon /dev/ramzswap1 -p 10
swapon /dev/ramzswap2 -p 10
swapon /dev/ramzswap3 -p 10
```

This kind of split setup should give you much better performance on multi-cores than a single giant ramzswap device.