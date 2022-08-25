---
title     : "Signing PDFs via command line"
date  : 2022-08-25
tags  :
  - mypdfsigner
  - foxitreader
  - PDF
categories :
  - CLI
---

Signing PDFs in Linux is always a PITA. Part of the reason is that Adobe
stopped the development of a linux port of Adobe Reader since adobe reader v9.
Some of the newer PDFs require features which are newer than acrobat v9 and
cannot be opened or signed with acrobat v9. 

In this page, I summarize some of the options for signing PDF that have worked
for me.
<!--more-->

## Adobe Acrobat v9

Adobe Acrobat is still my first choice for signing of PDFs. When it works, it
works well. But, as I mentioned above, it does not work in all cases.

## FoxitReader

I have purchased CodeWeavers Crossover (which is a commercially maintained
port of Wine) to be able to run Windows program. I tried installing modern
version of Acrobat Reader using Crossover, but those attempts have been
unfruitful. However, foxitreader does install well using crossover and works
really well for placing a visual signature on the PDF. I haven't managed to
get the actual cryptographical signing to work, but in my office that is
rarely needed.  The only downside is that foxitreader refuses to sign certain
PDFs, so this method does not always work in all cases.

## mypdfsigner

[mypdfsigner](https://www.kryptokoder.com/home.html) is a CLI tool (and a
language library for PHP, Ruby, and Python) for signing PDF documents. I
installed it using [snap](https://snapcraft.io/):

```
sudo snap install mypdfsigner
```

Although it has a [decent
documentation](https://www.kryptokoder.com/manual.html), it took a while to
figure out how to run it. It needs a config file, which looks as follows:

```
#MyPDFSigner configuration file
certfile=/home/path-to-p12-file
certpasswd=<encrypted password>
certstore=PKCS12 KEYSTORE FILE
sigpage=-1
sigrect=[-220 -240 -90 -220]
sigimage=/home/path-to-sig-file.png
tsaurl=http://adobe-timestamp.geotrust.com/tsa
subfilter=ETSI.CAdES.detached
```

Here `certfile` is a `.p12` file that I had created using Adobe Acrobat (on
Windows). The `certpasswd` is the encryped password for this file generated
using

```
snap run mypdfsigner -e <password>
```
where `password` is the password for reading `.p12` file. My password
contained special characters which was causing shell expansion, so I specified
it as `"<password>"`. 

Finally, `sigimage` is the name of a PNG file containing the signature. So far
so good. The tricky part is the `sigpage` and `sigrect` options. 

The `sigpage` option specifies which page the visual signature should be
placed. In the above `-1` means the last page. 

The `sigrect` specifies the lower bottom and top right rectangle of the
location on the page where the signature is placed. It requires a bit of trial
and error to get this right and this trial and error has to be repeated for
every new document.

But the good thing is that it works in settings where the other methods fail.
