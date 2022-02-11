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
## Update: No longer using qutebrowser

Qutebrowser does not support the standard Chrome/Firefox extensions. For the
most part, I can live without extensions, but one that I really need is Zotero
integration. Quite often I'll come across an interesting paper, but don't have
time to read it. I save (and categorize) such papers right away using Zotero,
so that I can read them at a later date. 

There is a [user script for
zotero](https://github.com/parchd-1/qutebrowser-zotero), which basically
parses the current URL, extracts the PDF, and passes it to Zotero. However,
most of the papers that I read are behind a paywall, so the script cannot
download the PDFs. So, I have to resort to "download paper -> manually import
to Zotero" which is too time consuming. 

I am still using Zotero for one off tasks (like previewing output for a
Hugo-generated website), but it is no longer my daily driver. 
