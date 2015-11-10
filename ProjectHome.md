![http://compcache.googlecode.com/svn/wiki/files/compcache.png](http://compcache.googlecode.com/svn/wiki/files/compcache.png)


**All further development is now being done on the mainline Linux kernel; the code on this site is no longer maintained. Various wiki pages are retained here only for reference.**

This project creates RAM based block device (named **zram**) which acts as swap disk. Pages swapped to this disk are compressed and stored in memory itself.

Compressing pages and keeping them in RAM virtually increases its capacity. This allows more applications to fit in given amount of memory.

The usual argument I get is - **memory is so cheap so why bother with compression**? So I list here some of the use cases. Rest depends on your imagination :)

  * **Netbooks**: Market is now getting flooded with these "lightweight laptops". These are memory constrained but have CPU enough to drive on compressed memory (e.g. [Cloudbook](http://en.wikipedia.org/wiki/CloudBook) features 1.2 GHz processor!).

  * **Virtualization**: With compcache at hypervisor level, we can compress _any_ part of _guest_ memory transparently - this is true for _any_ type of Guest OS (Linux, Windows etc.). This should allow running more number of VMs for given amount of total host memory.

  * **Embedded Devices**:  Memory is scarce and adding more memory increases device cost.  Also, flash storage suffers from wear-leveling issues, so its useful if we can avoid using them as swap device.

## Contact/Mailing Lists ##
linux-mm-cc at laptop dot org ([Info Page](http://lists.laptop.org/listinfo/linux-mm-cc))

ngupta at vflare dot org ([vflare.org](http://www.vflare.org/))

## Help ##
  * CompilingAndUsing (for version 0.5.x)
  * CompilingAndUsingNew (for version 0.6.x)

## News ##
  * Jan 30, 14 - zram is now part of the official Linux kernel tree and will be part of kernel 3.14 release. Finally!
  * Oct 2, 12 - compcache-0.6.2 is marked deprecated (so nothing available for download now). Please use the version available in the mainline (drivers/staging/zram). It should move out of staging when kernel 3.8 is released.
  * Nov 20, 11 - Project migrated to Git. Please see updated clone URLs in the Source tab.
  * Apr 27, 10 - Project 'Memory Compression for Virtualized Environments' has been selected in **Google Summer of Code** program. The plan is to re-implement compcache with a new approach that further reduces overheads and be useful from embedded devices to server environments.
  * Jan 25, 10 - Using compcache on multi-core machine? Refer [Scalability](http://code.google.com/p/compcache/wiki/Scalability).
  * Jan 25, 10 - compcache-0.6.2 is released. Contains lots of cleanups, fixes, works with kernel 2.6.32 and xvmalloc is no longer a separate module. Please see Changelog for details.
  * Dec 12, 09 - compcache is now in mainline -- it will be part of kernel 2.6.33.
  * Oct 17, 09 - compcache is now included in linux-next! Currently, this mainline version is almost equivalent to compcache-0.6 but without the swap notify support.
  * Oct 17, 09 - compcache-0.5.4 is released. This is to support kernel 2.6.27 or older (see [Issue #36](https://code.google.com/p/compcache/issues/detail?id=#36)). It also includes 'swap notify' patch. CompilingAndUsing page has been updated with all the details.
  * Aug 20, 09 - compcache-0.6 is released.
  * Aug 09, 09 - multiple\_rzs and default branch merged.
  * Aug 06, 09 - compcache-0.6-pre3 released. It includes experimental support for _swap free notify_ feature which eliminates any stale data from ramzswap -- README and CompilingAndUsingNew has details on how to enable this feature. It also includes fix for invalid stats reporting on ARM ([Issue #34](https://code.google.com/p/compcache/issues/detail?id=#34)). See Changelog for details.
  * Jul 20, 09 - compcache-0.6-pre2 is out. It includes fix for crashes on ARM (see [Issue #33](https://code.google.com/p/compcache/issues/detail?id=#33)). See Changelog included for full list for changes.
  * Jul 14, 09 - compcache-0.6-pre1 released! New features include creating multiple ramzswap devices, ability to have _file_ as a backing swap. See CompilingAndUsingNew for help. This required major code changes, so it needs good amount of testing before any serious use.
  * May 28, 09 - Project switches to mercurial.
  * May 26, 09 - [compcache article](http://lwn.net/Articles/334649/) featured at **LWN.net**.
  * Apr 08, 09 - compcache 0.5.3 released. This includes **major** cleanups. LZO modules are no longer included in package since most distros now already include these. There is change in name for module and parameters - README and CompilingAndUsing page are updated to reflect these changes.
  * Apr 03, 09 - It could not make it into 2.6.30 due to lack of supporting data. See this [post on LKML](http://lkml.org/lkml/2009/4/1/514). Now the point is, what data to collect? Can we get it by next kernel release??
  * Mar 29, 09 - Effort to push it to mainline has [started](http://lkml.org/lkml/2009/3/28/236). I have uploaded the kernel patch in Downloads section. Please test it with whatever benchmarks you can think of and let me know **any** problems you get. Your support can help make it into mainline. Please note that there is change in module name (compcache -> ramzswap) and change in names of configuration parameters. So, please go through Documentation/blockdev/ramzswap.txt. The patch applies against 2.6.29 (during menuconfig, select Device Drivers -> Block Devices -> Compressed RAM swap device). Thanks!
  * Mar 11, 09 - compcache 0.5.2 released! Most notable features include ability to send incompressible pages to physical swap and no memory allocation for zero-filled pages. See Changelog included for all details. There are also changes to module parameters so check updated CompilingAndUsing page before using.
  * Feb 24, 09 - Now we can forward incompressible pages to physical swap disk! Support is still experimental but I hope to get it stable by next release :)
  * Feb 23, 09 - TipsAndTricks wiki created!
  * Jan 22, 09 - compcache-0.5.1 released. This fixes crash that could happen only on x86 with highmem (mem > ~1G). See [Issue #20](https://code.google.com/p/compcache/issues/detail?id=#20) for details.
  * Jan 15, 09 - compcache-0.5 is out! This completes transition to new xvMalloc memory allocator. There is one additional bugfix in this final release.
  * Jan 10, 09 - compcache-0.5pre4 released. Finally this includes ability to free memory associated with freed swap blocks (similar to what compcache patch does but less aggressive). This feature requires 2.6.29-rc1 - if compiled on older kernel, this functionality will not be available. It also fixes incorrect stats reporting in /proc/compcache. See Changelog included for more details.
  * Jan 05, 09 - compcache-0.5pre3 released. Fixes system freeze [issue #19](https://code.google.com/p/compcache/issues/detail?id=#19) (tested on x86 and x64).
  * Dec 28, 08 - compcache-0.5pre2 released - see Changelog included for details. Happy New Year!
  * Dec 15, 08 - compcache-0.5pre1 released! This is preview release integrates xvMalloc memory allocator replacing TLSF.
  * Oct 03, 08 - xvMalloc: New memory allocator. This removes dependence on kernel vmalloc area. Its also quite memory efficient! Details [here](xvMalloc.md).
  * Aug 13, 08 - [compcache-0.4](http://compcache.googlecode.com/files/compcache-0.4.tar.gz) now available. Nothing new but few bug fixes and lot of cleanups. See Changelog included for details.
  * Jul 18, 08 - After lot more rigorous testing (on x86), compcache patches are now available from Downloads area. For help on using these patches, refer [Patches](http://code.google.com/p/compcache/wiki/Patches). Out-of-tree kernel modules are still available.
  * Jul 14, 08 - Patch for 2.6.26 is ([here](http://compcache.googlecode.com/files/patch_compcache_with_notify_support_2.6.26.diff)) and here is [Changelog](http://code.google.com/p/compcache/wiki/Changelog).
  * Jun 29, 08 - Here comes patch for Linux kernel 2.6.26-rc8 ([download](http://code.google.com/p/compcache/source/browse/trunk/sub-projects/mainline/patch_compcache_with_notify_support_2.6.26-rc8.diff)). It fixes bug in previous patch that could cause segfault when loading module and free invalid memory in swap free notify callback.
  * Jun 17, 08 - Patch for Linux kernel 2.6.26-rc6 for compcache is [here](http://code.google.com/p/compcache/source/browse/trunk/sub-projects/mainline/patch_compcache_with_notify_support_2.6.26-rc6.diff) - Its better than compcache-0.3 in terms of space efficiency, speed and also has lot of cleanups.
  * May 13, 08 - Some [Performance](http://code.google.com/p/compcache/wiki/Performance) numbers.
  * Apr 28, 08 - Todo list is [here](http://code.google.com/p/compcache/wiki/Todo)
  * Mar 17, 08 - [compcache-0.3](http://compcache.googlecode.com/files/compcache-0.3.tar.gz) Released with more bug fixes!
  * Mar 03, 08 - [compcache-0.2](http://compcache.googlecode.com/files/compcache-0.2.tar.bz2) Released! Now it also exports statistics through /proc.
  * Feb 20, 08 - [compcache-0.1](http://compcache.googlecode.com/files/compcache-0.1.tar.bz2) Released!
  * Feb 14, 08 - [TLSFAllocator](http://code.google.com/p/compcache/wiki/TLSFAllocator) - Performance evaluation of [TLSF](http://rtportal.upv.es/rtmalloc/) allocator.
  * Jan 24, 08 - SwapReplayDesign - Design and usage information. Work on allocator continues.
  * Jan 09, 08 - SwapReplay sub-project now functional! Will post some documentation soon.
  * Dec 21, 07 - Now compcache can dynamically resize - pages are allocated on demand and freed when not required. For now, compression structure is not yet implemented i.e. compressed page still requires 1 page for storage. Compression structure (heart of this project!) is currently under design.

## Acknowledgments ##
Thanks to John McCabe-Dansted and Nai Xia for their testing efforts.
Thanks to Pekka Enberg and Hugh Dickins for code reviews.

---


_Any problem in computer science can be solved with another layer of indirection. But that usually will create another problem._ - David Wheeler