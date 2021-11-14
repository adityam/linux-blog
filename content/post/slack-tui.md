---
title     : "Using Slack from the terminal"
date  : 2021-11-13
tags  :
  - slack
  - slack-term
categories :
  - Terminal
---

In rencent years, lot of my short conversations with my research lab has moved
to Slack. I am not a big fan of closed protocol communication clients, but
Slack has the advantage that it is relatively easy to use. Well, it seems that
it relatively easy to use for everyone except me. 

<!--more-->

I started with the Slack desktop client, which was a resource hog. So, I
switched to using Slack in the browser. But for the last week or so, Slack on
the browser (Firefox) was becoming extremely unresponsive. So, I started
looking at alternatives.

I tried a few different clients but was not impressed by any of the
alternative GUIs. So, I started testing TUIs as well. There were two which
seem to be reasonably well maintained:

* [wee-slack](https://github.com/wee-slack/wee-slack)
* [slack-term](https://github.com/erroneousboat/slack-term)

I could not get `wee-slack` to work. The instructions were asking to install
stuff in `~/.weechat/...` but that did not work for me. `slack-term` looks
decent and works well (except, as expected, it doesn't show attachments;
though I wonder if it is possible to do something similar to `w3m` to display
images inline. However, there was one show stopper: all the timestamps were in
UTC!

It turns out that by default, docker images show time in UTC (rather, if you
don't set time zone on a linux machine, it assumes that the time zone is UTC).
After a bit of duckduckgo-ing, I figured that I needed to add `tzdata` package
to the docker file, and then pass the `TZ` argument to set the time-zone. So,
I cloned the repo of slack-term and added the following patch:

```
diff --git a/Dockerfile b/Dockerfile
index 4139c4a..0cc8d99 100644
--- a/Dockerfile
+++ b/Dockerfile
@@ -4,7 +4,8 @@ ENV PATH /go/bin:/usr/local/go/bin:$PATH
 ENV GOPATH /go
 
 RUN    apk add --no-cache \
-   ca-certificates
+ ca-certificates \
+    tzdata
 
 COPY . /go/src/github.com/erroneousboat/slack-term
 
@@ -26,6 +27,7 @@ FROM alpine:latest
 ENV USER root
 
 COPY --from=builder /usr/bin/slack-term /usr/bin/slack-term
+COPY --from=builder /usr/share/zoneinfo /usr/share/zoneinfo
 COPY --from=builder /etc/ssl/certs/ /etc/ssl/certs
 
 ENTRYPOINT stty cols 25 && slack-term -config config
```

Then I built the image using:

    docket build . -t local/slack-term

And added the following aliases in my `~/.zshrc`:

```
alias slack-org1="docker run -it -v /home/username/.config/slack-term/org1:/config -e TZ='America/New_York' local/slack-term"                      
alias slack-org2="docker run -it -v /home/username/.config/slack-term/org2:/config -e TZ='America/New_York' local/slack-term"
```

where `org1` and `org2` are two slack organizations that I am a part of. Now,
the timezone is set correctly in the Docker image and `slack-term` shows the
correct timestamps.

Now, back to actual work...
