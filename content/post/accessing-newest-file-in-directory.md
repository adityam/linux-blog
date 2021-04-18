---
title     : "Accessing newest file in a directory"
date  : 2021-04-18
tags  :
  - zsh
  - glob qualifiers
  - zshexpn
categories :
  - Terminal
---

There are many instances where I want to access the most recent file in a
directory:

<!--more-->

* By default, my browser saves PDF files (and other downloaded files, for that
  matter) in `~/Downloads`. Rather than navigating to a more appropriate
  directory via the GUI, I find it more convenient to `cd` to the appropriate
  directory on the command line (where [zoxide][z] is an amazing tool to jump
  to commonly used directories) and simply move the file there.

* I use [a browser plugin][dotepub] to convert long web articles to `.epub` so
  that I can read them later on my eInk tablet. This plugin saves files in
  `~/Downloads` and has no option to change the downloads directory.

* I run a `cron` script that downloads and packages the latest version of
  ConTeXt by running `makepkg` in a directory containing the `PKGBUILD` file
  for [`luametatex`][luametatex]. However, I don't automatically upgrade the
  package. I always prefer to upgrade packages manually when I know that I
  have the time to fix the upgrade if something breaks (happens rarely, but I
  don't want something to break half an hour before a deadline). When
  updating, I want to find the latest `luametatex-date.pkg.tar.gz` file in the
  directory.

I recently discovered [glob qualifiers][glob], which is perfect for these
tasks. In the shell `*` expands to all files and directories. Zsh has options
to qualify which files needs to be expanded. In particular:

* `*(.)` expands all files (i.e., excludes directories)
* `*(.om)` expands all files and sorts them descending order according to
  modification time
* `*(.om[1])` selects the first such file.

Thus, for my first two use cases, I can use:

    z desired-directory && mv ~/Downloads/*(.om[1]) ./

For the third use case:

    z luametatex && sudo pamcan -U *(.om[1])

Sometimes, I want to use the latest file matching a particular pattern (say,
the latest PDF file with `pattern` in the title). In such cases, I can use:

    mv ~/Downloads/*pattern*.pdf(om[1]) ./

Or, if I want to see the name of the file before moving it:

    mv ~/Downloads/*pattern*.pdf^Xm

(where `^Xm` means press `CTRL+X m`). Search for `_most_recent_file` in
[zshcompsys][comp] for details.

[z]: https://github.com/ajeetdsouza/zoxide
[dotepub]: https://dotepub.com
[luametatex]: https://aur.archlinux.org/packages/luametatex/
[glob]: https://man.archlinux.org/man/extra/zsh/zshexpn.1.en#Glob_Qualifiers
[comp]: https://man.archlinux.org/man/extra/zsh/zshcompsys.1.en
