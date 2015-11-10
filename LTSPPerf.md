# Introduction #

**Contributed by: Nai Xia**

The Linux Terminal Server Project adds thin-client support to Linux servers.
LTSP is a flexible, cost effective solution that is empowering schools,
businesses, and organizations all over the world to easily install and deploy
desktop workstations. A growing number of Linux distributions include LTSP
out-of-the-box.

The Compcache creates RAM based block device (named ramzswap) which acts as
swap disk. Pages swapped to this disk are compressed and stored in memory itself.
Compressing pages and keeping them in RAM virtually increases its capacity. This
allows more applications to fit in given amount of memory. This is especially
suitable for LTSP since they both focus on limited resource PCs.

Many of the success stories of LTSP are solutions deployed at schools,
institutes, libraries, etc. Two mostly required features in these
environments, is the reading of the Adobe Portable Document Format and browsing
of web pages. So our experiment is based on a very popular .pdf reading software
(kpdf) and the firefox browser in a typical LTSP setup. The results show compcache
can bring significant improvements to these kinds of application.

# Details #

2. **Environment**

  * Hardware:
> > Server: Pentium 4 2.0GHZ, IDE disk 7200rpm, 1G RAM.
> > ThinClient: Old T42 IBM thinkpad, Pentium M 1.6GHZ, 124M RAM
> > > (limited with kernel cmdline, to emulate a legacy PC)

> > network:  1Gigabit network

  * Software:
> > Server host: Debian sid,updated at April,23th
> > > LTSP(ltsp-server-standalone) + DHCP + NFS rootfs

> > Thin client: Debian etch with KDE, diskless( without swap )




3. **Experiments**

A group of pdf files were randomly selected, each of them is a technical paper and of
similar size (1.1M~1.2M).15 web pages were selected, each of them is composed of text and
pictures(without flash).We symbolically named these files(web pages) as A~Z for ease of
presentation. The experiments are to evaluate the user experience
reading these papers and web pages with V.S. without compcache loaded.


3.1 **Desktop interactivity**



> a) kpdf responsiveness

When reading a pdf file with kpdf, if the system is under pressure (CPU or
memory), a user can feel obvious slowdown in paging down and paging up in a
document. Thus we define the pdf reading responsiveness as the time(seconds) for paging from
the 1st page to the 50th page (go back and forth to take the mean value) in the same
pdf file.

After the thin client booted up, the KDE was started and two pdf files(A.pdf,
B.pdf) were loaded, which ate most of the 124M RAM. We tested the value and closed
these two pdf files. Then we loaded the compcache with swapsize of 64M and did
the same test again. The results are as follows:
```
               original          with compcache(64M)

A.pdf           10s                     2.5s

B.pdf           412s                    4s
```

Just as we can expect, without compcache, the system slowed down significantly when
approaching the memory up limit, which actually makes the system unusable if one wants
to read the two pdf files side by side. While with the help of compcache, it
quite smooth to read these two pdf file concurrently.


> b) firefox responsiveness

Without compcache, firefox could successfully load on tabs 6 web pages. After
the 6th web page was loaded, significant slow down was observed. It took more
than 12 seconds to shift from one tab to another. Even within one tab, scrolling
is sluggish.

After compcache was loaded, browsing the 6 web pages was swift just as if there
is no memory pressure apposed.


> c) Desktop responsiveness

Similar slowdown can be observed in upper case if one wants to switch between
the two kpdf programs from time to time. We believe it is related to the X
window system and the window manager.We call the average swith time between two
programs "the desktop responsiveness". It's also important, for if someone is
reading one book and take another as a reference, low desktop responsiveness
will soon bored him out.

In the same experiment as in a), we observed the desktop responsiveness in the original
ltsp client was 10 seconds,while after the compcache was loaded, the swith time was
hardly observable.


> d) Memory behavior analysis

