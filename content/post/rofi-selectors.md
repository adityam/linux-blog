---
title: "Rofi Selectors"
date: 2020-05-09T00:00:51-04:00
images: 
categories: 
  - efficiency
tags: 
  - rofi
  - heredoc
  - awk
  - xargs
  - xclip
---

[rofi][rofi] is perfect for creating interactive scripts, which I like to call _rofi selectors_.  Here are some examples:

<!--more-->

[rofi]: https://github.com/davatorium/rofi
[dmenu-extended]: https://github.com/MarkHedleyJones/dmenu-extended

## Connecting to bluetooth devices

```
cat <<DEVICES | rofi -width 30 -dmenu -i -p "Devices"  \
              | awk '{printf $1}' \
              | xargs bluetoothctl connect \
# List of devices to connect
00:16:94:2F:AC:18 Sennheiser Headphone
01:16:94:2F:AC:18 Anker Headphone
DEVICES
```

First `rofi` displays the MAC addresses and names of the bluetooth headphones
that I use, then `awk` parses the appropriate field (the MAC address in this
case), and `xargs` call the appropriate command (`bluetoothctl` in this case)
to connect to that headphone. I use [heredocs][heredocs]  so that I can add
the list of devices at the end of the command. Note the trick of adding a `\`
after the last command so that the comment `# List of devices` is parsed as
part of the command rather than part of the heredoc. 

[heredocs]: https://linuxhint.com/bash-heredoc-tutorial/

I have stored this file as `~/bin/bt-connect`. To use it for the first time, I
call `rofi -show run` (which is mapped to `MOD+p` in my setup) and type
`/home/username/bin/bt-connect`. From then onwards, `rofi` remembers the
previously typed command and I can simply do `MDO+p` and then type
`bt-connect`, and select the appropriate headphone. No more fiddling around
with `blueman-applet`!

## Accessing frequently used information

I often have to fill forms where I need to know the project ids for the
different projects that I am working on. So, I have created a command
`~/bin/project-ids` as follows

```
cat <<PROJECTS | rofi -width 20 -dmenu -i -p "Project"  \
               | awk '{printf $1}' \
               | xclip -selection clipboard \
# List of Projects
123456 Project 1
123456 Project 2
123456 Project 3
PROJECTS
```

The basic structure of the command is the same as before. Display the list of
options (project-id and project-name) using `rofi`, parse the appropriate
field using `awk`, but in this case, I copy the parsed field to the clipboard.
This way I can simply paste it in the form where I need it.

## Parsing dynamic information

Again, I need to fill forms with student ids. I have an XML file which
contains details of students, including student ids. It is easy to use `rofi`
selectors for this situation. I have written a script (written in
[crystal][crystal], but it can be written in your favorite language), which
simply outputs the list of student-ids and names, and then I pass the output
to `rofi`.

```
/path-to-script/student-id
    | rofi -width 40 -dmenu -i -p "Name"  \
    | awk '{printf $1}' \
    | xclip -selection clipboard
```

[crystal]: https://crystal-lang.org/

## Copying passwords

I use [pass][pass] as a password manager. `pass` stores the passwords as
encrypted files insider `~/.password-store`. The next selector reads the
website/usernames for which a password is available, selects the username
using `rofi` and passes the result to `pass -c`, which copies the password to
clipboard for 45 sec.

[pass]: https://www.passwordstore.org/

```
cd $HOME/.password-store && fd -t f  \
    | sed 's/\.gpg$//'  \
    | rofi -width 30 -dmenu -i -p "pass" \
    | xargs pass -c 
```
