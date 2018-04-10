---
layout: post
title: Moving My Blog to Github
date: '2018-04-10 01:58:24'
tags:
- blog
- github
---

Way back in 2015, I described how I moved my blog over to [Ghost](https://notablog.xyz/2015/11/21/setting-up-my-ghost-blog.html). Until then, for small static websites, I had been using [Jekyll](https://jekyllrb.com/). Ghost had nearly everything I wanted - great design, fancy UI editing, and a reasonable blend of customization options and self-hosting ability. Ultimately, this was a huge mistake.

My previous set up was just using [Github Pages](https://pages.github.com/) with Jekyll. It had out of the box support for github-style syntax hilighting for code along with far more customization, but also various plug-in themes.

But more than anything, there's one significant upside to Github Pages - K.I.S.S.

I find that context-switching for my side projects and maintenance has almost always been responsible for premature abandonment. When things become complex, I find I need long runbook-style documentation just to keep track of all the various moving pieces, and the process of familiarizing myself with these documents and context switching takes far too long. These days I find I'm able to dedicate every other weekend to a side project, with the others going to a mix of work from my actual job and errands. This puts time at a premium and my patience for context switching, where it can be avoided, extremely short.

As of the writing of this post, my blog is currently 4 posts behind the originally told promise of 1 pst per month. (3 excluding this one.) And in the past couple months, I've run into constant issues w/ Digital Ocean instance upgrades, NginX / Apache issues (running multiple subdomains off of the same box for multiple websites), and Ghost / NPM versioning issues. These issues almost always take several hours to debug, and ultimately completely drain my motivation. Ironically, I've found that a side project requires far more focus than my regular job precisely because time is at such a premium. I can't afford to spend a weekend examining how a particular technology works, or the project itself will never ship. (Unless the side-exploration itself was the goal.)

Relying on nothing but Git and Jekyll, which is just a wrapper around markdown, ensures I have to waste basically no time at all.

More than anything else, though, the workflow is extremely simple. I don't have to worry about repeatedly renewing my SSL cert, my digital ocean box uptime, or Nginx issues. Github pages' workflow is super smooth, and Cloudflare makes it very simple to have [multiple submdomains](https://stackoverflow.com/questions/10685961/multiple-github-pages-and-custom-domains-via-dns?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa) pointing to the same [custom domain name](https://help.github.com/articles/setting-up-a-custom-subdomain/).

Moving from digital ocean to Github pages was also super easy thanks to [Frozen Flask](https://pythonhosted.org/Frozen-Flask/) which converted my very simple flask websites into a static one.

Ultiamtely, I'm hoping that this will be the last move for my blog. Using Pages and Cloudlfare, I'm hoping, will allow me to focus on writing more than all the other distractions.