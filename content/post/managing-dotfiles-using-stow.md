---
title     : "Managing dotfiles using stow"
date  : 2023-01-14
tags  :
  - stow
  - dotfile
categories :
  - OS
---

One of my new year's resolutions was to come up with a sensible way to manage
my dotfiles. I evaluated a few solutions and finally settled on [GNU
stow][stow].

[stow]: https://www.gnu.org/software/stow/

<!--more-->

GNU stow is a symlink manager, normally used to manage symlinks to programs
installed in `/usr/local/bin`. However, it can also be used to manage symlinks
to configuration files. After a bit of trial and error, I settled to the
following.

1. Create a dirctory `~/Dropbox/dotfiles` to store all the dotfiles.
2. For each package, create a subdictory `package`. 
3. Typically, the configuration files of a package are either stored in 
   `~/.packagerc` or `~/.config/package`. Let's use `zsh` and `kitty` as
   canoncial examples of the two cases.
4. For `zsh`, create a file `~/Dropbox/dotfiles/zsh/dot-zshrc` which has the
   contents of the desired `.zshrc`.
5. For `kitty`, create a file `~/Dropbox/dotfiles/dot-config/kitty/kitty.conf`
   with the desired contents of `~/.config/kitty.conf`
6. Note that in both cases, we have replaced `.filename` or `.dirname` by
   `dot-filename` and `dot-dirname`.

Then run the command

```
stow --target $HOME --dotfiles --verbose package
```

for each package, which creates the appropriate symlinks. That's it!

Well almost. There is currently a [bug] in stow, due to which `--dotfiles`
option does not work with directories. Fortunately, there is an AUR package
`show-dotfiles-git` which provides a fix, at least until the fix is merged
upstream. 

[bug]: https://github.com/aspiers/stow/issues/33

It can be tedious to link each package one by one. If so, we can stow all
packages at once using

```
find . -mindepth 1 -maxdepth 1 -typed -printf '%f ' | xargs stow --target $HOME --dotfiles --verbose
```


