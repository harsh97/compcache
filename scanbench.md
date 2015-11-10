## Introduction ##
Tests on Linux VM with RAM: 512MB, vCPU: 1. We use simple [scan](http://linux-mm.org/PageReplacementTesting) benchmark - it does scans of anonymous mapped memory, both cyclic and use once.

Thinclients/Netbooks (TC/NB) typically have have RAM and CPU configuration as above. However there are some differences:
  * CPU used in these benchmarks is 2.8GHz. TC/NB typically have 1GHz CPU. However, as shown by benchmarks below, current implementation of ramzswap is not every efficient -- even if all swap I/O is serviced through (compressed) ram, CPU utilization hardly goes over 35%. So, faster CPU is not such a big advantage in these benchmarks.
  * Hard disk is 7200 RPM. This is surely not the case with TC/NB - they usually are diskless or have flash storage. 7200 RPM disk surely beats flash storage in random R/W.

So, overall, I expect to get even better results (more speedup with ramzswap) in case on TC/NB. These benchmarks also shows good scope of improvement for ramzswap.

Now over to real benchmark:

## Details ##

## Test Setup ##

Testing done under Linux VM (VirtualBox 2.2.2).

  * **VM details:**
    * RAM: 512MB, swap: 1G
    * vCPU: 1
    * Fedora 10 (kernel: 2.6.29.2-52.fc10.x86\_64)
  * **Host details:**
    * Core 2 Duo: 2.8GHz, RAM: 4G, Disk: 7200 RPM (160G)
    * Win7 RC 64-bit

## Testing details ##

  * Boot into runlevel 3
  * Run 'scan' benchmark available at: http://linux-mm.org/PageReplacementTesting
  * ./scan 480 10 (i.e. allocate 480M and scan over this region 10 times)
  * Run above test 5 times for both disk based swap and ramzswap
  * Take average real time for 5 runs to get speedup with ramzswap

(These tests could not be run on swapless configuration. So no numbers for this case.)

## Tests with disk swap ##
```
Filename				Type		Size	Used	Priority
/dev/mapper/VolGroup00-LogVol01		Partition	1048568	0	-1
```

./scan 480 10

```
real 1m24.260s
user 0m9.019s
sys 0m12.075s

real 3m40.464s
user 0m4.726s
sys 0m36.359s

real 3m28.918s
user 0m4.400s
sys 0m36.537

real 3m12.444s
user 0m4.935s
sys 0m38.034s

real 3m24.138s
user 0m5.268s
sys 0m37.390s
```

**NOTES:**
  * run-1 is significantly faster than any of subsequent runs. To verify this result, first run values were noted several time -- it stayed around ~1m24s consistently. While subusequent runs stayed in ~3m20s range.
  * swap usage stays around 28M-32M during tests and falls down to ~8M after each scan iteration.

## Tests with ramzswap ##

```
# /proc/swaps
Filename				Type		Size	Used	Priority
/dev/ramzswap0                          partition	1048568	0	100

# from /proc/ramzswap
DiskSize:	 1048576 kB
MemLimit:	   75452 kB
```

Backing swap device was set to /dev/mapper/VolGroup00-LogVol01 (same as in above test)

./scan 480 10
```
real 0m43.674s
user 0m14.830s
sys 0m9.478s

real 2m22.095s
user 0m16.832s
sys 0m32.918s

real 2m17.664s
user 0m16.645s
sys 0m32.999s

real 2m19.759s
user 0m17.020s
sys 0m31.122s

real 2m13.606s
user 0m16.365s
sys 0m33.111s
```

**NOTES:**
  * run-1 is significantly faster than any of subsequent runs - behavior similar to the case with disk swap.
  * swap usage stays around 28M-32M during tests and falls down to ~8M after each scan iteration - again similar to case with disk swap.
  * /proc/ramzswap after 5 mem scan iterations:
```
DiskSize:	 1048576 kB
MemLimit:	   75452 kB
NumReads:	 4616690
NumWrites:	 4634882
FailedReads:	       0
FailedWrites:	       0
InvalidIO:	       0
PagesDiscard:	 4463021
ZeroPages:	     218
GoodCompress:	     100 %
NoCompress:	       0 %
PagesStored:	   45291
PagesUsed:	     779
OrigDataSize:	  181164 kB
ComprDataSize:	    2785 kB
MemUsedTotal:	    3116 kB
BDevNumReads:	     225
BDevNumWrites:	      92
```

## Results ##

Avg real time (ignoring 1st runs):
  * disk swap: 206s
  * ramzswap: 137s

**Speedup**: (206-137)/206 = 33%

## NOTES ##

  * In case of ramzswap, CPU usage for VirtualBox process kept varying between 25%-35% which is not expected! Since no. of R/W requests to backing swap device are negligible, almost all pages were kept in compressed memory itself. So 'scan' benchmark should have be completely CPU bound here.

  * Code profiling should help root-cause above. Initial suspicion is on mutex help in ramzswap\_write() path causing too many reschedules here.

  * Compression is really fast and allocation is O(1). So, should change it to use spinlock in common case - fall back to mutex only in cases our memory pool needs to grow (requesting pages from kernel page allocator).