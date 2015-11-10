# Introduction #

The Linux Terminal Server Project (LTSP) adds thin-client support to Linux servers. See project [home](http://www.ltsp.org/) for more info.


# Details #

### Environment ###

**Hardware**

Server: Pentium 4 2.0GHZ, IDE disk 7200rpm, 1G RAM
ThinClient: Old T42 IBM thinkpad, Pentium M 1.6GHZ, 124M RAM(limited with kernel cmdline, to emulate a legacy PC)
network:  1Gigabit network

**Software**

Server host: Debian sid,updated at April,23th
> LTSP(ltsp-server-standalone) + DHCP + NFS rootfs
Thin client: Debian etch with KDE, diskless( without swap )


### Experiments ###

When in RAM memory pressure, measure the following:
  * Responsiveness of kpdf(standard pdf reading tool in KDE) and firefox 2.0
  * Load capcity of kpdf and firefox 2.0

### Results ###

After the compcache was loaded:
  * The time of paging down one pdf page was reduced to 1/4~1/100
  * The time of switching from one firefox tab to another was reduced to 1/6
  * The capacity of kpdf was be increased from 2 pdf files to 11 pdf files.
  * The capacity of firefox was increased from 6 web pages to 15 web pages.