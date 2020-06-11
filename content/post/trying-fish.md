---
title     : "Trying Fish Shell"
date  : 2020-06-10
tags  :
  - fish
  - zsh
  - shell
categories :
  - terminal
---

I have been using `zsh` as my shell for almost 15 years. I spent ages
configuring it in the beginning and for the most part I have been happy with
my setup. Lately, I saw multiple suggestions for
[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions), which
has a tag-line "Fish-like autosuggestions for zsh". Imitation is the best form
of flattery. So, I thought of giving fish a shot.

<!--more-->

I did not have high hopes with fish, simply because I am so very used to zsh
and fish is an [opinionated
shell](https://fishshell.com/docs/current/design.html) which breaks POSIX
compatibility. After spending a day configuring it and spending another day
using it, I have been pleasantly surprised. 

For the most part, the default behavior of fish was similar to my zsh setup.
It was relatively simple to port all my aliases and customized shell functions
from zsh to fish. And for the most part, I was productive right away in fish. 

I like the directory dependent auto-suggestions and like the fact that
it can parse man pages for tab completion options. As an example, I use
[hugo](https://gohugo.io/) to generate this blog. Hugo comes with a completion
plugin for bash but not for zsh. I had a written a bare bones zsh-completion
plugin for hugo for the 2 or 3 options that I commonly use. Fish is able to
suggest tab completion options by parsing the man page.

But there are two pain points. 

The first is the ability to ignore suffixes in tab completion. For example, I
had the following in my .zshrc [inserted manual line break for readability]:

<pre><code><span class="Comment"># Vim&#0058; do not offer completion of temporary files of tex</span>
<span class="Keyword">zstyle</span> <span class="String">'</span><span class="String">:completion::*:(nvim|vim):*</span><span class="String">'</span> file-patterns <span class="String">'</span><span class="String">*~*.(aux|dvi|log|thm|idx|pdf
        |rel|out|tuo|tui|tuc|tmp|mpo|mpb|mpd|1|keep|pgf|fls|gz|fdb_latexmk)</span><span class="String">'</span> <span class="String">'</span><span class="String">*</span><span class="String">'</span></code></pre>

So, when I press `vim <Tab>`, the autocomplete does not suggest filenames with
the above extensions. I use the same pattern for many programs. In my quick
scan of the auto-complete plugins distributed with fish, I could not find a
similar pattern. I ended up replicating this functionality with (again,
inserted line break for readability):

<pre><code><span class="Comment"># Do not offer completion of tmp files</span>
complete -c nvim -x -a &quot;(
  fd --full-path --type file --max-results=20 (commandline -ct) 
      -E <span class="String">'*.{aux,bbl,blg,dvi,fdb_latexmk,gz,fls,idx,log,pdf,pgf,out,tmp,tuc,thm}'</span>
)&quot; </code></pre>

This is not functionally equivalent and I am forking out to an external
problem rather than builtin completion. But `fd` is relatively fast, and I do
not notice any perceptible difference in speed. Plus, it has the advantage
that I get suggestions from nested sub-directories, which I find useful.

The other trouble is that fish does not support history substitution (`!$`,
etc.). This is explicitly mentioned in the
[FAQ](https://fishshell.com/docs/current/faq.html#why-doesn-t-history-substitution-etc-work).
Fish recommends using `Up Arraw` and editing the recalled line or using `Alt+Up
Arrow` or `Alt+.` to recall the arguments of the previous command, start from
the last.

This breaks a lot of my muscle memory. For example, when generating a new blog
post, I use

```
$ hugo new post/title-of-the-post.md
$ nvim content/!$ 
```

Using `Alt+.` after `content/` does not work.[^1]

[^1]: Though, in this situation, I can use my fancy new `fd` based completion to simply do `nvim title<Tab>` to complete the filename. I could also use `nvim title<Ctrl+T>` to invoke `fzf` based completion (but that doesn't ignore exteions).

In spite of these two annoyances, I like the rest of the shell. I like the
fact that I can type `funced name` to see how each function is defined, and
tweak it if I wish. So, I am going to use fish for a while and see how it
goes. 




