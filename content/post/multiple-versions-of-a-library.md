---
title     : "Installing and using multiple versions of a library"
linktitle : "Multiple versions of a library"
date  : 2020-05-13T13:07:31-04:00
categories :
  - OS
tags  :
  - Arch Linux
  - SNAFU
  - pacman
  - downgrage
  - yst
---

I use a Haskell static website generator called [yst][yst] for generating my
homepage. Why? Partly because when I first created my professional webpage, I
was a Haskell enthusiast and [yst][yst] seemed cool. After a while, I got
frustrated with Haskell (I use Arch Linux, and it seemed that each upgrade
would break Haskell install) and moved on. I haven't used Haskell for a while
now. 

[yst]: https://github.com/jgm/yst

<!--more-->

But, I had a critical resource, my webpage, which depended on a Haskell
library. I thought that Haskell generated statically linked binaries, so I
simply copied the `yst` binary and kept it in my `PATH`. This worked for
almost 8 years during which I upgraded 3 systems (all using Arch Linux). Until
today, when `yst` stopped working with the following error message:

```
yst: error while loading shared libraries: libffi.so.6: cannot open shared object file: No such file or directory
```

Darn! It turns out that I had a dynamically linked binary rather than a
statically linked library. 

```
$ldd =yst
        linux-vdso.so.1 (0x00007ffc5696c000)
        libz.so.1 => /usr/lib/libz.so.1 (0x00007f30268c3000)
        librt.so.1 => /usr/lib/librt.so.1 (0x00007f30268b8000)
        libdl.so.2 => /usr/lib/libdl.so.2 (0x00007f30268b3000)
        libpthread.so.0 => /usr/lib/libpthread.so.0 (0x00007f3026891000)
        libsqlite3.so.0 => /usr/lib/libsqlite3.so.0 (0x00007f3026760000)
        libgmp.so.10 => /usr/lib/libgmp.so.10 (0x00007f30266bd000)
        libm.so.6 => /usr/lib/libm.so.6 (0x00007f3026577000)
        libffi.so.6 => not found
        libc.so.6 => /usr/lib/libc.so.6 (0x00007f30263b1000)
        /lib64/ld-linux-x86-64.so.2 => /usr/lib64/ld-linux-x86-64.so.2 (0x00007f3026921000)
```

So, I tried to install `yst` using a current version of Haskell. Both `cabal
install yst` and `stack install yst` failed. Basically, the only way to
satisfy all the constraints for building `yst` is to use a lower version of
the `base` library. As the error message of `yst` so succinctly put it:

> * Build requires unattainable version of base. Since base is a part of GHC, you
>   most likely need to use a different GHC version with the matching base.

I could have opened a bug request for `yst` but thought that a simpler fix
will be to simply download the older version of `libffi`. 

So, I installed the `downgrade` package from AUR and ran

```
$downgrade libffi
Available packages:

-  1)  libffi    3.2.1  3  x86_64  (remote)
-  2)  libffi    3.2.1  4  x86_64  (remote)
-  3)  libffi    3.2.1  4  x86_64  (remote)
   4)  libffi    3.3    1  x86_64  (remote)
   5)  libffi    3.3    2  x86_64  (remote)
+  6)  libffi    3.3    3  x86_64  (remote)
+  7)  libffi    3.3    3  x86_64  (local)

select a package by number:
```

I selected `3`, which is the latest version of libffi 3.2.1 and installed it.
Now, `yst` was working, but almost all other programs were not. So, I copied

```
/usr/lib/libffi.so.6
```

to `$HOME/lib` and upgraded `libffi` (using the `downgrade` program again).
Now, I was back to square one, but I had the required version of `libffi`
needed to run `yst`. So, instead of running `yst` directly, I can run

```
LD_LIBRARY_PATH=$(HOME)/lib/ yst
```

which uses the old version of libffi for yst, and the current version of
libffi for everything else. Phew! 

This also reminds me that at some stage, I should move my professional webpage
to a more modern static website generator, like Hugo used for this blog. Another project for a rainy day.
