# Introduction #

zram disks (/dev/zramX) are generic in-memory R/W block devices. Here we measure I/O performance of these devices and compare it to plain ramdisks.

# Details #

## I/O performance was measured using 'dd' command ##

Common configuration for ram and zram disks case:
  * Tests were run natively (not in a VM)
  * Host OS: Fedora 11 x86\_64
  * Host configuration: CPU: Intel Core 2 Duo (T9600, 2.8GHz), RAM: 4G
  * Boot into runlevel 3 and turn off crond, atd, cpuspeed

## ram disk case ##
  * Create 2G ram disk using ramdisk\_size boot param
  * mkfs.ext4 /dev/ram0
  * mount /dev/ram0 /mnt/other

## zram disk case ##
  * Create 2G zram disk using zramconfig
  * mkfs.ext4 /dev/zram0
  * mount /dev/zram0 /mnt/other


## Read benchmark ##
1G file was copied to (z)ramdisk which was then synchronously read into /dev/null using different block sizes.
```
if [ -z "$1" ]; then
	echo "Missing file to read into /dev/null"
	exit 1
else
	FI="$1"
fi

if [ -z "$2" ]; then
	echo "Missing file to dump output"
	exit 1
else
	FO="$2"
fi

for B in 64 128 256 512 1K 2K 4K 8K 16K 32K 64K 128K 256K 512K 1M; do
	echo "$B" | tee -a "$FO"
	echo 1 > /proc/sys/vm/drop_caches
	echo 1 > /proc/sys/vm/drop_caches
	sleep 1

	dd if="$FI" of=/dev/null bs="$B" iflag=sync 2>&1 | tee -a "$FO"
done
```

## Write benchmark ##
First make sure that test (512M) file was completely cached. Then synchronously write this file to (z)ramdisk.
```
if [ -z "$1" ]; then
	echo "Missing file to be written"
	exit 1
else
	FI="$1"
fi

# make sure input file is cached
dd if="$FI" of=/dev/null
dd if="$FI" of=/dev/null
dd if="$FI" of=/dev/null
dd if="$FI" of=/dev/null

for B in 64 128 256 512 1K 2K 4K 8K 16K 32K 64K 128K 256K 512K 1M; do
	echo "$B" | tee -a "$FO"
	dd if="$FI" of=`basename "$FI"` bs="$B" oflag=sync 2>&1 | tee -a "$FO"
done
```

## Results ##

### Read Speed (MB/sec) ###
```
BlockSize	ramdisk	zramdisk
64		90.3	88.6
128		176	156
256		300	245
512		483	354
1K		695	450
2K		890	520
4K		1024	567
8K		1126.4	586
16K		1126.4	598
32K		1126.4	601
64K		1126.4	603
128K		1126.4	604
256K		1228.8	605
512K		1228.8	607
1M		1126.4	604
```

![http://wiki.compcache.googlecode.com/hg/files/zram/ddbench/readperf.png](http://wiki.compcache.googlecode.com/hg/files/zram/ddbench/readperf.png)

### Write Speed (MB/sec) ###
```
BlockSize	ramdisk	zramdisk
64		7.3	2.9
128		13.7	5.5
256		23.7	9.7
512		37.5	15.6
1K		52.9	24.8
2K		67	29.3
4K		79.1	41.8
8K		136	58.8
16K		218	87.4
32K		310	114
64K		393	133
128K		454	147
256K		481	156
512K		500	159
1M		403	160
```

![http://wiki.compcache.googlecode.com/hg/files/zram/ddbench/writeperf.png](http://wiki.compcache.googlecode.com/hg/files/zram/ddbench/writeperf.png)