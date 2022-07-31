---
title: "Site Rewrite: From theme to lean"
date: 2021-12-07
author: CPunch
tags: ["hugo"]
repo: "https://github.com/CPunch/openpunk"
draft: false
---

I recently rewrote my Hugo site. I'd be lying if I said this was a spur-of-the-moment thing. The thing that really kicked me into finally starting from scratch was [this recent PR](https://github.com/XXIIVV/webring/pull/643) where I submitted my site to XXIIVV's webring. It was closed rather quickly, with the site ultimately being rejected from the webring. Neauoire's reasoning was simply put, it read:

{{< img pull.png "900x q100 jpg" "It's nice to see a PR from you, but your website in its current state does not qualify for the webring. There are no projects documented on-site(besides the site-builder itself), also using such a standard Hugo template goes against the hand-crafted requirement of the webring. Sorry, feel free to open another one when the site is further down the line" >}}

This didn't come as a surprise, in fact after I had submitted my PR I my had doubts. While I had made several modifications to the theme it was still fairly generic and 'template-y.' The theme in question was this [lovely theme by adityatelange](https://github.com/adityatelange/hugo-PaperMod). There was nothing inherently wrong with the theme, but it didn't quite fit my needs any more.

## The woes of using a theme

There were many, *many* features of that theme I didn't have a use for or just completely disagreed with. For example, the theme had integration for google analytics. My site has never and will never use such an analytics tool, instead relying on nginx log parsers like [GoAccess](https://goaccess.io). All of these unused features were just that, unused. This made my site full of features I didn't even know I had. My site should only have things that I explicitly enable and added.

Another thing about using a Hugo theme is it doesn't force you to *learn* Hugo. Immediately after starting the rewrite I had learned more in 20 minutes from reading the docs and writing my own layouts than I had in the year I spent modifying that theme. Being forced to really *know* your tooling is always a plus.

## The rewrite and some nice-to-haves

After deciding to get rid of my Hugo theme, I was forced to learn a lot more about Hugo than I had before. Writing my own [shortcodes](https://github.com/CPunch/openpunk/tree/main/layouts/shortcodes), [section parsers](https://github.com/CPunch/openpunk/tree/main/layouts/section), and of course the actual rendered HTML and CSS.

[One such shortcode](https://github.com/CPunch/openpunk/tree/main/layouts/shortcodes/img.html) enables me to compress images into a more friendly format for your web browser automagically. This made sharing photos (and ultimately [/places/](/places)) a lot more intuitive and friendly for my servers bandwidth and yours! Having to compress my images manually is such a chore, but Hugo and this shortcode makes it as easy as adding

```md
{{</* img image.png "q70 jpg" "alt-text" */>}}
```

This even lets me selectively compress images, ignoring images I'd like to keep raw.

Another main point was the lack of JavaScript. Not to say JS is inherently bad, but this keeps compatibility with some more privacy-focused web browsers like the Tor Browser. Speaking of Tor, my Tor mirror link is no longer hidden behind some obscure blog post, instead its in plain view as a clickable link in the site's footer.

## Addressing the PR

My immediate decision was made to rewrite my site from scratch, ditching the Hugo theme. I had debated writing my own static site generator but I did this a couple weeks before finals so I scrapped that almost immediately since I wanted to get this done rather quickly. Another note of importance was the lack of on-site project documentation. ~~So, I've created a new section called ['Software'](/software). This section is pretty lackluster right now, however eventually I plan to document *most* of my projects here. This also might force me to finish some projects so that they're in a nice presentable state.~~ (The ['Critiques'](/critiques) section has replaced this.)
