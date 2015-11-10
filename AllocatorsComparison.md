# Introduction #


Allocators compared:
  * Ideal: No wastage - uses exactly as much space as required
  * TLSF: See [TLSFAllocator](TLSFAllocator.md)
  * kmalloc: Linux kernel (2.6.25) standard allocator ([SLUB](http://lwn.net/Articles/229984/))

Comparison between xvMalloc and TLSF is [here](http://code.google.com/p/compcache/wiki/xvMalloc). In summary, xvmalloc is ~2% more space efficient than TLSF. Also, note that space efficiency over TLSF is not the only reason to favor xvMalloc as detailed in above wiki.

This data was collected by replaying (see: [SwapReplay](SwapReplay.md)) [this data](http://compcache.googlecode.com/svn/wiki/files/sample_sr_data_2.bz2) over allocator test modules. So, in effect, we replaced compcache allocator with these allocators and noted memory usage over time.

# Details #

Data plotted is available **[here](http://compcache.googlecode.com/svn/wiki/files/allocators_ideal_tlsf_kmalloc_compare.tar.gz)**

![http://compcache.googlecode.com/svn/wiki/files/allocators_ideal_tlsf_kmalloc_compare.gif](http://compcache.googlecode.com/svn/wiki/files/allocators_ideal_tlsf_kmalloc_compare.gif)

## Data Summary ##

Peak Memory Usage:
  * Ideal: 24947 KB
  * TLSF: 25377 KB
  * kmalloc(SLUB): 36483 KB

So, KMalloc uses ~43% more memory than TLSF!