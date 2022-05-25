---
title     : "Changing terminal and browser"
date  : 2022-05-24
tags  :
  - xterm
  - kitty
  - links
  - w3m
categories :
  - CLI
---

I spend a lot of time on the terminal: using alpine to send emails and using
vim/neovim to edit tex files and code. For at least a decade if not longer, I
have been using UXTerm as my terminal emulator. At some stage I spent some
time configuring UXTerm fonts and color schemes to what I like, and I have
been using it ever since. Recently, I started experiencing some weird issues
with UXTerm, which forced me down a rabbit hole.

I did not use my office workstation for almost a year and a half due to covid.
When I returned back, I did a system update, and started working. But every so
often (once an hour or so), I started seeing weird flickering of the terminal.
This was particularly common when I was composing emails (neovim open inside
alpine). Most of the times, this scrolling stopped on pressing `CTRL-L` but it
was annoying to say the least. I tried searching online for what was
happening, but could not find a solution that worked. Surprisingly, my laptop,
which has the same distro and the same setup never had an isse.

Anyways, I thought that it was time to try out a new terminal emulator. After
reading a bit about the different options, [this post][notcurses] of the
notcurses author convinced me to test [kitty][kitty]
first. After spending half an hour going over the documentation, I could
configure everything (fonts, colorscheme, copy and paste behavior) as I liked.
I have been using it on my office workstation for 3-4 hours and no more
flickering.

[kitty]: https://sw.kovidgoyal.net/kitty/
[notcurses]: https://www.reddit.com/r/archlinux/comments/n9noje/alacritty_vs_kitty/gxqne6n

The story would have ended with "and they lived happily ever after", until I
noticed that images where not displayed when viewing HTML emails in alpine. I
am sure you are screaming, "What!". So, let me take a step back and explain.

I use alpine (which is a text based email program) for reading emails.
Occasionally, I get an email which has some images or HTML formatting (such as
tables), which doesn't display nicely in alpine. In such cases, I simply do
'V' + 'Down-Arrow' + 'Enter' to display the `text/html` portion of the email.
I have configured my `mailcap` file to display `text/html` using `w3m`, which
is a text-based browser which can display inline images in the terminal. The
inline image display of `w3m` was not working on kitty. 

Back to DuckDuckGo. There was [an issue][issue] on kitty's github page where
the author essentially said that w3m uses a hack to display images in the
terminal and he is not going to support that hack. So, time to see if there
are any other text-based browsers that are still maintained and support inline
images. 

In the past I had used [lynx][lynx], but that doesn't support inline images.
[elinks][elinks] hasn't been updated in almost a decade, but [links][links] is
actively maintained and supports inline images!

So, I installed `links` using `pacman -S links` and tried to open a website
with images:

```
$links -g <website>
Graphics not enabled when compiling
```

Hmm..., so I downloaded the `PKGBUILD` of links using

```
asp checkout links
```

and realized that `links` [PKGBUILD does something unusual][github]. It first compiles
the package with graphics support, renames the executable as `xlinks` and then
recompiles without graphics support. So, all I needed to do was use `xlinks
-g` in my `mailcap`. But ... that opens a new X window to display the results!
So, effectively `xlinks -g` is a lightweight browser. 

The interesting thing is that I did not notice that `links -g` is a graphical
browser rather than a text browser until I wrote this blog post. The reason is
that I use a tiling windows manager (i3m) and normally the workspace where I
am running alpine is two columns of windows: one with email and another
terminal and the other with slack and a few other terminal. Both columns are
in stacked mode. So, when `xlinks -g` opened a new X window, it completely
covered alpine and I thought that the content was being displayed as part of
alpine. 

Now, the reason that I pipe email messages to a text-based browser rather than
firefox is to keep distractions in check. Typically, with text based browsers,
I just read the message and don't start browsing the web. I am going to see if
I can maintain the same distraction free habit with `xlinks`. If not, then
I'll be back to square one.


[issue]: https://github.com/kovidgoyal/kitty/issues/851
[lynx]: https://lynx.invisible-island.net/
[elinks]: http://www.elinks.cz/
[links]: http://links.twibright.com/
[github]: https://github.com/archlinux/svntogit-packages/blob/f86750a7ef66c071873cb580de45d03b9fd13cac/trunk/PKGBUILD#L35-L50
