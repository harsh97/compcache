# Introduction #
Each test done as follows:
  1. Start some workload within VM (VMware)
  1. Suspend VM - this gives memory dump (vmem) file
  1. Compress vmem file page-by-page and dump compressed length for each page (compression done using LZO1X)
  1. Plot compressed length size distribution

# Details #

VM: Linux (Fedora 10, x86). VCPU: 1, RAM: 512M

Workload: "general desktop" - web browsers, text editors, media player, mail client.

![http://compcache.googlecode.com/svn/wiki/files/clen_distrib/clen_distribution_f10_general.png](http://compcache.googlecode.com/svn/wiki/files/clen_distrib/clen_distribution_f10_general.png)


---


VM: Linux (Fedora 10, x64), VCPU: 1, RAM: 512M

Workload: kernel compile - suspended VM 15 mins after kernel compile started (to let it come to steady state).

![http://compcache.googlecode.com/svn/wiki/files/clen_distrib/clen_distribution_f10_x64_kernel_compile.png](http://compcache.googlecode.com/svn/wiki/files/clen_distrib/clen_distribution_f10_x64_kernel_compile.png)


---


VM: WinXP SP2, VCPU: 1, RAM: 512M

Workload: "general desktop" - apps set similar to that of Linux.

![http://compcache.googlecode.com/svn/wiki/files/clen_distrib/clen_distribution_winxp_general.png](http://compcache.googlecode.com/svn/wiki/files/clen_distrib/clen_distribution_winxp_general.png)


---


VM: WinXP SP2, VCPU: 1, RAM: 512M

Workload: [HammerOra](http://hammerora.sourceforge.net/) on [Oracle 10g EE](http://www.oracle.com/technology/products/database/xe/index.html) (TPC-C driver script used with 10 virtual users each with 10 iterations) - suspended VM 15 mins after tests started.

![http://compcache.googlecode.com/svn/wiki/files/clen_distrib/clen_distribution_winxp_hammerOra_Oracle10gEE.png](http://compcache.googlecode.com/svn/wiki/files/clen_distrib/clen_distribution_winxp_hammerOra_Oracle10gEE.png)