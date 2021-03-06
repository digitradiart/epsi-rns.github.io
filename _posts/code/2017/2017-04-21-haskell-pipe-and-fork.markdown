---
layout     : post
title      : "Piping and Forking in Haskell"
date       : 2017-04-21 17:35:15 +0700
categories : code
tags       : [coding, conky, haskell]
keywords   : [pipe and fork, dzen2, lemonbar]
author     : epsi
toc        : toc/2017/04/pipe-and-fork-language.html

opengraph:
  image: /assets/site/images/topics/haskell.png

excerpt:
  How to be a Haskell Plumber.
  
related_link_ids: 
  - 17042335  # Pipe and Fork Overview
  - 17041535  # Pipe and Fork BASH
  - 17041635  # Pipe and Fork Perl
  - 17041735  # Pipe and Fork Python
  - 17041835  # Pipe and Fork Ruby
  - 17041935  # Pipe and Fork PHP
  - 17042035  # Pipe and Fork Lua
  - 17042135  # Pipe and Fork Haskell

---

### Haskell Plumber.

> Goal: A script that continuously show date and time,
> with Dzen2, and Conky.

Before you dip your toe to scripting,
you might desire to know the reason by reading this overview.

**Reading**

*	[Piping and Forking in Linux Script][local-overview]

-- -- --

### Pipe and Fork in Desktop Ricing

	Why Haskell? 

This is not a scripting language but compiled.
The reason is XMonad. Most people is still combined
the Haskell Configuration, with BASH, conky, even Perl.
Makes it looks like a Frankestein.
We need a more unified approach.

And of course you have total Control of the script,
e.g. color across the configuration,
when you call from just one Haskell script, instead of many language.

	Be Lazy, Learn Haskell.

-- -- --

### Start Simple

Welcome to n00berland. Begin with simple script.
We will use this loop as a source feed to pipe.
This step won't introduce Pipe nor Fork.

This script only show an infinite loop showing local time.
Each updated in one second interval.
We manage this interval by delaying,
using <code>sleep</code> code.

A few things to be noted here:

*	Haskell does have <code>threadDelay</code> function.
	But since it is in microsecond.
	We have to wrap it in a new <code>sleep</code> function.

*	Haskell complex time library could be wrapped into simple function.

*	Haskell has no built in loop.
	But we can use Control, e.g. <code>Forever</code>.

**Source**:

*	[gitlab.com/.../dotfiles/.../haskell-01-basic.hs][dotfiles-haskell-01-basic]

{% highlight haskell %}
import Data.Time.LocalTime
import Data.Time.Format

import Control.Concurrent
import Control.Monad

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- wrap Funktion

myTimeFormat = "%a %b %d %H:%M:%S"

wFormatTime :: FormatTime t => t -> String
wFormatTime myUtcTime = formatTime 
    Data.Time.Format.defaultTimeLocale myTimeFormat myUtcTime

wSleep :: Int -> IO ()
wSleep mySecond = threadDelay (1000000 * mySecond)

printDate = do
    now <- getZonedTime
    let nowFmt = wFormatTime now
    putStrLn nowFmt
    wSleep 1

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- main

main = forever $ printDate
{% endhighlight %}

Call to this simple code would produce time marching,
one after another, below the command line prompt.

![Pipe: Basic][image-time-basic]{: .img-responsive }

{% include toc/2017/04/pipe-and-fork-similar-01.html %}

-- -- --

### How does it works ?

Haskell has no built in loop.
Since we want infinite loop, we use Control <code>Forever</code>.

{% highlight haskell %}
main = forever $ printDate
{% endhighlight %}

There is this Dollar <code>$</code> operator.
You might consider reading my old post.

*	[How Haskell Syntax can Make Your Code Cleaner][local-haskell-dollar]

-- -- --

### External Command as Source Feed

Beside previous simple loop that is used as Internal Command,
this tutorial also provide Conky as External Command
in <code class="code-file">asset</code> directory.
I made it as simple as possible.

**Source**:

*	[gitlab.com/.../dotfiles/.../conky.lua][dotfiles-conky]

{% highlight lua %}
conky.config = {
    out_to_x = false,
    out_to_console = true,
    short_units = true,
    update_interval = 1
}

conky.text = [[\
${time %a %b %d %H:%M:%S}\
]]
{% endhighlight %}

-- -- --

### Spawning Using System Shell

