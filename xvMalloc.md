# Introduction #

xvMalloc summary: ([pdf](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/xvMalloc.pdf))

xvMalloc is memory allocator designed specifically for compcache project. It is based on [TLSF allocator](http://rtportal.upv.es/rtmalloc/) so we naturally get all its goodness :)

  * O(1) Alloc/Free - Except for cases where we have to go to system page allocator to get additional memory.
  * Very low fragmentation - In all tests, xvMalloc memory usage is within 10% of “Ideal” allocator.

xvMalloc is not derived from TLSF implementation (as provided on its project page). Its written from scratch to cleanly incorporate changes described below.

## xvMalloc vs TLSF ##

TLSF has some problems when used as-is:

  * It assumes that its dealing with virtual memory.
  * It is designed to handle allocation for large size range [32, 2^32) while our need is to handle very small size range, say, [32, 3/4\*PAGE\_SIZE]. So, we can have very fine grained size lists for entire size range.
    * Due to nature of TLSF, size difference between free lists increases rapidly as size increases (for objects > 1024b, free lists are separated by 128 bytes!)
    * We have equally spaced (just 8 bytes!) free lists for entire size range. We still use two level bitmap to locate appropriate free list.

xvMalloc overcomes above problems and also makes some assumptions to improve performance and avoid unnecessary complexity. Assumptions:

  * Allocation size is always in range [MIN, MAX] and range (MAX – MIN) is small. For compcache, this range is, say, [32, 3/4\*PAGE\_SIZE].
    * This allows large number of freelists with small size difference resulting in lesser fragmentation.
  * Memory being managed is not backed by any VA. Allocator records <pageNum, offset> for each object. So, we need to provide implementation for map/unmap functions that provide dereferenceable pointer given this <pageNum, offset> pair (kmap\_high()).

  * Per object overhead:
    * TLSF: 16b, xvMalloc: 4b

  * xvMalloc stores **exact** object size in object header (TLSF stores rounded-off size).  This saves additional 2bytes per object since; otherwise, user has to store this value in beginning of data section (specific to compcache) and further store all compressed data with slow unaligned access.

## Implementation Details ##
  * It uses same two level bitmap scheme as used by TLSF to manage freelists with changes to alloc/free as described above. TLSF details are well explained in this paper ([pdf](http://rtportal.upv.es/rtmalloc/files/MRBC_2008.pdf)).
  * Code:
    * Checkout from repository:   `hg clone https://compcache.googlecode.com/hg/ compcache compcache (xvMalloc code in trunk/sub-projects/allocators/xvmalloc-kmod)`
    * Browse online [here](http://code.google.com/p/compcache/source/browse/#hg/sub-projects/allocators/xvmalloc-kmod).
    * Currently, its implemented in userspace for easy debugging and exp. Should be easy to port to kernel.

## Limitations ##
  * No concern for scalability.
    * Its used only from swap path, so it this really a problem?
  * No concern for locality. Page selection for any allocation is function of only request size.

## Evaluation ##
> _Additional performance numbers are [here](xvMallocPerformance.md)._

  * **Category 1:** Tests with randomly generated workloads with each executing 1 million mallocs and frees.

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_1.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_1.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|2604|2824|8.4%|


---


  * **Category 2:** With real workloads, we tend to have majority of allocations within a small range.

Following shows traces collected from actual desktop system with "generic" workload.

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/swap_trace_desktop.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/swap_trace_desktop.gif)

In above trace, compressed length `[1024, 2600]` is most common and also sizes < 64.
We approximate this using random workload generator with bias for this size range.

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/random_prefer_range_1024_2600.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/random_prefer_range_1024_2600.gif)


Test result with above workload

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_1.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_1.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|2421|2548|5.2%|


### Additional performance numbers are [here](xvMallocPerformance.md). ###

## Links ##
  * xvMalloc: trunk/sub-projects/allocators/xvmalloc-kmod ([webview](http://code.google.com/p/compcache/source/browse/#hg/sub-projects/allocators/xvmalloc-kmod))
  * Allocator stress framework (used to generate above data): trunk/sub-projects/alloc\_stress ([webview](http://code.google.com/p/compcache/source/browse/#hg/sub-projects/alloc_stress))
  * TLSF Allocator [homepage](http://rtportal.upv.es/rtmalloc/).


---


BTW, "xv" vaguely stands for "x"->no, "v"->virtual - we don't need tons of virtual address space :)