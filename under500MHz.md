# Introduction #

**Contributed by: John McCabe-Dansted**

This benchmark roughly attempts to determine how compcache performs
on loads such that we could run without swap. It is run on a Pentium
500 with 256MB of ram running Xubuntu. It opens my thesis in multiple
evince windows, and measures the time taken to open n copies, then
cycles through opening and closing them.

I have put up a benchmark up at:
> http://www.ucc.asn.au/~mccabedj/compcache/
This is script based, and theoretically could be run by others,
although in practice I'd want to clean them up a bit first:
> http://www.ucc.asn.au/~mccabedj/compcache/benchmark_new_sh.zip

The spreadsheet summary is time.ods, the raw data is in final.tgz.
> http://www.ucc.asn.au/~mccabedj/compcache/time.ods
> http://www.ucc.asn.au/~mccabedj/compcache/final.tgz

# Details #

The benchmark is not consistent. For some reason, in the beginning
there is no real difference whether swap is used or not. However as
time goes on, using no swap becomes a really bad idea... maybe there
is some form of memory leak in Xubuntu? Anyway, under no circumstance
does compcache degrade performance (compared to optimum options) by
more than 2%, while using no swap can cause much strangeness,
particularly under heavy load.

One concern about compcache is that it never really frees compressed
pages as it cannot know whether they are still in use. To measure this
I included the "rev" code, which means that we have had heavy load
(and have not unused compcache since) This does not cause significant
(>1% ish) performance problems in this benchmark, however since we are
only testing memory loads that do not truly need swap, we only used at
most 68MB of compcache (about 25MB compressed I think) so we would not
expect severe performance problems in any case.

HDD seems to fairly consistently outperform CompCache by a tiny margin
on this machine. While performance without swap can vary wildly, both
HDD swap and CompCache seem to give decent solid performance. Hence I
would recommend that Pentium 500's use HDD swap if available, use
CompCache swap otherwise and try to avoid swapless configurations if
they have only 256MB of RAM.

The maximum about of compcache actually used  in this benchmark  was
68MB, over 25% of ram, and as discussed does not degrade performance
significantly here (even with rev and _swappi: see time.ods). Thus the
default of 25% is not too large for this purpose. A larger compcache
may be better, but I cannot conclude that from these benchmarks since
I have not tested use of more than 68MB swap._

-- John C. McCabe-Dansted, PhD Student, University of Western Australia