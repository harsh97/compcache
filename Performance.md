# Introduction #

Performance numbers for compcache under various workloads.

# Available data #

## Positive Cases ##
  * compcache on LTSP ([benchmark](http://code.google.com/p/compcache/wiki/Performance/LTSPPerf)) ([summary](http://code.google.com/p/compcache/wiki/Performance/LTSPPerfSummary)): by Nai Xia
  * compcache on under-500MHz machine ([benchmark](http://code.google.com/p/compcache/wiki/Performance/under500MHz)): by John M<sup>c</sup>Cabe-Dansted
  * compcache on Eeepc 701 ([benchmark](http://code.google.com/p/compcache/wiki/Performance/Eeepc701)): by John M<sup>c</sup>Cabe-Dansted
  * Simulating Netbooks/Thinclients ([benchmark](http://code.google.com/p/compcache/wiki/Performance/scanbench)): Tests on Linux VM with RAM: 512MB, vCPU: 1. We use simple [scan](http://linux-mm.org/PageReplacementTesting) benchmark - it does scans of anonymous mapped memory, both cyclic and use once.
  * Comparing swap R/W times for hard-disk and ramzswap ([here](http://code.google.com/p/compcache/wiki/Performance/SwapDiskVsRamz)).

## Negative Cases ##
  * Iozone: Workload with large filesystem cache requirement ([results](http://code.google.com/p/compcache/wiki/Performance/Iozone)).

# Need data #

  * [PlayStation3](http://en.wikipedia.org/wiki/PlayStation_3#Hardware): Some people reported running ramzswap successfully on PS3 (see [Issue #11](https://code.google.com/p/compcache/issues/detail?id=#11)). It has just 256 MB of XDR main memory.
  * Virtualization: KVM Guest VM memory is _anonymous_ w.r.t Linux host. Also, with recent [MMU notifier](http://lwn.net/Articles/266320/) support in Linux kernel, almost all guest memory is swappable! Running compcache at hypervisor layer (Linux kernel here) allows us to compress _any_ part of guest memory and for _any_ kind of Guest OS (Linux, Windows etc.). With compcache on Linux _host_, we expect to be able to run more no. of VMs for given amount of total host memory.
  * Embedded devices: TODO