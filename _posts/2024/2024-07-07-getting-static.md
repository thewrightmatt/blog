---
layout: post
title: Getting Static
description: Taking a step backward to take a few steps forward. Rather than using a fancy CMS, I'm back to using static pages built with some magic.
date:   2024-07-07 15:00:00 -0600 
image:  '/images/2024/7/header.jpg'
tags:   [blog]
---

# Another Day, Another Blog(ing platform)

Well, I've gone from Wordpress (actually Xanga initially if I'm being honest), to Ghost and now to Jekyll hosted on GitHub Pages. It's a different process, but cheaper!

I've been running two different sites on [Lightsail]({{site.baseurl}}/back-again). One site was a Wordpress site with just a single page pointing to a Discord link which was a bit of a waste, the other site was my rarely updated blog using [Ghost](https://ghost.org/). While neither was a major charge, after a few years it adds up. 

The Wordpress site could have easily just been a static HTML page and after doing some research I found Jekyll! Even better, I found a free theme that would let me build a site similar to the simple Wordpress page with very little adjustment. After about a day or two of tweaking the site and GitHub, I was able to swap things over and delete the Lightsail instance.

While I really like the sleekness of Ghost, I wanted to migrate my blog to something cheaper as well since it was rarely used. After getting my hands a little dirty migrating from the Wordpress site to Jekyll, I felt I could manage. Honestly, the hardest part was finding a theme I liked as well as moving all my old posts over. Sure, there's supposedly a migration tool but I have no idea how it would work with GitHub Actions building the pages (and not running locally) so I skipped that step and did it all manually.

Now, hopefully once I get this post up I'll be done using the subdomain and fully migrated over to thewrightmatt.com.

I also want to try spinning up a local instance of Ghost to eventually start up a gaming fan site. Having placed more with Docker, I think I may be able to self-host at least as a trial run before potentially having to purchase hosting (again).

## To Do
One of my complaints with Ghost (though looking back maybe it was Ghost and Firefox?) was that spellcheck would only work half the time. Now with these posts being build by GitHub Actions, I should be able to [take a cue from this blog](https://swild.dev/self-hosting/github-spellcheck-lint-action/) and integrate spellcheck into the process.

I gotta see if I can automate the post image bit. Ghost was nice in that you could just search for an image and it'd pull one from [unsplash](https://unsplash.com/) and set it as the image associated with the post. The current workflow is going to unsplash, downloading, editing/cropping and then uploading to my images folder.

Last up I need to see if there's a way to fix the 'pages' automatically getting added to the top navigation bar. There's probably an easy way to do it, I just gotta dig into Jekyll some more. I have a few posts that I want to more or less be pages that reference each other, but not have them listed in the top header.

## One Last Thing
In keeping with tradition, I did get a new certificate (well, really I just renewed it). Terraform Associate 003! Mostly just for work, next up will be some Github certifications to help with my most recent job title.

{:refdef: style="text-align: center;"}
![Terraform Associate]({{site.baseurl}}/images/2024/7/tf-assc.png)
*Behold!*
{: refdef}

As always, there's much more going on in life but that's all for now.