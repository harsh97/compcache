More xvMalloc performance data. Allocator homepage is [here](xvMalloc.md).


---


  * **Category 1**: Tests with randomly generated workloads with each executing 1 million mallocs and frees.
  * **Category 2**: With real workloads, we tend to have majority of allocations within a small range. We simulate this in testing.


---


## Category 1 ##

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_2.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_2.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|2492|2656|6.5%|

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_3.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_3.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|1484|1362|8.9%|

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_4.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_4.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|3684|3908|6%|

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_5.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_5.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|2016|1840|9.5%|


---


## Category 2 ##

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_2.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_2.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|1239|1324|6.8%|

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_3.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_3.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|2480|2301|7.7%|

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_4.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_4.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|3120|2931|6.4%|

![http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_5.gif](http://compcache.googlecode.com/svn/wiki/files/xvmalloc/mem_xv_ideal_1mn_pr5_5.gif)

| **Data Summary** | | | |
|:-----------------|:|:|:|
|                  |Ideal|xvMalloc|Diff|
|Peak Memory Used (KB)|2748|2578|6.6%|