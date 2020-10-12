---
title: Web Development On An iPad
description: Using a VPS with SSH to run web development software through an iPad
date: 2017-05-03
tags:
---

![iPad Pro with Smart Keyboard](/img/ipad-pro.jpg)
_iPad Pro with Smart Keyboard — good enough for web development?_

Apple has always tried to market the iPad Pro as a laptop replacement. This might work out for college students or people in certain professions where the available iOS apps cover their daily needs. However, as a web developer, there are too many command line tools that are necessary on a day-to-day basis that makes it infeasible for an iPad by itself to be a complete laptop replacement.

However, you can combine an iPad with a VPS (Virtual Private Server) to have access to the best of both worlds. In essence, a VPS is a remote computer where you have full access to an Linux system. You can install all the web development tools you normally use on your laptop or desktop. Then you use your iPad to SSH into your VPS, giving you full access to your remote system through the terminal.

Think about what this means — an iPad can never be a fully fledged laptop replacement, but with a VPS it doesn’t have to be! Let the iPad be the iPad, and your VPS will be the remote computer that handles everything else. Services like DigitalOcean and Linode offer a VPS for \$5/month, so it’s very affordable to get started!

This isn’t a unique idea — some people have been using this setup for years, and there are [several](http://yieldthought.com/post/12239282034/swapped-my-macbook-for-an-ipad) [good](https://medium.com/@felippenardi/how-to-setup-a-remote-development-server-from-stracth-2b1e6155464c) [write-ups](http://decoding.io/using-ipad-pro-as-a-web-developer/) about it. This post will cover the tools and tips that I have found useful for my specific workflow.

## What you’ll need

- An iPad _(I use a [12.9" iPad Pro](https://www.apple.com/shop/buy-ipad/ipad-pro/12.9-inch-display-128gb-space-gray-wifi))_

- A compatible keyboard _(I use the [iPad Pro Smart Keyboard](https://www.apple.com/shop/product/MJYR2LL/A/smart-keyboard-for-129-inch-ipad-pro-us-english))_

- A Virtual Private Server _(I use [DigitalOcean](https://www.digitalocean.com/pricing/)’s \$5/month plan)_

- An iOS terminal app _(I use [Blink](http://www.blink.sh/))_

- An iOS text editor _(I don’t use one, I use Vim instead)_

## Step 1: Get your VPS up and running

Your VPS is your remote system where you’ll install all your familiar web development tools — your programming language (ruby, node, etc.), your database (postgres, mysql, etc.), your version control (git, mecurial, etc.). Generally you’ll set up a VPS using Ubuntu without a graphical operating system — everything is installed using a command line. This may seem a bit daunting if you’re only familiar with Mac or Windows computers. However, if you’ve been doing web development for a while, you are already more than familiar with installing applications through a command line.

I use DigitalOcean, which has fantastic tutorials that walk you through every step of the process. To get started with DigitalOcean, [sign up](https://m.do.co/c/196d08803914) for a plan, then follow the steps in this tutorial: [Initial Server Setup with Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04). Again, it can be a bit daunting for first time Linux users, but the instructions are clear and the content is definitely worth learning. By the time you’re done, you’ll have your very own remote box which you can log into from any computer connected to the internet!

> **_TIP_**: DigitalOcean provides a web-based terminal to log into your remote server. I found it to be painfully slow — it’s useful to first get started, but switch to your terminal of choice to ssh into your remote box as soon as you get a chance!

## Step 2: Install software on your VPS

Once you have a basic VPS set up, you’ll need to install your web development tools. Here are some helpful guides from DigitalOcean to get you started:

- [How To Install Ruby on Rails with rbenv on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-16-04)

- [How To Install Node.js on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04)

- [How To Install and Use PostgreSQL on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)

There are many more [well-written tutorials](https://www.digitalocean.com/community/tutorials) from DigitalOcean to help you install pretty much anything you can think of. Note that at this point you have a fully functional Ubuntu Linux machine, so any installation instructions that work with Ubuntu will work for your VPS, there’s nothing really specific to DigitalOcean at this point.

## Step 3: Set up your iPad terminal

To access your VPS using an iPad, you’ll need an iOS app that has SSH capabilities. My app of choice is [Blink](http://www.blink.sh/) — it costs \$20, but it’s completely open source and provides instructions so you can [build it yourself](https://github.com/blinksh/blink/#build) using XCode! Blink uses [Mosh](https://mosh.org/), an SSH replacement that is built to work with unstable and intermittent connectivity (Blink works with regular SSH as well). Perhaps the most important feature to me — Blink let’s you remap the Caps Lock key on your iPad keyboard to Control (or whatever you want). A small detail but hugely important to my workflow!

Once you have an iOS terminal app installed, you can SSH into your VPS as you would on any computer. For example, if you set up a username of bob on your VPS, and your VPS IP address was 192.168.10.1, then you can run in your iPad terminal:

    ssh bob@192.168.10.1

If you’re using Blink, you can use mosh instead:

    mosh bob@192.168.10.1

Now you have full terminal access to your remote computer on your iPad!

> **_TIP_**: If you own a domain, you can configure your VPS IP address to point to that domain or a subdomain (the instructions depends on your domain provider). So if you own the domain mycoolsite.com, you can point your _192.168.10.1_ IP address to a subdomain like dev.mycoolsite.com. This allows you do ssh or mosh into your VPS using ssh bob@dev.mycoolsite.com.

## Step 4: Set up your iPad text editor

At this point you’ll need a text editor to do any actual programming. But it can’t be any iOS text editor — it must be able to edit remote files on your VPS. The most feature rich editor I found was [Coda for iOS](https://panic.com/coda-ios/) — syntax highlighting, tabs, built-in terminal, you name it. Other interesting alternatives are apps that connect to GitHub, like [Working Copy](https://workingcopyapp.com/) or [Git2Go](http://git2go.com/). These apps work as GitHub clients, but also come with text editors to edit files directly as well.

However, I don’t use any of these apps — instead, I use Vim installed on my VPS. If you’re not familiar with Vim, this may seem like a strange choice, and you can definitely go with one of the above options instead. But if you are already comfortable with Vim or [willing to learn it](how-to-learn-vim-a-four-week-plan.html), then you get the benefit of having a world-class editing experience on your iPad instead of one that will inevitably be limited compared to your regular text editor of choice.

## Step 5: Handle miscellaneous issues

At this point you have a complete iPad development machine, with full terminal access to a remote Linux computer and a full programming text editor! Anything you could do on your laptop you can now do on your iPad! Well, almost — there are a couple of gotchas that we have to take care of…

### Use multiple terminal tabs

Usually as a web developer you will have multiple terminal tabs open. However, when you use your iOS terminal to ssh into your remote server, opening a new tab doesn’t open a new ssh connection. There are different workarounds for this — the one I recommend using is [tmux](a-minimalist-guide-to-tmux.html). If you aren’t familiar with tmux, it basically allows you to have multiple terminal sessions within a single terminal window. It also has the benefit of saving a session even when you log off, which means you can sleep or turn off your iPad and still maintain the terminal processes on your remote server.

### Run a development web server

You can easily run a development server on your VPS localhost, but it won’t be accessible on your iPad. The solution is to bind your development web server to your VPS IP address instead of localhost.

If you’re using rails, you can bind the dev server to your VPS IP address (here we’re using 192.168.10.1 as an example) on port 3000 like this:

    rails server -b 192.168.10.1 -p 3000

If you’re using Node, you can set the HOST to the VPS IP address to accomplish the same thing. For example, if you were running webpack-dev-server, you can run the command:

    HOST=192.168.10.1 PORT=3000 webpack-dev-server --hot --inline

Now on your iPad, you can see your development site by opening up any browser and going to [http://192.168.10.1:3000](http://192.168.10.1:3000). Sweet!

### Access a JavaScript console

Now that you can see your development application in the browser, the only thing that’s missing is a JavaScript console. Sadly iOS browsers don’t provide the same dev tools as their desktop counterparts, making it very difficult to debug JavaScript issues.

The solution is to use a bookmarklet for [Firebug Lite](http://getfirebug.com/firebuglite)! For those unfamiliar with bookmarklets, they are basically bookmarks with embedded JavaScript — they were what people used before browser extensions came about. Browser extensions don’t exist on iOS, but bookmarklets still work just fine. Follow [these instructions](http://martinkool.com/post/13629963755/firebug-on-ipad-and-iphone) to install the Firebug Lite bookmarklet in your iOS browser, and you have access to debugging tools on any website you visit! Not as extensive as Chrome dev tools, but you do get a JavaScript console, which is critical to web development.

> **Edit**: After many years, it looks like Firebug Lite is no longer running as a service! If you’re looking for an alternative, there are iOS apps that provide similar functionality, such as [Inspect Browser](https://apps.pdyn.net/inspect/), [Webtools](http://ergowebtools.com/) & [MIH Tools](https://www.mihtool.com/).

## Conclusion

By following this guide, you can definitely use your iPad as a laptop replacement for web development. There are some weird gotchas, but it honestly covers 95% of your development needs without a hiccup. I still own a Macbook Pro, and I find the iPad Pro to be a great complement to it. When I carry both around, I can use the iPad Pro as a great second monitor (using [Duet](https://www.duetdisplay.com/) or [Astropad](http://astropad.com/)), or do development work on either machine. If I’m going on a trip, I’ll just take the iPad Pro.

If you’re a web developer and already own an iPad with keyboard, this is a no-brainer. Combining an iPad with a VPN will more than double the usefulness of your tablet as a professional (and you’ll learn more about Linux and running remote servers to boot). To me, it’s less about making the iPad replace my laptop and more about maximizing the iPad’s value. Hope you find these tips to be useful!
