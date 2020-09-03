---
title     : "Back to ZSH"
date  : 2020-09-03T17:53:52-04:00
tags  :
  - zsh
  - fist
categories :
  - Terminal
---

As I mentioned in [a previous post][fish], I started using fish after using
zsh for about 15 years. After working exclusively on fish for about 3 months,
I am now back to zsh. 

In the end, there was nothing inherently missing in fish, but fish just did a
few minor things differently which began to get annoying.

* Fish's tab-completion first shows a short list of option (top 10 or so) and
  you have to press tab twice to see the whole list. I found that I was almost
  always pressing tab twice. 

* By default, while doing tab complete on directories, fish includes the
  directories in my `CDPATH`. In principle, this is a good thing, but these
  directories are shown _before_ the directories in the current working
  directory. Combined with the previous comment, this meant that I had to hunt
  around for the directory I wanted to cd to.

* The trick of ignoring certain suffixes using `fd` that I had listed in my
  previous post was not robust enough. I could get something like `nvim
  ~/.config/<tab>` to work reliably. So, after a couple of weeks, I just
  commented out the `fd` stuff and then I had to hunt for the right file
  extension while doing tab completion. 

* I kinda missed `sudo !!`, etc. The `Alt + Arrow` commands were simply not as
  powerful.

So, in the end, although fish has an overall cleaner design, 
my muscle memory is too much tied to zsh. So, back to the old ... 

[fish]: https://adityam.github.io/linux-blog/post/trying-fish/