Using system shell is simple and straightforward.
But it does not have any ability,
to stream internal function process,
that required later on this article.

**Source**:

*	[gitlab.com/.../dotfiles/.../haskell-02-system.hs][dotfiles-haskell-02-system]

{% highlight haskell %}
import System.Process
import System.Directory

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- wrap Funktion

-- source directory is irrelevant in Haskell
-- but we'll do it anyway for the sake of learning
wGetCmdIn :: String -> String
wGetCmdIn dirname = "conky -c " 
    ++ dirname ++ "/../assets" ++ "/conky.lua"

cmdout = "less" -- or dzen2

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- main

main = do
    dirname <- getCurrentDirectory
    let cmdin = wGetCmdIn dirname
    system $ cmdin ++ " | " ++ cmdout
{% endhighlight %}

{% include toc/2017/04/pipe-and-fork-similar-02.html %}

-- -- --

### A Unidirectional Pipe Between External Command

This step is overview of Pipe between two external command.
This short script is using <code>conky</code>
as pipe source feed and <code>less</code> as pipe target.
Showing time and date forever in the console.

	This infinite pipe run in time-less fashioned.

I add <code>dirname</code>, relative to the Haskell source,
to locate the conky script assets.
Source directory is irrelevant in Haskell,
since Haskell is compiled, and can be execute from anywhere.
But we'll do it anyway for the sake of learning

We use this amazingly cool Haskell's
<code>createProcess</code> mechanism.

**Source**:

*	[gitlab.com/.../dotfiles/.../haskell-02-process.hs][dotfiles-haskell-02-process]

{% highlight haskell %}
import System.Process
import System.Directory

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- wrap Funktion

-- Source directory is irrelevant in Haskell
-- but we'll do it anyway for the sake of learning
wConkyFileName :: String -> String
wConkyFileName dirName = dirName ++ "/../assets" ++ "/conky.lua"

cmdin  = "conky"
cmdout = "less" -- or dzen2

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- main

main = do
    dirName <- getCurrentDirectory
    let conkyFileName = wConkyFileName dirName   
  
    (_, Just pipeout, _, _) <- 
        createProcess (proc cmdin ["-c", conkyFileName])
        { std_out = CreatePipe } 

    (_, _, _, ph)  <- 
        createProcess (proc cmdout []) 
        { std_in = UseHandle pipeout }
    
    -- just remove this waitForProcess line to detach dzen2
    _ <- waitForProcess ph

    putStr ""
{% endhighlight %}


You can see, how simple it is.
This would have <code>less</code> output similar to this below.

You can just remove this <code>waitForProcess</code> line
to detach dzen2. No need to fork

![Pipe: to Less][image-time-less]{: .img-responsive }

	Your wallpaper might be different than mine.

{% include toc/2017/04/pipe-and-fork-similar-02.html %}

-- -- --

### How does it works ?

First process create a new stdout handle <code>std_out = CreatePipe</code>.
And the second process use it as stdin feed <code>std_in = UseHandle pipeout</code>.

{% highlight haskell %}
    (_, Just pipeout, _, _) <- 
        createProcess (proc cmdin ["-c", conkyFileName])
        { std_out = CreatePipe } 

    (_, _, _, ph)  <- 
        createProcess (proc cmdout []) 
        { std_in = UseHandle pipeout }
{% endhighlight %}

-- -- --

### A Unidirectional Pipe from Internal Function

Using internal function as source feed
to external command is straight forward.
This should be self explanatory.

	Do not forget to flush.

**Source**:

*	[gitlab.com/.../dotfiles/.../haskell-03-process.hs][dotfiles-haskell-03-process]

{% highlight haskell %}
import System.Process
import System.IO

import Data.Time.LocalTime
import Data.Time.Format

import Control.Concurrent
import Control.Monad

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- wrap Funktion

wFormatTime :: FormatTime t => t -> String
wFormatTime myUtcTime = formatTime 
    Data.Time.Format.defaultTimeLocale myTimeFormat myUtcTime
  where myTimeFormat = "%a %b %d %H:%M:%S"

wSleep :: Int -> IO ()
wSleep mySecond = threadDelay (1000000 * mySecond)

generatedOutput pipein = do
     now <- getZonedTime
     let nowFmt = wFormatTime now

     hPutStrLn pipein nowFmt
     hFlush pipein

     wSleep 1

cmdout = "less" -- or dzen2

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- main