Detailed memory behavior data  was collected when doing the experiment in a) and
it's shown in Figure1. The first pdf file included 46 pages,
and the first 51 pages of the 2nd pdf file were loaded page by page. The total
pdf page footrpint includes 97 pages. The lines postfixed by "noswp" is the data
when compcache was not loaded. The "compcache\_size" line represents the RAM size
used by compcache to store compressed pages.


![http://compcache.googlecode.com/svn/wiki/files/perf/ltsp/two_pdfs.gif](http://compcache.googlecode.com/svn/wiki/files/perf/ltsp/two_pdfs.gif)

**Figure 1**

We can see from the picture that when compcache is loaded, the anonymous,
cached, and free pages are much "smoother" and more "stable". While in the
opposite case, the cached pages was pushed by anonymous pages and so decreased
very rapidly, which is especially not what we want when in a networked root
file system. The free RAM size also trembled baddly when compcache was not
loaded, which maybe a good sign of trashing.

The data for firefox was shown by Figure2. Remember that with
compcache, the decreased anonymous pages were still in RAM, only in compressed form.
The cost to expand them and reused by firefox is rather low. While the increased file
cache pages would otherwise require I/O from NFS.

![http://compcache.googlecode.com/svn/wiki/files/perf/ltsp/6sites.gif](http://compcache.googlecode.com/svn/wiki/files/perf/ltsp/6sites.gif)

**Figure 2**

3.2 **Load capacity**

> a) How many pdf files?

Man is a lazy animal, if one opens a pdf file and feel he MAY take a look at it later
on, he trends to keep it open for hours :). So the number of pdf files we can
keep open in our system is never large enough. We here show how compcache can
improve on this situation.

As we have seen, the original system can successfully load two pdf files, but
not more, further loading triggered the OOM killer. That means the loading
capacity of the original is 2. The compcache can be installed as different swap
size, so we ranged the swap size from 32768kbytes to 196608kbytes to see the
relation between swap size and load capacity.

```
swap size(kbytes)               load capacity(num. of pdf files)
(none)                                  2
32768                                   4
65536                                   6
98304                                   9
131072                                  10
163840                                  11
196608                                  11
```

We can see that in the best case, compcache increased the load capacity by 450%.


> b) How many web pages?

If compcache was loaded with size of 163840KB, firefox could easily load up to
15 web pages. Similar to the case of kpdf, compcache again greatly improved the
capacity of firefox.


> c) Memory behavior analysis

Detailed memory behavior data was also collected to give a description of how
compcache functioned when loading large number of pdf pages just before the OOM
killer was triggered.

The data is plotted in Figure3, each odd-numbered "loading step"
means "opening a new pdf file", and each even-numbered step is "browsing the first
50 pages of the newly opened file". The first 10 pdf files were loaded and
browsed with ease. Browsing of the 11th pdf file triggered the OOM killer. So 20
steps are shown. The "zswpmem" line is the memory used by compcache to store the
compressed page.


![http://compcache.googlecode.com/svn/wiki/files/perf/ltsp/10files.gif](http://compcache.googlecode.com/svn/wiki/files/perf/ltsp/10files.gif)

**Figure 3**

We can see from the figure, for all the steps, the "zswpmem" size is always
smaller than the half size of overall swapped pages. That means the compcache
compression ratio is very good for this application. While the most anonymous
pages were stored compressed at low CPU&RAM cost, the file cache decreased in a
reasonable speed and the free memory pages were in a very stable state.
This is an impression that the VM was still in good shape and can still respond
in a graceful manner --- just what we felt in the real world.

Data for firefox is shown in Figure4.

![http://compcache.googlecode.com/svn/wiki/files/perf/ltsp/15sites.gif](http://compcache.googlecode.com/svn/wiki/files/perf/ltsp/15sites.gif)

**Figure 4**

5. **Conclusion**

LTSP is a great invention for large number of legacy or resource-limited PCs,
however, users may suffer from the limited memory using these systems when
the up limit is approached. More money can be spent adding on more RAM to
improve the user experience, the feature-rich KDE desktop can be cutted off to
lend more memory to other programs. But, with compcache, who bothers ! :)