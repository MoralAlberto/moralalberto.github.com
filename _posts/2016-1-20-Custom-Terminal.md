---
layout: post
title: "Custom Terminal"
excerpt: "Custom Terminal with cool path bars, font and colors."
tags: [iTerm, oh-my-zsh, Terminal, Console, Bash, zsh]
image:
  feature: iTerm.png
---

> **Abstract**: iTerm, oh-my-zsh, Terminal, Console, Bash, zsh

I installed a new cool terminal, like [@KrauseFx](https://twitter.com/KrauseFx) has. There are some steps:

- Install [iTerm2](https://iterm2.com)
<br/>
- Install [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
<br/>
<br/>
run the following command in your terminal

{% highlight bash %}
code sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
{% endhighlight %}

or

{% highlight bash %}
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
{% endhighlight %}

When you have installed **oh-my-zsh**, edit the ./zshrc file with vim (or another editor)

{% highlight bash %}
vim ~/.zshrc
{% endhighlight %}

and change the theme to "agnoster"

{% highlight bash %}
ZSH_THEME="agnoster"
{% endhighlight %}

then, if you open a new terminal you will see question mark symbols, don't be scared! we'll fix that in the next steps

<figure>
    <a href="/images/MVC-2.png"><img src="/images/iTerm-1.png"></a>
</figure>

- Install [Solarized](http://ethanschoonover.com/solarized) a new terminal colors
<br/>
Download the latest package, and in iTerm2 preferences, within profiles select the section menu Colors, and import the two Solarized colors for iTerm2.

<figure>
    <a href="/images/MVC-2.png"><img src="/images/iTerm-2.png"></a>
</figure>

- Install [powerline-shell](https://github.com/milkbikis/powerline-shell) to get a cool bar paths.
<br/>
Clone the repository

{% highlight bash %}
git clone https://github.com/milkbikis/powerline-shell
{% endhighlight %}

Copy **config.py.dist** to **config.py** and edit it to configure the segments you want. Then run the next command:
{% highlight bash %}
./install.py
{% endhighlight %}

Create a symlink to this python script in your home:
{% highlight bash %}
ln -s /Users/Moral/powerline-shell/powerline-shell.py ~/powerline-shell.py
{% endhighlight %}

Add the following to your **.zshrc file**:

{% highlight bash %}
function powerline_precmd() {
    PS1="$(~/powerline-shell.py $? --shell zsh 2> /dev/null)"
}

function install_powerline_precmd() {
  for s in "${precmd_functions[@]}"; do
    if [ "$s" = "powerline_precmd" ]; then
      return
    fi
  done
  precmd_functions+=(powerline_precmd)
}

if [ "$TERM" != "linux" ]; then
    install_powerline_precmd
fi
{% endhighlight %}

And now, close and open iTerm2, you will see a new style within your terminal 😁.
