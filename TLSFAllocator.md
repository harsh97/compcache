# Introduction #

This page provides performance evaluation of TLSF (Two Level Segregated Fit) allocator. Details of this allocator can be found [here](http://rtportal.upv.es/rtmalloc/).


# Details #

Some changes have been made to this allocator to suite needs of this project:
  * Now 'Pool' can dynamically grow/shrink. Each pool consists of set of contiguous regions of memory. This also required change in TLSF interface.
  * Removed realloc()
  * Cleanups: Macros -> static inline functions etc.
  * Code:
    * [Original](http://code.google.com/p/compcache/source/browse/trunk/sub-projects/allocators/tlsf-copy/tlsf.c) (userspace)
    * [Modified](http://code.google.com/p/compcache/source/browse/trunk/sub-projects/allocators/tlsf-edit/tlsf.c) (userspace - used in this benchmark)

This has now been ported to Linux kernel with some additional changes (code [here](http://code.google.com/p/compcache/source/browse/trunk/sub-projects/allocators/tlsf-kmod)).

**A slight variation of this extended TLSF allocator is now [included in Xen](http://xenbits.xensource.com/xen-unstable.hg?file/48fba1dbcfaf/xen/common/xmalloc_tlsf.c)!**

# Performance #

Performance evaluation is done by [replaying](SwapReplay.md) this [data](http://compcache.googlecode.com/files/sample_sr_data_2.bz2) over TLSF allocator.

Following plot shows effect on memory usage by allocator as _region_ size was varied from 4K to 64K:

([Here](http://compcache.googlecode.com/svn/wiki/files/plot_tlsf_data.tar.bz2) are text data files plotted below).


![http://compcache.googlecode.com/svn/wiki/files/tlsf_ideal_compare_plot.gif](http://compcache.googlecode.com/svn/wiki/files/tlsf_ideal_compare_plot.gif)

Zooming on swap requests above 50K:


![http://compcache.googlecode.com/svn/wiki/files/tlsf_ideal_compare_plot_part.gif](http://compcache.googlecode.com/svn/wiki/files/tlsf_ideal_compare_plot_part.gif)

## Data Summary ##

Following shows, number of times tlsf\_alloc(), tlsf\_free() is called along with average time per call spent in these functions. Also, get\_mem(), put\_mem() shows no. of times allocator had to go down to system allocator (for adding/freeing region).

![http://compcache.googlecode.com/svn/wiki/files/tlsf_profile.gif](http://compcache.googlecode.com/svn/wiki/files/tlsf_profile.gif)

This profiling is done with TLSF region size of 16k. Region size has almost no effect on allocator speed.

(System: Linux VM (VMware) with 512M RAM - underlying processor: Pentium4 2.8GHz)

## Conclusions ##
  * TLSF allocator with region size of 16K looks most memory space efficient.
  * Region size has almost no effect on allocator speed.

## Notes/Todo ##
  * Need to compare this with other allocators e.g. [DLM Allocator](http://g.oswego.edu/dl/html/malloc.html)
  * Run all above tests with many more different SwapReplay traces.