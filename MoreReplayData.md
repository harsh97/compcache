# Introduction #

All contributed SwapReplay data files will be appended here.

# Details #

  * **[Data 1](http://compcache.googlecode.com/svn/wiki/files/sample_sr_data_2.bz2)**

  * System:
    * Swap=64M, mem=128M
    * Workload: web browers(ffox, konq), mp3 player(amarok), mail app(kmail), kpdf, konsole. Use these apps for a while. Close and reopen mix of these apps.

  * Compressed page size histogram (plotted below): **[Data 1 Histo](http://ccompcache.googlecode.com/svn/wiki/files/sample_sr_data_2_histo.txt)**

![http://compcache.googlecode.com/svn/wiki/files/sample_sr_data_2_histo_plot.gif](http://compcache.googlecode.com/svn/wiki/files/sample_sr_data_2_histo_plot.gif)

  * **Data summary**
    * Total: R=272563	W=139937
    * % pages with compress ratio <= 50%: 72
    * No. of pages with compress length >= 4096: 83

(compression algo: LZO1X)