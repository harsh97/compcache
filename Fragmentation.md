# Introduction #

There is an optional patch to add a SWAP\_NOTIFY callback to notify compcache when swap is nolonger in use. This allows compcache to free() the memory. However the tlsf/xvmalloc may not be able to return the memory back to the OS because the freed memory may be spread across many pages leaving most of the pages still partially in use. As a result, although the ComprDataSize shrinks as swap is freed, the MemUsedTotal stays relatively high.

In normal use I noticed that compcache was approaching a memory fragmentation rate of %50. After doing an interrupted swap off the ratio became even worse. Although compcache managed to free some memory, memory fragmentation rose to 63%. This suggests that defragmenting the memory allocated by comp-cache may be worthwhile in some circumstances.

Note that the compcache was still using less memory to store the compressed pages than would be required to store the uncompressed pages.

# Details #

After closing some windows, my swap usage dropped from 1GB to 411MB, but MemUsedTotal remained high.
```
$ rzstats
BackingSwap:	/old/swap_4.dd
DiskSize:	 4194304 kB
MemLimit:	 1000000 kB
NumReads:	 1408562
NumWrites:	 1047863
FailedReads:	       0
FailedWrites:	       0
InvalidIO:	       0
NotifyFree:	  814586
PagesDiscard:	       0
ZeroPages:	   14374
GoodCompress:	     100 %
NoCompress:	       0 %
PagesStored:	  102777
PagesUsed:	   41309
OrigDataSize:	  411108 kB
ComprDataSize:	   91324 kB
MemUsedTotal:	  165236 kB
BDevNumReads:	  171419
BDevNumWrites:	  113780
```
```
$ cat /proc/meminfo 
MemTotal:        3926280 kB
MemFree:          746780 kB
Buffers:           59964 kB
Cached:           839612 kB
SwapCached:        67460 kB
Active:           961052 kB
Inactive:         724740 kB
Active(anon):     564264 kB
Inactive(anon):   226532 kB
Active(file):     396788 kB
Inactive(file):   498208 kB
Unevictable:          16 kB
Mlocked:              16 kB
SwapTotal:       6756624 kB
SwapFree:        6294740 kB
Dirty:               184 kB
Writeback:             0 kB
AnonPages:        764088 kB
Mapped:           632852 kB
Slab:             121576 kB
SReclaimable:      90672 kB
SUnreclaim:        30904 kB
PageTables:        34272 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     8719764 kB
Committed_AS:    2589344 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       80404 kB
VmallocChunk:   34359654343 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:     3806144 kB
DirectMap2M:      387072 kB
```

After doing a swapoff, and interruping it, almost half the memory was freed, but memory fragmentation ratio rose to 63%
```
$ rzstats
BackingSwap:	/old/swap_4.dd
DiskSize:	 4194304 kB
MemLimit:	 1000000 kB
NumReads:	 1466336
NumWrites:	 1047863
FailedReads:	       0
FailedWrites:	       0
InvalidIO:	       0
NotifyFree:	  875438
PagesDiscard:	       0
ZeroPages:	   14374
GoodCompress:	     100 %
NoCompress:	       0 %
PagesStored:	   41925
PagesUsed:	   22792
OrigDataSize:	  167700 kB
ComprDataSize:	   33310 kB
MemUsedTotal:	   91168 kB
BDevNumReads:	  175369
BDevNumWrites:	  113780
```
```
xp@xp-desktop:/mnt/big/xp/images/share/Ubuntu904/keytest$ cat /proc/meminfo 
MemTotal:        3926280 kB
MemFree:          443012 kB
Buffers:           62972 kB
Cached:           872620 kB
SwapCached:        48956 kB
Active:          1055912 kB
Inactive:         964876 kB
Active(anon):     652512 kB
Inactive(anon):   443684 kB
Active(file):     403400 kB
Inactive(file):   521192 kB
Unevictable:          16 kB
Mlocked:              16 kB
SwapTotal:       6756624 kB
SwapFree:        6560424 kB
Dirty:               100 kB
Writeback:             0 kB
AnonPages:       1071652 kB
Mapped:           733832 kB
Slab:             122624 kB
SReclaimable:      91676 kB
SUnreclaim:        30948 kB
PageTables:        35280 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     8719764 kB
Committed_AS:    2684328 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       80404 kB
VmallocChunk:   34359654343 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:     3806144 kB
DirectMap2M:      387072 kB
```