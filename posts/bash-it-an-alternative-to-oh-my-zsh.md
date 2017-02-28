<!-- 
.. title: bash-it: an alternative to oh-my-zsh
.. slug: bash-it-an-alternative-to-oh-my-zsh
.. date: 2017-02-28 13:56:50 UTC-08:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
-->

## Trying out zsh

I have noticed that many people seem to be switching shells to `zsh`. In fact, I even tried it myself to see what the hype was all about. :)

So for a while there I was actually feeling just as good using `zsh` as `bash`. That was until I started discovering some idiosyncrasies with `zsh` that were different from `bash`.
It appeared that `zsh` was not quite as backwards compatible with `bash`. Even though it seems to support the same features, it does so with similar but
different syntax. This made it quite painful because every system I work on has `bash` installed and used by default, but not necessarily `zsh`. Also,
many scripts out there in repositories are all coded to use `bash`. Now of course you can specify the shebang to make sure the scripts use `bash`, but why would I want to use a different shell
than the scripts or systems I'm working with use themselves? I would then have to learn and maintain scripts for two different shells depending on what
I'm working on.

### oh-my-zsh

I'll also add that the most promising feature of switching to `zsh` was the awesome [`oh-my-zsh`](https://github.com/robbyrussell/oh-my-zsh) GitHub project that gives you a highly customizable framework.
This seemed to clearly be lacking in `bash` and so it was this that made it quite compelling for me at first. In addition, I agree that `zsh`
does have some nice features that work right out of the gate, but it felt like it was more to do with `oh-my-zsh` than `zsh` itself.
Therefore, nothing was really compelling enough that you couldn't do with `bash`, albiet with some customizations.

## Moving back to bash

### bash-it

This is where I stumbled upon [bash-it](https://github.com/Bash-it/bash-it). It literally promotes itself as being a
shameless ripoff of [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh). Finally, this seemed to be the piece that was missing from `bash` that one was able to get
with `zsh` quite easily. I immediately started using it and enjoyed bashing again.

[bash-it](https://github.com/Bash-it/bash-it) has all of the benefits of a framework with support for plugins, aliases, completions,
custom scripts and functions, themes, updates, search, etc. Here is a quick summary of some help options:

```
bash-it show aliases        # shows installed and available aliases
bash-it show completions    # shows installed and available completions
bash-it show plugins        # shows installed and available plugins
bash-it help aliases        # shows help for installed aliases
bash-it help completions    # shows help for installed completions
bash-it help plugins        # shows help for installed plugins
```

I even stumbled upon a few issues and feature requests myself, which I proceeded to contribute back as time allowed.
I have most recently created and contributed my own [font theme](https://github.com/Bash-it/bash-it/blob/master/themes/font/font.theme.bash).
It supports the following prompt features:

- time
- Python virtual environment
- user
- host
- path
- git repo
- git branch
- git dirty/not dirty
- return code status of last command

Here is a [screenshot](https://github.com/font/bash-it/tree/font_theme/themes/font#screenshot) of it in action.

Check it out and let me know how you like it!
