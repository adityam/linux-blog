---
title     : "Local config for kitty"
date  : 2022-06-01
tags  :
  - kitty
  - configuration
categories :
  - CLI
---

As I mentioned in the last [post](../changing-terminal-and-browser), I am
testing out [kitty][kitty] as my terminal emulator instead of UXTerm. So for
everything has been smooth sailing except for one minor issue. On my work
desktop, I want fontsize 11 but on my laptop, I want fontsize 14. This is a combination of resolution and physical size of the two monitors. Reading the documentation of kitty.conf, it was not clear how to set conditional values for the variables.

On a whim, I tried to see if kitty.conf can read environmental variables and
it can! So I created two config files, `desktop-hostname.conf` and
`laptop-hostname.conf`, where the names correspond to the value of `$HOST` on
the two systems. Then, I end my `kitty.conf` with

```
include ./$HOST.conf
```

I can now add the settings for my desktop in the `desktop-hostname.conf` file
and the settings for my laptop on `laptop-hostname.conf` file. 


[kitty]: https://sw.kovidgoyal.net/kitty/
