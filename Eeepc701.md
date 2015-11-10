# Introduction #

Ubuntu uses compcache in its LiveCDs and LiveUSBs. Ubuntu is uses the default size of 25%. This benchmark tests the hypothesis that much larger compcaches would provide better real world performance as the kernel would not use the compcache unless it is needed. Based on this benchmark I would reject the hypothesis; increasing the size of the compcache to 100% of ram may well impair performance.

# Details #

## Test machine ##
The test machine was Eeepc 701 4G netbook, which has 512MB of ram.
I booted off a Ubuntu 9.10 Kingston USB Datatraveler 4G G2 that was
created with usbcreator and had persistence enabled. A recent version
of compcache from hg was used. (from around the new year of 2010)

This machine was chosen as it is a popular machine with only 512MB of ram, so it could benefit from a well chosen compcache size

## Benchmark ##

The benchmark opens a large number of copies of my thesis. It then cycles through the copies three times, openning and closing them. Based on a sample size of one, this is the common use of Ubuntu; while this sample is both small and biased any application benchmark is somewhat arbitrary. This particular use case was enough to demonstrate that larger compcaches could impair application (rather than synthetic) performance.

The benchmark avoids pushing total memory free (including 50% of swap
when using compcache) under 128MB as I have determined that Ubuntu
Karmic is unstable when less than 100MB of memory is free, and may
also be unstable when in the 100-128MB range.

## Observations on Compression Ratios ##

In this benchmark the compression ratio was good, it seemed to always
give at least a 4:1 compression ration, and sometimes much more. For
example, consider the following three lines from out/rzstat.log
```
  OrigDataSize:     513404 kB
  ComprDataSize:     75090 kB
  MemUsedTotal:      77040 kB
```
This compression ratio was not artificial, the test involved opening
many copies of my masters thesis in xpdf. As I understand that
compcache does not merge identical pages, this compression ration
reflects a possible real world use, and I have also noticed a solid
4:1 compression ratio on my 4GB x86\_64 desktop under my normal
day-to-day use.

The compression ratio is guaranteed to be better than 1:1. Even if we fill memory with random data, some of the pages evicted will be from system processes that will compress well. On a 256MB Ubuntu machine we saved [39, 48, 48, 59 and 62 MB](http://spreadsheets.google.com/ccc?key=0AkuyLOeO1dSpdF9Ic1BldE1Fb0tGaGFteFAzbXFfQlE&hl=en) by using compcaches of 25%, 50%, 75%, 100% and 105% of ram respectively; even though the workload was filling memory with random data.

## Hypothesis ##
My hypothesis was that large compcaches, up to 100% of ram would
improve performance as the kernel wouldn't use the swap unless it was
beneficial.  This test suggests that the hypothesis is wrong; in 11/12
cases, a 75% compcache outperformed a 100% compcache. This test does
support my previous conclusion that a little compcache is better than
none. In all three runs both 25% and 50% of compcache out performed no
compcache.

## Conclusion ##

This benchmark supports the finding from the 500MHz benchmark that %25 compcache is better than none. From a performance point of view either 25% or 50% seems a reasonable choice; 50% seems a safe choice because it gives some extra protection against OOM kills. Additionally, the high compression ratios suggest that a compcache device of size 50% may take only 10% of ram when full which supports the idea there should not be big performance regressions.

If you need to fit more data in memory you will need a larger compcache, but do not besurprised if setting compache to 100% gives worse performance when a smaller compcache would suffice.

## Notes ##

This test would need to be repeated under different workloads to be
taken very seriously. I'll rerun it when SWAP\_FREE\_NOTIFY enters the
linux kernel, as this is likely to improve the performance of large
compcaches.

Xpdf was used instead of evince, as evince continually stops and asks the user if they want to restore previously open documents. This makes benchmarking evince a pain.

Note that when we say n% compcache we mean that the swap device is
that size. The stored data will be much smaller. Given the good
compression ratios above a compcache of 400% could well be reasonable
for some workload.

## Raw Data ##

C0, C1, C2, C3, C4 indicate 0%, 25%, 50%, 75% 100% sized compcaches
respectively.
The following number is the number of instances of xpdf running at the
same time. The final number is the number of seconds to cycle through
the instances three times, individually opening and closing them.
Smaller numbers are better.

```
$ grep real `ls cycle_?/time.f.*` | sed s/NONE/C0/ | sort -n -k2 |
sort -t. -k 4,3 -n
cycle_3/time.f.C0.16.y:real 85.56
cycle_3/time.f.C1.16:real 79.21
cycle_3/time.f.C2.16:real 83.90
cycle_3/time.f.C3.16:real 80.61
cycle_3/time.f.C4.16:real 82.78

cycle_2/time.f.C0.19.y:real 95.11
cycle_2/time.f.C1.19:real 93.81
cycle_2/time.f.C2.19:real 94.97
cycle_2/time.f.C3.19:real 97.08
cycle_2/time.f.C4.19:real 100.12

cycle_1/time.f.C0.21.y:real 112.95
cycle_1/time.f.C1.21:real 104.62
cycle_1/time.f.C2.21:real 105.70
cycle_1/time.f.C3.21:real 105.77
cycle_1/time.f.C4.21:real 105.85

cycle_3/time.f.C1.25.y:real 196.78
cycle_3/time.f.C2.25:real 229.23
cycle_3/time.f.C3.25:real 234.60
cycle_3/time.f.C4.25:real 314.58

cycle_2/time.f.C1.28.y:real 212.55
cycle_2/time.f.C2.28:real 204.09
cycle_2/time.f.C3.28:real 207.16
cycle_2/time.f.C4.28:real 189.46

cycle_1/time.f.C1.36.y:real 239.58
cycle_1/time.f.C2.36:real 214.54
cycle_1/time.f.C3.36:real 212.59
cycle_1/time.f.C4.36:real 568.55

cycle_2/time.f.C2.36.y:real 380.01
cycle_2/time.f.C3.36:real 438.92
cycle_2/time.f.C4.36:real 537.79

cycle_3/time.f.C2.36.y:real 382.23
cycle_3/time.f.C3.36:real 509.71
cycle_3/time.f.C4.36:real 537.41

cycle_1/time.f.C2.47.y:real 396.81
cycle_1/time.f.C3.47:real 347.85
cycle_1/time.f.C4.47:real 755.35

cycle_3/time.f.C3.47.y:real 777.56
cycle_3/time.f.C4.47:real 906.89

cycle_2/time.f.C3.51.y:real 807.07
cycle_2/time.f.C4.51:real 1044.15

cycle_1/time.f.C3.54.y:real 777.31
cycle_1/time.f.C4.54:real 837.70

cycle_3/time.f.C4.60.y:real 1371.86
cycle_1/time.f.C4.65.y:real 1517.96
cycle_2/time.f.C4.65.y:real 3212.10
```