main = do
    (Just pipein, _, _, ph)  <- 
        createProcess (System.Process.proc cmdout []) 
        { std_in = CreatePipe }
    
    forever $ generatedOutput pipein
    hClose pipein
    
    putStr ""

{% endhighlight %}

{% include toc/2017/04/pipe-and-fork-similar-03.html %}

-- -- --

### How does it works ?

The same as previous.
But instead of reading from stdout <code>std_out</code>,
it is managed by internal process using <code>hPutStrLn</code>.

The second process use its own stdin <code>std_in = CreatePipe</code>.

{% highlight haskell %}
     now <- getZonedTime
     let nowFmt = wFormatTime now

     hPutStrLn pipein nowFmt
{% endhighlight %}

-- -- --

### Fork Overview

This step use internal function as source feed,
as continuation of previous step.

This step use dzen2, with complete parameters. 
This dzen2 is forked, running in the background.
Detached from the script,
no need to wait for dzen2 to finish the script.

**Source**:

*	[gitlab.com/.../dotfiles/.../haskell-05-fork.hs][dotfiles-haskell-05-fork]

{% highlight haskell %}
import System.Process
import System.IO
import System.Posix.Process

import Data.Time.LocalTime
import Data.Time.Format

import Control.Concurrent
import Control.Monad

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- wrap Funktion

cmdout = "dzen2"

getDzen2Parameters = [
      "-x", xpos,  "-y", ypos,
      "-w", width, "-h", height,
      "-fn", font,
      "-ta", "c",
      "-bg", bgcolor,
      "-fg", fgcolor,
      "-title-name", "dzentop"
    ]
  where    
    xpos    = "0"
    ypos    = "0"
    width   = "640"
    height  = "24"
    fgcolor = "#000000"
    bgcolor = "#ffffff"
    font    = "-*-fixed-medium-*-*-*-12-*-*-*-*-*-*-*"


wFormatTime :: FormatTime t => t -> String
wFormatTime myUtcTime = formatTime 
    Data.Time.Format.defaultTimeLocale myTimeFormat myUtcTime
  where myTimeFormat = "%a %b %d %H:%M:%S"



wSleep :: Int -> IO ()
wSleep mySecond = threadDelay (1000000 * mySecond)

generatedOutput pipein = do
     now <- getZonedTime
     let nowFmt = wFormatTime now

     hPutStrLn pipein nowFmt
     hFlush pipein

     wSleep 1

runDzen2 = do
    (Just pipein, _, _, ph)  <- 
        createProcess (proc cmdout getDzen2Parameters) 
        { std_in = CreatePipe }
    
    forever $ generatedOutput pipein
    
    hClose pipein

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- main

main = do
    -- remove all dzen2 instance
    system "pkill dzen2"

    -- run process in the background
    -- forkProcess is required since we use forever control
    forkProcess $ runDzen2
    
    putStr ""

{% endhighlight %}

This step also add system command that kill
any previous dzen2 instance. So it will be guaranteed,
that the dzen2 shown is coming from the latest script.

{% include toc/2017/04/pipe-and-fork-similar-05.html %}

-- -- --

### How does it works ?

Since we use <code>forever</code> control, it will block the process forever.
So we do have to detach it using <code>forkProcess</code>.

{% highlight haskell %}
    forkProcess $ runDzen2
{% endhighlight %}

-- -- --

### Polishing The Script

This step, we use conky again, as a source feed.
And also parameterized dzen2 as continuation of previous step.

This step add optional transset transparency,
detached from script. So we two forks, dzen and transset.

**Source**:

*	[gitlab.com/.../dotfiles/.../haskell-07-conky.hs][dotfiles-haskell-07-conky]

{% highlight haskell %}
import System.Process
import System.Directory
import System.IO
import System.Posix.Process
import System.Posix.Types

import Control.Concurrent
import Control.Monad

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- wrap Funktion

cmdin  = "conky"
cmdout = "dzen2"

-- Source directory is irrelevant in Haskell
-- but we'll do it anyway for the sake of learning
wConkyFileName :: String -> String
wConkyFileName dirName = dirName ++ "/../assets" ++ "/conky.lua"

getDzen2Parameters :: [String]
getDzen2Parameters = [
      "-x", xpos,  "-y", ypos,
      "-w", width, "-h", height,
      "-fn", font,
      "-ta", "c",
      "-bg", bgcolor,
      "-fg", fgcolor,
      "-title-name", "dzentop"
    ]
  where    
    xpos    = "0"
    ypos    = "0"
    width   = "640"
    height  = "24"
    fgcolor = "#000000"
    bgcolor = "#ffffff"
    font    = "-*-fixed-medium-*-*-*-12-*-*-*-*-*-*-*"

