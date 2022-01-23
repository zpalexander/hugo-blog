---
title: "Switching to ZSH"
date: 2017-04-15T16:14:11-08:00
draft: false
featured_image: "images/switching-to-zsh/cool-terminal-2.png"
aliases:
    - /switching-to-zsh/
---

[Jeff Atwood](https://en.wikipedia.org/wiki/Jeff_Atwood) once famously proclaimed ["we are typists first, programmers second"](https://blog.codinghorror.com/we-are-typists-first-programmers-second/). While I think software development is [a little more complicated than that](https://developers.slashdot.org/story/10/12/25/2034201/Does-Typing-Speed-Really-Matter-For-Programmers), this quote brings up an important point: efficiency in software development hinges on the tooling we choose.

For the longest time I used [Bash](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) as my UNIX shell. It got the job done. I would occasionally feel frustrated by a repetitive command or a missing feature, but there was usually an [alias](http://www.tldp.org/LDP/abs/html/aliases.html) or [script](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html) I could apply like a bandaid to minimize the pain enough to keep going. I even switched to [iTerm](https://iterm2.com/) several years back to improve my terminal emulator GUI. I worked with Bash for long enough that I managed to amass a pretty fancy [.bash_profile](http://www.joshstaiger.org/archives/2005/07/bash_profile_vs.html) config. If you're curious about my config, [you can check it out for yourself](https://gist.github.com/zpalexander/213a0b53b4bc128c0410195a79a6ea51).

But I'm not here to help you optimize Bash. I'm here to tell you there's a better way.

---

### Why ZSH is better than Bash

After a few years working in the software industry, I kept noticing that the shells of senior programmers often looked much different than mine did.

Whereas my shell looked something like this:

![Standard boring terminal screenshot](/images/switching-to-zsh/terminal-mac.png)

Theirs tended to look more like this:

![ZSH terminal screenshot](https://cloud.githubusercontent.com/assets/2618447/6316862/70f58fb6-ba03-11e4-82c9-c083bf9a6574.png)

I had heard talk of alternative shells like [ZSH](http://www.zsh.org/) before, but I always assumed that the difference was purely aesthetic. It wasn't until I started working for a company whose cloud machines all came pre-configured with ZSH that I realized what I was missing.

ZSH provides a huge number of improvements over other shells and I could write an entire post just comparing and contrasting the benefits and drawbacks. Instead, I'll provide you with [this excellent slide deck](//www.slideshare.net/jaguardesignstudio/why-zsh-is-cooler-than-your-shell-16194692) about the benefits of ZSH over Bash:

<iframe src="//www.slideshare.net/slideshow/embed_code/key/1XBPbsul67hF67" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>

---

### Getting Started on Mac OS X

First let's cover the basics of getting ZSH set up on your Mac. The good news is that you probably already have ZSH on your system.

#### 1. Install ZSH

![ZSH logo](https://ih0.redbubble.net/image.154489147.8220/flat,800x800,075,f.jpg)

Let's get started by running <code>which zsh</code> in whatever shell you're currently using. If you have the executable on your system, <code>[which](https://linux.die.net/man/1/which)</code> should output <code>/bin/zsh</code> or something similar.

If <code>which</code> instead outputs <code>zsh not found</code>, you can install zsh easily with [Homebrew](https://brew.sh/). Just run <code>brew install zsh</code>.

I also strongly recommend using [iTerm2](https://iterm2.com/) as your terminal emulator app on Mac OS X. You can download it [here](https://iterm2.com/downloads/stable/latest).

#### 2. Set ZSH as your default shell

The actual process for changing your default shell from Bash to ZSH is extremely easy. Just run <code>chsh -s /bin/zsh</code>.

>***Note*** that you'll need to supply the correct path your ZSH binary which you can get with the <code>which zsh</code> command we used earlier. [Click here](https://linux.die.net/man/1/chsh) for more information on the <code>chsh</code> command.

That's it! Close and reopen your terminal. You're now using ZSH!


#### 3. Install a ZSH configuration manager

![oh my zsh](https://camo.githubusercontent.com/5c385f15f3eaedb72cfcfbbaf75355b700ac0757/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f6f686d797a73682f6f682d6d792d7a73682d6c6f676f2e706e67)

In the old days, configuring ZSH manually took a lot of time and effort. Thankfully, ZSH has a large and devoted user community which has produced a few tools to help with writing and sharing ZSH configurations.

The two big players are [Oh My ZSH](http://ohmyz.sh/) and [Prezto](https://github.com/sorin-ionescu/prezto).

> ***tl;dr*** Oh My ZSH has a larger community, but Prezto performs better with a large number of modules installed.

I use [Oh My ZSH](http://ohmyz.sh/) since it has a wider variety of themes and plugins. Its also the most "standard" of all options, which means a larger community and better support. If you hit a wall early with Oh My ZSH, feel free to try out alternatives.

Installing Oh My ZSH is pretty easy. You can find [CURL and WGET instructions here](https://github.com/robbyrussell/oh-my-zsh#basic-installation).

Run the command of your choice and that's it. One and done.

#### 4. Choose a theme

![sample list of themes](/images/switching-to-zsh/zsh-theme-list-1.png)

Setting a custom theme with Oh My ZSH is simple. Oh My ZSH comes bundled with a bunch of themes by default. You can find a [list with screenshots on their wiki](https://github.com/robbyrussell/oh-my-zsh/wiki/themes). If you want to see the files on your local filesystem, they live in <code>~/.oh-my-zsh/themes</code>.

Once you've picked a theme, open the file <code>~/.zshrc</code> in the text editor of your choice. Towards the top of the file you should see a value <code>ZSH_THEME</code>. Change the value of the string to the name of the theme, save the file, and then run <code>source ~/.zshrc</code> to pick up the changes.

Check out the [Oh My ZSH README](https://github.com/robbyrussell/oh-my-zsh#custom-plugins-and-themes) for info on how to install custom themes.

#### 5. Add plugins

Oh My ZSH's best feature is its ability to manage custom ZSH plugins. Setting up a list of custom plugins works just like changing themes. Open <code>~/.zshrc</code> in a text editor and look for a line that looks something like <code>plugins=(git)</code>. Check out the comment two lines up for an example of how to add to this list.

Just like with themes, you can browse bundled plugins in [a list on the Oh My ZSH wiki](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins), or you can browse the files locally at <code>~/.oh-my-zsh/plugins</code>. Custom plugins can also be installed. Check out the [Oh My ZSH README](https://github.com/robbyrussell/oh-my-zsh#custom-plugins-and-themes) for more info.

I'm a relative newbie which makes me a bad resource, but here's a list of the plugins I'm currently using:

* [colorize](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/colorize)
* [colored-man-pages](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/colored-man-pages)
* [command-not-found](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/command-not-found)
* [cp](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/cp)
* [extract](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/extract)
* [git](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/git)
* [npm](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/npm)
* [web-search](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/web-search)

#### 6. Customize your experience

Now that you've got ZSH and Oh My ZSH set up, the possibilities for customization are nearly endless. You can add custom themes and plugins, write your own custom aliases or configure advanced auto-completion for the tools you use most commonly. A couple good places to get started are [zsh-lovers](https://grml.org/zsh/zsh-lovers.html), the [ZSH man page](https://linux.die.net/man/1/zsh) and the [Oh My ZSH community page](http://ohmyz.sh/community/).

As a VIM lover, one addition I personally made was to add a command mode to my shell so that I could use VIM keybindings to navigate around the text of my commands. You can do this with <code>bindkey -v</code>.

Bonus shot of my terminal (PII grayed out):

![Screenfetch of my terminal](/images/switching-to-zsh/my-terminal-screenshot-2.png)

