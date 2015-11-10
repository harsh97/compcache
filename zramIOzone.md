# Introduction #
IOzone is a filesystem benchmark tool. It was used to generate variety of filesystem (ext4) workload over zram devices.

# Details #

## Common configuration for ram and zram disks case ##

  * Tests were run natively (not in a VM)
  * Host OS: Fedora 11 x86\_64
  * Host configuration: CPU: Intel Core 2 Duo (T9600, 2.8GHz), RAM: 4G
  * Boot into runlevel 3 and turn off crond, atd, cpuspeed

## ram disk case ##

  * Create 3G ram disk using ramdisk\_size boot param
  * mkfs.ext4 /dev/ram0
  * mount /dev/ram0 /mnt/other

## zram disk case ##
  * Create 3G zram disk using zramconfig
  * mkfs.ext4 /dev/zram0
  * mount /dev/zram0 /mnt/other

## IOzone command ##
```
iozone -a -B -n 1G -g 2G -G -R -b iozone.xls -M -+u
```

```
 -a: Automatic mode.
 -B: Use mmap() files.
 -n: Minimum file size.
 -g: Maximum file size.
 -G  Use msync(MS_SYNC) on mmap files
 -R: Generate excel output.
 -b: Excel output filename.
 -M: uname() output in results.
 -+u: Enable CPU stats.
```

In parallel, run:
```
watch -n 1 'echo 1 > /proc/sys/vm/drop_caches'
```
This will drop/free pagecache every 1 second. This forces most of the I/O to actually go to disk.

## Results ##

Actual numbers do not matter since the effect of pagecache make R/W appear faster than reality. For more accurate performance numbers, refer zramperf. This IOzone test was done primarily to stress zram code and not to measure performance.

Each test was run 5 times and no error was observed.

### ramdisk case ###
```
        Machine = Linux vflare 2.6.34 #1 SMP Wed May 19 13:04:50 IST 2010 x86_64 x86_6  CPU utilization Resolution = 0.001 seconds.
        CPU utilization Resolution = 0.001 seconds.
        CPU utilization Excel chart enabled
        Command line used: /home/ngupta/opt/iozone/src/current/iozone -a -B -n 1G -g 2G -G -R -b iozone.xls -M -+u
        Output is in Kbytes/sec
        Time Resolution = 0.000001 seconds.
        Processor cache size set to 1024 Kbytes.
        Processor cache line size set to 32 bytes.
        File stride size set to 17 * record size.
                                                            random  random    bkwd   record   stride
              KB  reclen   write rewrite    read    reread    read   write    read  rewrite     read
         1048576      64  309500  346052   741827   692543  668591  342610  943661   375120   986910
         1048576     128  359506  390935   608138   870010  493423  310893  783716   391109   654727
         1048576     256  389008  371681   868290   870373  641083  356326  986793   389621   825965
         1048576     512  411349  346160   808808   779252  666401  403118 1093451   409388   886225
         1048576    1024  434998  377833   790972   744039  538548  398744 1026594   402078   734151
         1048576    2048  397826  340499   728962   698772  657706  394667  647407   397864   935239
         1048576    4096  400690  354910   653682   588612  473785  376885  781584   395144   602351
         1048576    8192  391729  351347   656444   561333  674144  385264  738427   388412   692058
         1048576   16384  380419  383663   488296   627425  639278  381618  769378   385701   464041
         2097152      64  290187  343444   656998   708405  557515  289988  701520   313568   742025
         2097152     128  341624  347826   752151   743522  419107  233937  725035   366825   548462
         2097152     256  381127  365021   711029   718995  533597  289949  743980   354228   526949
         2097152     512  398443  362675   778044   755627  614676  334848  744319   374755   731869
         2097152    1024  395428  374523   534204   675757  611516  350119  723927   374815   591226
         2097152    2048  395592  375151   631266   652976  628337  374699  671840   371318   605499
         2097152    4096  379787  357786   486780   599601  595327  328224  624011   353998   527587
         2097152    8192  378041  359126   567922   524867  476484  356595  607510   367276   576345
         2097152   16384  370599  361646   608814   598262  590169  367291  531728   363478   563297

iozone test complete.
```

### zramdisk case ###
```
        Machine = Linux vflare 2.6.34 #1 SMP Wed May 19 13:04:50 IST 2010 x86_64 x86_6  CPU utilization Resolution = 0.001 seconds.
        CPU utilization Resolution = 0.001 seconds.
        CPU utilization Excel chart enabled
        Command line used: /home/ngupta/opt/iozone/src/current/iozone -a -B -n 1G -g 2G -G -R -b iozone.xls -M -+u
        Output is in Kbytes/sec
        Time Resolution = 0.000001 seconds.
        Processor cache size set to 1024 Kbytes.
        Processor cache line size set to 32 bytes.
        File stride size set to 17 * record size.
                                                            random  random    bkwd   record   stride
              KB  reclen   write rewrite    read    reread    read   write    read  rewrite     read
         1048576      64  226178  255617   479036   460596 2244167  193102  905992   230000  1111346
         1048576     128  274232  230013   578938  2271981  269776  170376 2232842   221372  2251926
         1048576     256  311753  251856  2285788  1180270 2244234  183105 2256977   235259  1475296
         1048576     512  330645  236067   536017   962596  758183  248576 2255977   258073  2236008
         1048576    1024  344364  237175   454852  2255695 1577881  249907 2240691   232577  2255987
         1048576    2048  348098  233300  2145572  1237024 2123702  224368 2123672   242684  1066512
         1048576    4096  348580  232020  1390494   432517 1394587  225765 1156772   223862   859950
         1048576    8192  348501  230655  1247663   374442  575120  247239  804708   244736   659031
         1048576   16384  339752  226126   807560   528928  630223  265362  453253   228401   971366
         2097152      64  230949  219985  2235560  2304147 2266475  171366 1333621   208522  1285780
         2097152     128  280088  220032   934568   745437 1920407  170593  810684   215980   370272
         2097152     256  311173  218876   587314   433425  881887  186110 1748379   226138   409723
         2097152     512  329355  223234   564243  2342699  364212  214951  936711   237650  1283409
         2097152    1024  339664  242885  1102029  2139366 1850092  234779  810483   230460  1114067
         2097152    2048  342278  236132   498602   617058  500605  226954 1060779   228319   850868
         2097152    4096  344085  223445   417500   550464  386282  221147  440016   233749   410492
         2097152    8192  345870  228366   543570   395759  434723  237362  527818   235590   449568
         2097152   16384  341133  239329   484319   491404  520720  239360  559416   235280   434267

iozone test complete.
```

**No I/O errors were observed during/after the test**

Output of zram stats after the test:
```
zramconfig /dev/zram0 --stats
```

```
DiskSize:        3145728 kB
NumReads:        4990928
NumWrites:      59131245
FailedReads:           0  <----\
FailedWrites:          0  <-----\
InvalidIO:             0  <----- No errors
NotifyFree:            0
ZeroPages:         12341
GoodCompress:         99 %
NoCompress:            0 %
PagesStored:      710862
PagesUsed:          7281
OrigDataSize:    2843448 kB
ComprDataSize:     25377 kB
MemUsedTotal:      29124 kB
```