wSleep :: Int -> IO ()
wSleep mySecond = threadDelay (1000000 * mySecond)

detachDzen2 :: IO ()
detachDzen2 = do
    dirName <- getCurrentDirectory
    let conkyFileName = wConkyFileName dirName  

    (_, Just pipeout, _, _) <- 
        createProcess (proc cmdin ["-c", conkyFileName])
        { std_out = CreatePipe } 

    (_, _, _, ph)  <- 
        createProcess (proc cmdout getDzen2Parameters) 
        { std_in = UseHandle pipeout }
      
    hClose pipeout

detachTransset :: IO ProcessID
detachTransset = forkProcess $ do    
    wSleep 1
    system "transset .8 -n dzentop >/dev/null"
    return ()

-- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ----- ---
-- main

main = do
    -- remove all dzen2 instance
    system "pkill dzen2"

    -- run process in the background
    detachDzen2

    -- optional transparency
    detachTransset
    
    return ()

{% endhighlight %}

This would have <code>dzen2</code> output similar to this below.

![Pipe: to Dzen2][image-time-dzen]{: .img-responsive }

	You may use transset-df instead of transset.

{% include toc/2017/04/pipe-and-fork-similar-07.html %}

-- -- --

### How does it works ?

Since we do not use <code>forever</code> control,
we do not need to detach dzen2 using <code>forkProcess</code>.

But we still have to do <code>forkProcess</code>
for <code>transset</code>.
So it does not wait for <code>wSleep 1</code>.
Or to be precise, delayed in the background.

{% highlight haskell %}
import System.Posix.Types

detachTransset :: IO ProcessID
detachTransset = forkProcess $ do    
    wSleep 1
    system "transset .8 -n dzentop >/dev/null 2"
    return ()
{% endhighlight %}

-- -- --

### Lemonbar

I also provide Lemonbar, instead of Dzen2.
The code is very similar.

**Source**:
*	[gitlab.com/.../dotfiles/.../haskell-17-conky.hs][dotfiles-haskell-17-conky]

{% include toc/2017/04/pipe-and-fork-similar-17.html %}

-- -- --

### Coming up Next

There already an advance case of Pipe and Fork.
Multitier, and bidirectional.

*	[HerbstluftWM Event Idle in Haskell][local-haskell-idle]

-- -- --

There above are some simple codes I put together. 
I'm mostly posting codes so I won't have
any problems finding it in the future.

Thank you for reading.


[//]: <> ( -- -- -- links below -- -- -- )

{% assign asset_path = site.url | append: '/assets/posts/code/2017/04' %}
{% assign dotfiles_path = 'https://gitlab.com/epsi-rns/dotfiles/blob/master/standalone/pipe' %}

[dotfiles-conky]: {{ dotfiles_path }}/assets/conky.lua
[local-haskell-idle]:   {{ site.url }}/desktop/2017/06/18/herbstlustwm-event-idle-haskell.html

[dotfiles-haskell-01-basic]:   {{ dotfiles_path }}/haskell/haskell-01-basic.hs
[dotfiles-haskell-02-system]:  {{ dotfiles_path }}/haskell/haskell-02-system.hs
[dotfiles-haskell-02-process]: {{ dotfiles_path }}/haskell/haskell-02-process.hs
[dotfiles-haskell-03-process]: {{ dotfiles_path }}/haskell/haskell-03-process.hs
[dotfiles-haskell-05-fork]:    {{ dotfiles_path }}/haskell/haskell-05-fork.hs
[dotfiles-haskell-07-conky]:   {{ dotfiles_path }}/haskell/haskell-07-fork.hs
[dotfiles-haskell-17-conky]: {{ dotfiles_path }}/haskell/haskell-17-fork.hs

[image-time-less]:  {{ asset_path }}/pipe-time-less.png
[image-time-dzen]:  {{ asset_path }}/pipe-time-dzen.png
[image-time-basic]: {{ asset_path }}/pipe-basic-haskell.png

[local-overview]: {{ site.url }}/code/2017/04/23/overview-pipe-and-fork.html
[local-haskell-dollar]: {{ site.url }}/code/2016/05/14/haskell-dollar-syntax.html
