---
title     : "Syncing using rclone"
date  : 2020-08-23
tags  :
  - rclone
  - dropbox
  - google-drive
  - one-drive
categories :
  - syncronization
---

I have been a Dropbox user for over a decade. Part of the attraction for me
was that it is one of the few synchronization services that fully supports
Linux. I had it installed on two servers, my office desktop, my laptop, and
most of my mobile devices. Couple of years ago, my employer moved most of its
IT services with Microsoft 365, which gives me access to storage space on
Microsoft OneDrive as well. Plus, I have purchased additional storage on
Google (for backing up my personal photos). But I never seriously used
Microsoft OneDrive or Google Drive for synchronization because all my computers
run Linux and neither of these services had a Linux client.

<!--more-->

Now with the pandemic, I am involved in more remote collaborations and some of
my collaborators prefer Google Drive and some administrative projects are
managed on Microsoft Teams and use the shared files on Microsoft Teams. At
first, I was simply viewing these files using a browser but after a while
manually synchronizing files by uploading using a browser became annoying. So,
I began to search for ways in which I could synchronize with these services on
Linux.

Enter [rclone](https://rclone.org/), which markets itself as _"The Swiss army
knife of cloud storage"_. Rclone can sync with Microsoft OneDrive, Google
Drive, and more than 40 other cloud storage products! 

So far I am only doing manual syncing. First, create a new "remote" using
`rclone config` and then add a cron-job which periodically runs:

    rclone copy REMOTE:folder local-folder
    rclone copy local-folder REMOTE:folder 

So far, I have used this on projects where the files change once a week and
there is no risk of conflicts etc., and it has worked flawlessly. Rclone
provides an option to [mount a cloud storage service
locally](https://rclone.org/commands/rclone_mount/) and I plan to explore that
if/when I need to work on a project which is updated more frequently.


