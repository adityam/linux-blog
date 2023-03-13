---
title     : "Converting SuperNote files to PDF"
date  : 2022-09-02
tags  :
  - supernote
  - supernote-tool
  - rake
  - cron
  - Max3
  - A6X
  - boox
categories :
  - eink
---

Writing is my primary mode of note taking and at the beginning of the
pandemic, I switched from paper notebooks to digital notebooks. My primary
note taking devices are SuperNote A6X, Boox Max3, and iPad Pro using Notability
App (in that order,
though I do prefer iPad for reviewing papers etc. because of the speed). I
prefer the writing experience on Supernote, but I do miss one of the software
features of Boox: In Boox, every time I save a note file, it is automatically
converted to PDF and stored in a pdf directory which mirrors the layout of the
notes directory. There is no such feature in SuperNote. Notes have to
converted manually and get exported to a flat directory. 

<!--more-->

Now, the reason that I want automatic pdf conversion is that PDF is a
universal format and can be read on any device. In contrast, all the digital
notebook devices or apps use a propriety format, which cannot be read easily
on other devices. That's why I like the automatic conversion on Boox. That
(along with DropSync to instantly sync the converted file) means that I always
have access to the latest version of my notebooks everywhere. On SuperNote, I
need to remember to manually convert the file. (And also manually since the
device, but that's another story). 

I recently discovered a script called
[supernote-tool](https://github.com/jya-dev/supernote-tool), which parses the
SuperNote `.note` format and converts it to PDF. So, I wrote a simple
[`Rakefile`][rake] to convert all `.note` files to `.pdf` files. 

I sync SuperNote files using Dropbox and Supernote creates a directory
`Dropbox/Supernote` which has the following structure: 

```
Dropbox/Supernote
├── Document
├── EXPORT
├── INBOX
├── MyStyle
├── Note
└── SCREENSHOT
```

By default, all the note files are in the `Note` sub-directory. I wanted to
create a sibling directory called `pdfs`, which mirrors the layout of the
`Notes` directory and converts each `.note` file to `.pdf`. I wanted to write
this as a `Makefile` so that I can periodically run the conversion via `cron`,
but writing complext makefiles is always wild goose chase on google. So, I
decided to write it as a `Rakefile`, although I haven't touched ruby in almost
15 years. Here is the file:

<pre><code>
<span class="Include">require</span> <span class="Delimiter">'</span><span class="String">fileutils</span><span class="Delimiter">'</span>
<span class="Include">require</span> <span class="Delimiter">'</span><span class="String">rake/clean</span><span class="Delimiter">'</span>

<span class="Type">SN</span> = <span class="Delimiter">&quot;</span><span class="String">$HOME/Software/supernote-tool/venv/bin/supernote-tool</span><span class="Delimiter">&quot;</span>

<span class="Type">SRC_DIR</span> = <span class="Delimiter">&quot;</span><span class="String">Note</span><span class="Delimiter">&quot;</span>
<span class="Type">PDF_DIR</span> = <span class="Delimiter">&quot;</span><span class="String">pdfs</span><span class="Delimiter">&quot;</span>

<span class="Type">SRC</span> = <span class="Type">FileList</span>[<span class="Delimiter">&quot;</span><span class="Delimiter">#{</span><span class="Type">SRC_DIR</span><span class="Delimiter">}</span><span class="String">/**/*.note</span><span class="Delimiter">&quot;</span>]
<span class="Type">PDF</span> = <span class="Type">SRC</span>.pathmap(<span class="Delimiter">&quot;</span><span class="String">%{^</span><span class="Delimiter">#{</span><span class="Type">SRC_DIR</span><span class="Delimiter">}</span><span class="String">,</span><span class="Delimiter">#{</span><span class="Type">PDF_DIR</span><span class="Delimiter">}</span><span class="String">}X.pdf</span><span class="Delimiter">&quot;</span>)

directory <span class="Type">PDF_DIR</span>

task <span class="Constant">:</span><span class="Constant">pdf</span> =&gt; <span class="Type">PDF</span>

rule <span class="Delimiter">'</span><span class="String">.pdf</span><span class="Delimiter">'</span> =&gt; <span class="Keyword">proc</span> {|f| f.pathmap(<span class="Delimiter">&quot;</span><span class="String">%{^</span><span class="Delimiter">#{</span><span class="Type">PDF_DIR</span><span class="Delimiter">}</span><span class="String">,</span><span class="Delimiter">#{</span><span class="Type">SRC_DIR</span><span class="Delimiter">}</span><span class="String">}X.note</span><span class="Delimiter">&quot;</span>)} <span class="Statement">do</span> |t|
    mkdir_p t.name.pathmap(<span class="Delimiter">&quot;</span><span class="String">%d</span><span class="Delimiter">&quot;</span>)
    sh <span class="Delimiter">%Q{</span><span class="Delimiter">#{</span><span class="Type">SN</span><span class="Delimiter">}</span><span class="String"> convert -t pdf --pdf-type vector -a &quot;</span><span class="Delimiter">#{</span>t.source<span class="Delimiter">}</span><span class="String">&quot; &quot;</span><span class="Delimiter">#{</span>t.name<span class="Delimiter">}</span><span class="String">&quot;</span><span class="Delimiter">}</span>
<span class="Statement">end</span>

task <span class="Constant">:</span><span class="Constant">default</span> =&gt; <span class="Constant">:</span><span class="Constant">pdf</span>

<span class="Type">CLEAN</span>.include(<span class="Type">PDF</span>)

</code></pre>

The tricky part of the writing this was to figure out how to make sure that
the converted pdfs are saved in a parallel directory with the same directory
structure as the notes directory. I was hoping that there was an easy way to
do it using `rule`s, but I could not figure it out. So, I followed a very
rudimentary method. 

First, collect the names of all `.note` files:
<pre><code>
<span class="Type">SRC</span> = <span class="Type">FileList</span>[<span class="Delimiter">&quot;</span><span class="Delimiter">#{</span><span class="Type">SRC_DIR</span><span class="Delimiter">}</span><span class="String">/**/*.note</span><span class="Delimiter">&quot;</span>]
</code></pre>

and then change the root directory and the extension:


<pre><code>
<span class="Type">PDF</span> = <span class="Type">SRC</span>.pathmap(<span class="Delimiter">&quot;</span><span class="String">%{^</span><span class="Delimiter">#{</span><span class="Type">SRC_DIR</span><span class="Delimiter">}</span><span class="String">,</span><span class="Delimiter">#{</span><span class="Type">PDF_DIR</span><span class="Delimiter">}</span><span class="String">}X.pdf</span><span class="Delimiter">&quot;</span>)
</code></pre>

This looks like black magic, but it fairly simply once your read the
documentation of
[`pathmap`](https://ruby-doc.org/stdlib-3.0.2/libdoc/rake/rdoc/String.html). 

Then, I specify a rule to generate the `.pdf` file. To do so, I need to
specify that the corresponding `.note` file is a dependency of the `.pdf`
file, I run the conversion if the `.note` file is newer than the `.pdf` file.
Rake has this super cool feature that you don't have to explicitly list all
target names; it can regex against a specified string. See, for example, [this
blog post](https://jacobswanner.com/development/2014/rake-rule-tasks/).
However, this automatic dependency specification only works when the source
and the destination are in the same directory. But I want the `.note` and
`.pdf` files to be in parallel directories. Again, I found that the simplest
way to do this was to invert the conversion used to generate the name of the
`.pdf` file:


<pre><code>
rule <span class="Delimiter">'</span><span class="String">.pdf</span><span class="Delimiter">'</span> =&gt; <span class="Keyword">proc</span> {|f| f.pathmap(<span class="Delimiter">&quot;</span><span class="String">%{^</span><span class="Delimiter">#{</span><span class="Type">PDF_DIR</span><span class="Delimiter">}</span><span class="String">,</span><span class="Delimiter">#{</span><span class="Type">SRC_DIR</span><span class="Delimiter">}</span><span class="String">}X.note</span><span class="Delimiter">&quot;</span>)} <span class="Statement">do</span> |t|
    mkdir_p t.name.pathmap(<span class="Delimiter">&quot;</span><span class="String">%d</span><span class="Delimiter">&quot;</span>)
    sh <span class="Delimiter">%Q{</span><span class="Delimiter">#{</span><span class="Type">SN</span><span class="Delimiter">}</span><span class="String"> convert -t pdf --pdf-type vector -a &quot;</span><span class="Delimiter">#{</span>t.source<span class="Delimiter">}</span><span class="String">&quot; &quot;</span><span class="Delimiter">#{</span>t.name<span class="Delimiter">}</span><span class="String">&quot;</span><span class="Delimiter">}</span>
<span class="Statement">end</span>
</code></pre>

And that's it! I added a cron task so that this script is run every 6 hours. 

[rake]: https://ruby.github.io/rake/index.html

There are a few things that I don't like about this setup:

* AFAIK, there is no public discription of the spec of the SuperNote format.
  So, most likely, `supernote-tool` is a reverse engineered tool. It works
  well now, but break in a future release. 

* Supernote provides two conversion methods: pixel conversion and vector
  conversion. I prefer the look of vector conversion, but that is _very_ slow.
  It doesn't really matter much in this setup, as I don't need the PDF right
  away, but it is still annoying. I don't understand why the conversion is
  slow from a practical point of view (it is just drawing a smooth path from a
  bunch of points). One of these days, I plan to look into the
  storage format and figure out why the vector conversion is taking too long,
  and if I can speed it up by using Metapost. 

Nontheless, at present, this simple `Rakefile` scratches the itch.


