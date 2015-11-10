# Introduction #

Help on using compcache kernel patches. If you are using out-of-tree compcache modules then refer CompilingAndUsing

# Download #

Goto download area: http://code.google.com/p/compcache/downloads/list

All Changelogs are [here](http://code.google.com/p/compcache/wiki/Changelog).


# How to use #

  * Apply patch for corresponding kernel version.
  * In kernel config:
    * Enable: Device Drivers -> Block Devices -> Compressed RAM based swap device -> {debug, stats}
    * Once above is selected, TLSF options will be visible: General setup -> TLSF Allocator -> {debug, stats}

# FAQ #
  * How are these patches different from modules available in Downloads section?
    * These patches are integrated with kernel build system and have additional hooks in swap path to make it more efficient. However, applying patches and recompiling kernel is cumbersome. So, these are also made available as separate modules to avoid all this.
    * Latest patch is equivalent to compcache-0.4 with added notify callback to free memory associated with free swap slots. Similar functionality is now also available in compcache-0.5pre4.
    * This integration was started to prepare this for mainline.