---
title     : "Testing out qutebrowser"
date  : 2021-12-21
tags  :
  - qutebrowser
  - xdg-settings
categories :
  - web-browser
---

I have been cycling between web browsers for the last few years. I used to use
Opera, but then it became a shell for Chrome, then I started using Chromium,
but occasonally got huge memory consumption, then I started using Firefox, but
lately it has become very laggy (to the extent that even scrolling was slow).
So, after testing a bunch of less commonly used browsers, I have finally
settled on [qutebrowser](https://qutebrowser.org/).

<!--more-->

qutebrowser is a keyborad focused browser (which is like a cheery on top for
me), but what I realy like is the fact that is lightweight. I have been using
it for a week, and everything seems to work correctly. I'll collect my tips
and tricks for customizing qutebrowser here. 

### Setting as default browser

Suprisingly, setting qutebrowser as the default browser was a bit unusal. 
The usual:

```
xdg-settings set default-web-browser qutebrowser.desktop
```

did not work. A quick test showed that

```
$pacman -Ql qutebrowser| grep desktop
qutebrowser /usr/share/applications/org.qutebrowser.qutebrowser.desktop
```

So, the desktop entry is called `org.qutebrowser.qutebrowser.desktop`! Once I
knew this, setting qutebrowser as a default browser was easy:

```
xdg-settings set default-web-browser org.qutebrowser.qutebrowser.desktop
```
