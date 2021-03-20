---
title     : "Backup up data"
date  : 2021-03-20
tags  :
  - rsync.net
  - unison
  - dropbox
  - cron
categories :
  - backup
---

For about 10 years now, I have been using Dropbox as a tool to synchronize data
between my work desktop and my laptop. I do a local backup using
[`rsnapshot`][rsnapshot], but they were only stored locally on my desktop. So,
effectively, I had been relying on Dropbox as a backup solution. Every so
often, I read horror stories that [Dropbox is not a backup solution][google].
And every time I would read one of these reports, I would think, "Hmm... maybe
I should start using a proper backup solution." I finally bit the bullet and
purchased some storage from [rsync.net][rsync] and set up a back up
system. Yay!


<!--more-->

I chose [rsync.net][rsync] because they have a completely no nonsense website
(no marketing gimmicks and an honest technical description of what they
provide). And I have read many blogs and posts where people recommend
[rsync.net][rsync], and I have never read a negative review.

My current synchronization setup is as follows. There are two directories that
I sync between my desktop and laptop: `Projects` (which contains my active
projects) and `backup` (which contains zipped copies of my old projects). 
The `Projects` directory is sym-linked to `Dropbox` directory, and key in sync
via Dropbox. The `backup` directory is kept in sync via `unison` (which I find
to be faster than raw `rsync`) and `cron`. 

It was relatively easy to add [rsync.net][rsync] as another synchronization
host. To do that, I created a file `$HOME/.unison/rsync.prf` on my desktop:

```
root = /home/username
root = ssh://user@user.rsync.net

force = /home/username

path = backup
path = Dropbox

log = true
logfile = .unison/rsync.log

batch = true

ignore = Path Dropbox/.dropbox
ignore = Path Dropbox/.dropbox.cache
```

Most of the preferences are pretty self explanatory. The only non-obvious line
is `force = /home/username`, which tells `unison` that in case of a file
conflict, overwrite the file from my desktop to `rsync.net`. This is slightly
risky, but rsync.net provides daily snapshots for 7 days, so I should be able
to recover from accidental deletions. I have only had the account for less
than 24hrs, so the automatic snapshot hasn't kicked in yet. I'll see how it
looks; otherwise, I'll also backup the `rsnapshot` snapshots to `rsync.net`. 

Now, one difficulty was that my desktop is running Arch Linux. So I had the
latest version of `unison` (version 2.51.2), while `rsync.net` is running
version 2.40.63. And `unison` refuses to sync with different versions of
`unison` on the client and the server. 

Fortunately, there is a package on [AUR][AUR] (isn't that always the case),
which installs the pre-built binary from debian. Fortunately, [debian's
repository][debian], had version 2.40.102 of `unison`, so I could easily
install that using the AUR package (which installs the binary as `unison-2.40`
to avoid any clashes with the default version). 

Now, I needed to do was run

```
$unison-2.40 rsync
```

to sync my desktop to rsync.net. I added the following to my `crontab -e`
file:

```
5 */6 * * *   unison-2.40 rsync 
```
which runs `unison` every six hours (at 5 minutes past the hour).

And Voilà! Now all my work projects are backup up on `rsync.net`. That was not
too difficult to set up. 

Now all I need to do is to figure out a backup strategy for my photos and
videos which are currently all on Google Photos. 

[rsync]: http://rsync.net
[rsnapshot]: https://rsnapshot.org
[google]: https://duckduckgo.com/?q=dropbox+is+not+a+backup+solution
[AUR]: https://aur.archlinux.org/packages/unison-2.48.4-compat-bin/
[debian]: http://ftp.de.debian.org/debian/pool/main/u/unison/


