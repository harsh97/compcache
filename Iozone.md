# Introduction #
Any workload that has filesystem cache requirement that is nearly equal to amount of RAM while has minimal anonymous (heap) memory requirement, is expected to suffer maximum loss in performance with compcache enabled.

[Iozone](http://www.iozone.org/) filesystem benchmark can simulate exactly this kind of workload. As expected, this test shows loss of performance when compcache is used.

# Details #

### Configuration ###
  * CPU: Intel Core2 Duo T9600 @2.80GHz
  * RAM: Limited to 512M using "mem=512m" boot parameter
  * Disk: 7200 RPM

### Environment ###
  * All tests were run in runlevel 1 to minimize interference with random background processes.
  * Tests were done separately with:
    * Disk based swap only.
    * ramzswap only (without backing swap).
  * Kernel: 2.6.31-rc6, with:
    * [swap free notify patch](http://code.google.com/p/compcache/source/browse/patches/patch_swap_free_notify_2.6.31_rc6.diff).
    * compcache-0.6 equivalent patch.
  * Disk swap (when used): ~5.8G
  * ramzswap (when used): 25% of RAM = 25% of 512M = 128M

### Benchmark ###
```
/usr/bin/time -v -o time.out iozone -a -B -n 256M -g 1G -R -b iozone.xls -i 0 -i 1 -M -+u -+A 4 | tee iozone.out

Options:
 -a: Automatic mode.
 -B: Use mmap() files.
 -n: Minimum file size.
 -g: Maximum file size.
 -R: Generate excel output.
 -b: Excel output filename.
 -i: Selected tests: 0=read/re-read, 1=write/re-write.
 -M: uname() output in results.
 -+u: Enable CPU stats.
 -+A: Enable madvise behavior: 4=willneed.
```

This causes files from 256M to 1024M to be written/re-written followed be read/re-read. Also, madvise policy of _willneed_ means kernel will try to keep all this data in memory. This workload has filesystem cache requirement that is nearly twice available RAM (512M). So, any data swapped out to compcache simply reduces size available for this fs-cache which causes significant slowdown as shown below.

Following shows iozone performance for run over 512M and 1G file -- with and without ramzswap. In end, comparison is done for these two cases. The data for 256M file size is not shown since performance is identical in this case.


## Without ramzswap ##

![http://wiki.compcache.googlecode.com/hg/files/iozone/without_rzs_file_512m.png](http://wiki.compcache.googlecode.com/hg/files/iozone/without_rzs_file_512m.png)

![http://wiki.compcache.googlecode.com/hg/files/iozone/without_rzs_file_1g.png](http://wiki.compcache.googlecode.com/hg/files/iozone/without_rzs_file_1g.png)

## With ramzswap ##

![http://wiki.compcache.googlecode.com/hg/files/iozone/with_rzs_file_512m.png](http://wiki.compcache.googlecode.com/hg/files/iozone/with_rzs_file_512m.png)

![http://wiki.compcache.googlecode.com/hg/files/iozone/with_rzs_file_1g.png](http://wiki.compcache.googlecode.com/hg/files/iozone/with_rzs_file_1g.png)

## Comparison ##

In legend, _(c)_ means the data with memory compression (i.e. ramzswap) enabled.

![http://wiki.compcache.googlecode.com/hg/files/iozone/compare_file_512m.png](http://wiki.compcache.googlecode.com/hg/files/iozone/compare_file_512m.png)

![http://wiki.compcache.googlecode.com/hg/files/iozone/compare_file_1g.png](http://wiki.compcache.googlecode.com/hg/files/iozone/compare_file_1g.png)

## Summary ##

Elapsed (wall clock) time (h:mm:ss or m:ss):
| | without rzs | with rzs |
|:|:------------|:---------|
| time | 59:18       | 1:16:41  |

Slowdown with ramzswap: 24.8%