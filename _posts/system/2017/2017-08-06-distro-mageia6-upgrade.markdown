---
layout     : post
title      : "Distribution - Mageia 6 Upgrade"
date       : 2017-08-06 09:45:15 +0700
categories : system
tags       : [mageia, distro, package manager]
keywords   : [urpmi]
author     : epsi

excerpt:
  Mageia 6 Upgrade Log.

related_link_ids: 
  - 17083145  # Docker Package Manager Summary
  - 17080845  # Debian Devuan Migration
  - 17080745  # Manjaro Artix Migration
  - 17080545  # GRUB2 BTRFS Support
  - 17080345  # openSUSE Tumbleweed Install
  - 17080245  # Fedora 23 to 26 Install
  - 17080145  # Manjaro OpenRC Issues
  - 14050934  # Mageia Experiment
---

### Preface

After painting my fence and ladder at home with brown-violet colour,
I finally got few hours time to continue my genealogy project tonight, 
but the Mageia Linux at home needed to upgraded. Therefore I did.

[![Mageia 6 Upgrade][image-ss-mageia]{: .img-responsive }][photo-ss-mageia]

*sigh* I need to postpone my genealogy project.

For a more complete Mageia review,
can be found in my old Mageia 4 article.
This article contain some cool screenshots.

*	[Mageia 4 Experiment][local-mageia-4]

-- -- --

### Repository

Mageia packages can be found in

*	<http://svnweb.mageia.org/packages/cauldron/>

No official git yet, only project stage

*	<http://gitweb.mageia.org/>


-- -- --

### Just a few command line

This is the process

{% highlight bash %}
$ su
# urpmi.removemedia -a
# urpmi.addmedia --distrib --mirrorlist 'http://mirrors.mageia.org/api/mageia.6.$ARCH.list'
# urpmi --replacefiles --auto-update --auto
# urpme --auto-orphans
{% endhighlight %}

I have used Mageia since 2014.
And this old article has a more complete review.

*	<http://epsi-rns.github.io/system/2014/05/09/mageia-experiment.html>

I'm just showing that I'm still single.
I ain't got  nowhere tonight but my desktop.

ihavenolife
saturdaynightfever
﻿
Update: It is 02:55 AM on sunday morning. And the upgrade haven't finished yet.

-- -- --

### Notes

A few notes on post install

(1)	DNF

Must install manually

{% highlight bash %}
$ sudo urpmi dnf
{% endhighlight %}

Other than that, dnf works like a charm.

[![Mageia 6 DNF][image-ss-mageia-dnf]{: .img-responsive }][photo-ss-mageia-dnf]

(2)	Compiz Issue

After install gnome lost all panel.
Therefore I go back to XFCE4, which is lost window border.

And "$ xfwm4 -- replace" also does not work since compiz has the deafult Window Manager.

The next thing I did is "$ sudo urpme compiz" and logout.

{% highlight bash %}
$ sudo urpme compiz
{% endhighlight %}

After this procedure both gnome-shell and XFCE4 works normal as it should be.

(3)	Plasma5

Must install manually

{% highlight bash %}
$ sudo urpmi task-plasma5
{% endhighlight %}

-- -- --

### Update

	First Thing First

{% highlight bash %}
$ sudo urmi --auto-update
{% endhighlight %}

-- -- --

Thank you for reading

[//]: <> ( -- -- -- links below -- -- -- )

{% assign system_path = 'https://epsi-rns.github.io/assets-system' %}
{% assign asset_post  = system_path | append: '/2017/08/distro-mageia' %}

[local-mageia-4]: {{ site.url }}/system/2014/05/09/mageia-experiment.html

[image-ss-mageia]: {{ asset_post }}/release-6-upgrade.png
[photo-ss-mageia]: https://photos.google.com/share/AF1QipMO53TtSJVXrkn8R0s4wre4QWgX7_G5CoaSkFMneVHFp9Tu5STBmdjW3M3fpA2eEw/photo/AF1QipNj2Y-cqsOhW3IrV5C_uaCOQQ6tSiXaTJYSOwNI?key=WGIySDVOaVpibkJCRkV5NWVZUUs3UnNLNHR1MVpn

[image-ss-mageia-dnf]: {{ asset_post }}/dnf.png
[photo-ss-mageia-dnf]: https://photos.google.com/share/AF1QipMO53TtSJVXrkn8R0s4wre4QWgX7_G5CoaSkFMneVHFp9Tu5STBmdjW3M3fpA2eEw/photo/AF1QipN_L4dszGn4GGFS4B7_cVkr1GLy5tpbD5AAAFbd?key=WGIySDVOaVpibkJCRkV5NWVZUUs3UnNLNHR1MVpn
