# Introduction #

How much faster is I/O from ramzswap compared to hard-disk? To determine this we check how long it takes for individual swap R/W operations to complete under heavy swapping conditions.

# Details #

## Test config ##
  * RAM: 2G
  * CPU: Intel Core2 CPU 6400 @ 2.13GHz
  * Swap partition (hard-disk): 4G
    * Western Digital Raptor ([WD1600ADFS](http://www.newegg.com/Product/Product.aspx?Item=N82E16822136216))
    * 10000 RPM
    * 16MB Cache SATA 3.0Gb/s 3.5" Hard Drive
  * ramzswap: 512MB (without backing swap) - swap usage during testing never exceeds 410MB.

## Test details ##
  * start swap systemtap probe script (see below).
  * run [scan](http://linux-mm.org/PageReplacementTesting) benchmark to induce heavy swap I/O - it does scans of anonymous mapped memory, both cyclic and use once.
  * ./scan 1800 10 (i.e. allocate 1800MB and scan over it 10 times).
  * background tasks: window manager(kde4), music player(audacious), terminal(konsole).
  * stop systemtap script once above scan command completes. On termination it shows swap I/O timing stats.

We use [SystemTap](http://sourceware.org/systemtap/wiki) to determine time taken by individual swap I/O. Following is the systemtap script used:

```
global time_read
global time_read_aggr

global time_write
global time_write_aggr

probe begin {
        print("swap probe begin\n")
}

probe end {
        print("swap probe end\n")
}

/* swap read */
probe kernel.function("swap_readpage") {
        time_read[tid()] = gettimeofday_ns();
}

probe kernel.function("end_swap_bio_read") {
        if (time_read[tid()]) {
                time_read_aggr <<< (gettimeofday_ns() - time_read[tid()])
                delete time_read[tid()]
        }
}

/* swap write */
probe kernel.function("swap_writepage") {
        time_write[tid()] = gettimeofday_ns();
}

probe kernel.function("end_swap_bio_write") {
        if (time_write[tid()]) {
                time_write_aggr <<< (gettimeofday_ns() - time_write[tid()])
                delete time_write[tid()]
        }
}
```

# Output #
### For disk swap ###
```
time_read_aggr @count=0xc465 @min=0x1e1d @max=0x2778b69d5ef @sum=0x7b4718532b7 @avg=0xa0b13c9
time_write_aggr @count=0x1bb6 @min=0x1d0e @max=0x773d04af3e @sum=0x24b9c4f02a9 @avg=0x15347b3c
```

### For ramzswap ###
```
time_read_aggr @count=0x412fba @min=0x59b @max=0x195473f @sum=0xc02d2b158 @avg=0x2f2b
time_write_aggr @count=0x414e35 @min=0x929 @max=0x15fcacd @sum=0x6fbdbb071 @avg=0x1b60
```

## Results ##
Comparing average R/W times:
|       | disk   | ramzswap |
|:------|:-------|:---------|
| read  | 168 ms | 12 us    |
| write | 355 ms | 7 us     |

## Notes ##
  * It is surprising that ramzswap average read time > write time. LZO decompression is much faster than compression. Maybe there are some bottlenecks in ramzswap\_read()?