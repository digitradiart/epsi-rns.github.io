---
layout: page-sidemenu-wm
title: Piping and Forking in Linux Script
---

This is just a complementary topic for Dzen, Lemonbar and Conky. Where they are also just tools in Desktop Ricing, especially Tiling Winodw Manager. Dive deeper, from decoration, to fundamental code.

	Goal: A script that continuously show date and time,
	with Dzen2, and Conky.

### How not to be a Bashful Plumber.

Piping is a powerful concept in Linux and Unix. 
Concept is deeper than just syntax.
As meaning is more important than grammar.

Piping is very common, and very easy to do it in BASH.
We utilize a lot of Pipe to communicate with Dzen2 and Lemonbar.
But we might need a little googling
when it comes to other language.
This is why I brought this topic.

Beside <code>|</code> character in BASH,
most common method are using <code>popen</code>,
or <code>subProcess</code>.
but there are other mechanism as well,
depend on the language you deal with.

	Becoming plumber automagically.

I have made few example of Pipe, Port BASH to other language,
step by step for your convenience.
So now we have BASH, Perl, Python, Ruby, PHP, Lua, and Haskell.
These will give you overview on how
to flow your stream through pipe conduit.

	Make the script simple, less problem.

In short, this Pipe example has Source and Target

*	Pipe Source: using conky, or internal function

*	Pipe Target: using less command, or dzen2.

Since we want to go for walk step by step,
I use <code>less</code> for Pipe target,
and later <code>dzen2</code> for Pipe Target.
And for feed, we are using <code>conky</code>,
and also function as a pipe source.

	Dark art of daemonizing a process.

For each article I also add Fork example,
so your process can run in the background,
detached from console.

We only fork the previous pipe process,
we use less and dzen2. We do not fork cat,
or any other cute animal.

-- -- --

### Pipe and Fork in Desktop Ricing

I have seen a lot of configuration in dotfiles.
Most utilize external tool written in BASH, Perl of Python.
Between taste and color of your language, it is just about preferences.
If you want to combine your preferred language to configure
Dzen2 or Lemonbar,or any tools in need of piping and fork,
then this tutorial is a good starter kit for you.

For historical reason.
BASH evolve to Perl, Perl Evolve to PHP and Python.
And here we are with Ruby. This is why I put these script.

I also add Lua.
Lua only used as embedded script. But I can see Lua's potential.
I have never seen anybody utilize Lua as main scripting tools in dotfiles,
except Conky, and AwesomeWM that use Lua as main scripting configuration.
I myself utilize a lot of Conky a lot. So why not make a start ?

How about Haskell? 
This is not a scripting language but compiled.
The reason is XMonad. Most people is still combined
the Haskell Configuration, with BASH, conky, even Perl.
Makes it looks like a Frankestein.
We need a more unified approach.

And of course you have total Control of the script,
e.g. color across the configuration,
when you call from just one script, instead of many language.

But again, it is a matter of preferences.


### Blog Post

*	[Piping and Forking in BASH][local-bash]

*	[Piping and Forking in Perl][local-perl]

*	[Piping and Forking in Python][local-python]

*	[Piping and Forking in Ruby][local-ruby]

*	[Piping and Forking in PHP][local-php]

*	[Piping and Forking in Lua][local-lua]

*	[Piping and Forking in Haskell][local-haskell]

### Dotfiles (Source Code)

*	[lang/bash/][dotfiles-BASH]

*	[lang/perl][dotfiles-Perl]

*	[lang/python][dotfiles-python]

*	[lang/ruby][dotfiles-Ruby]

*	[lang/php][dotfiles-PHP]

*	[lang/lua][dotfiles-Lua]

*	[lang/haskell][dotfiles-Haskell]

Thank you for Reading.

[//]: <> ( -- -- -- links below -- -- -- )

{% assign dotfiles_path = 'https://github.com/epsi-rns/dotfiles/blob/master/standalone/lang' %}

[local-bash]:    http://epsi-rns.github.io/code/2017/04/15/bash-pipe-and-fork.html
[local-perl]:    http://epsi-rns.github.io/code/2017/04/16/perl-pipe-and-fork.html
[local-python]:  http://epsi-rns.github.io/code/2017/04/17/python-pipe-and-fork.html
[local-ruby]:    http://epsi-rns.github.io/code/2017/04/18/ruby-pipe-and-fork.html
[local-php]:     http://epsi-rns.github.io/code/2017/04/19/php-pipe-and-fork.html
[local-lua]:     http://epsi-rns.github.io/code/2017/04/20/lua-pipe-and-fork.html
[local-haskell]: http://epsi-rns.github.io/code/2017/04/21/haskell-pipe-and-fork.html

[dotfiles-BASH]:    {{ dotfiles_path }}/bash
[dotfiles-Perl]:    {{ dotfiles_path }}/perl
[dotfiles-python]:  {{ dotfiles_path }}/python
[dotfiles-Ruby]:    {{ dotfiles_path }}/ruby
[dotfiles-PHP]:     {{ dotfiles_path }}/php
[dotfiles-Lua]:     {{ dotfiles_path }}/lua
[dotfiles-Haskell]: {{ dotfiles_path }}/haskell