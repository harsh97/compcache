# Introduction #

Test to measure effects of zcache on I/O performance using [IOzone](http://www.iozone.org/) benchmark.

# Details #
  * Tests were done on physical machine (not in a VM)
  * Kernel 2.6.35-rc5 (Fedora 13 x86\_64)
  * CPU: AMD Phenom 9850 Quad-Core Processor
  * RAM: 1G
  * Boot into runlevel 1, disable cpuspeed service
  * Swapoff -a to avoid I/O interference
  * Separate partitions (all ext4) for: /boot, / and /home. So, three separate zcache pools
  * Each pool with default memlimit: 10% of RAM (memory is allocated on-demand and is not pre-allocated)

## IOzone Command ##
```
iozone -a -M -B -s 1G -y 4k -+u -R -b iozone.xls

 -a: Automatic mode
 -M: uname() output in results
 -B: Use mmap() files
 -s: Test file size
 -y: Minimum record size
 -+u: Enable CPU stats
 -R: Generate excel output
 -b: Excel output filename
```

## Results ##

### Without zcache ###
```
                                                            random  random    bkwd   record   stride
              KB  reclen   write rewrite    read    reread    read   write    read  rewrite     read
         1048576       4   81747   35641    73644    73894    2770     805   40026    29575     7645
         1048576       8   83705   32300    73820    74538    5038    1574   40421    28497     8405
         1048576      16   84598   32695    74155    74055    7264    2665   67044    29447    16401
         1048576      32   83284   33775    74330    74011   11335    4968   89200    38146    16536
         1048576      64   84624   31663    74443    74886   20401    7904   87859    52953    15252
         1048576     128   84119   32181    73625    74184   29406   13388  108887    45291    40198
         1048576     256   85066   32399    74223    74669   49404   19665  100221    61621    64905
         1048576     512   84766   32393    74304    74493   67391   27464  100663    56411    79136
         1048576    1024   84106   31842    74621    74230   77566   33689  104536    53872   101085
         1048576    2048   83457   34314    74327    73969   91260   37762  132794    59247   116495
         1048576    4096   82397   32363    74860    75167  111522   43053  141978    56101   106958
         1048576    8192   83243   33081    74453    75310  103258   41675  154065    63781   133428
         1048576   16384   82272   35016    75297    75820  106463   45219  151327    59112   126241
```

### With zcache ###
```
                                                            random  random    bkwd   record   stride
              KB  reclen   write rewrite    read    reread    read   write    read  rewrite     read
         1048576       4   81631   82966   170791   182281   70232    3767  315319    45913   236752
         1048576       8   83628   84875   178837   180704  122548    3974  258612    34192    58622
         1048576      16   86304   83955   182102   182047  169258    8889  349967    39811   211067
         1048576      32   85563   84217   179635   179207  215550   13790  366341    42330   286812
         1048576      64   85659   84699   180742   178811  219179   16523  366110    66318   296575
         1048576     128   85799   84037   181291   178420  189617   29399  372189    80316   223222
         1048576     256   86227   83899   180636   177483  174331   38991  319907    82751   288582
         1048576     512   85894   80082   178110   179869  167583   51452  282554    83002   194232
         1048576    1024   85372   80135   182361   181431  130953   59845  194552    82461   210398
         1048576    2048   85931   85151   179580   180170  124217   65891  220392    80128   188798
         1048576    4096   83560   77782   178131   180214  172986   77165  199148    86217   175917
         1048576    8192   85394   85720   178419   180836  165107   75002  261217    81979   259541
         1048576   16384   84441   85645   179645   181085  166825   71333  208200    82581   255815
```

### Comparison ###
  * (z) indicates run with zcache enabled

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/write.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/write.png)
| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 83644          | 85031       |

Ratio: 1.02

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/rewrite.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/rewrite.png)
| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 33051          | 83320       |

Ratio: 2.52

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/read.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/read.png)

| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 74315          | 179252      |

Ratio: 2.41

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/reread.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/reread.png)

| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 74555          | 180196      |

Ratio: 2.42

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/randomread.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/randomread.png)

| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 52544          | 160645      |

Ratio: 3.06

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/randomwrite.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/randomwrite.png)

| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 21525          | 39693       |

Ratio: 1.84

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/backwardread.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/backwardread.png)

| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 101463         | 285731      |

Ratio: 2.82

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/recordrewrite.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/recordrewrite.png)

| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 48773          | 68307       |

Ratio: 1.4

![http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/strideread.png](http://wiki.compcache.googlecode.com/hg/files/zcache/iozone/graphs/strideread.png)

| | without zcache | with zcache |
|:|:---------------|:------------|
| Average (KB/s) | 64052          | 222025      |

Ratio: 3.47

### Summary ###
  * Much better I/O performance with zcache enabled in all the cases
  * In particular, the case of Random Reads shows vastly superior (3x) performance with zcache enabled

## TODO ##
  * Test with different per-pool memlimits