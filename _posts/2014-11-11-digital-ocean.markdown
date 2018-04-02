---
layout: post
title: Digital Ocean
date: '2014-11-11 09:33:18'
tags:
- digital-ocean
---

So I've finally made the switch to Digial Ocean, and I couldn't be happier. The setup of this blog (I timed it) took `12:30`, and I'm fairly new to this whole ordeal. So in under 15 minutes, I had done the following:

* Set up a Digital Ocean account
* Made a 'droplet' for Ghost blog
* Configured DNS to point to my own domain name
* Configured Ghost on my VPS

Not bad! Of course, the DNS didn't actually change for several hours afterwards, but the point is that in less than 15 minutes, I was already typing up my first blog post *on the VPS*.

I can't say whether it's the actual set up process or that I've simply become more experienced with setting up VPS's and changing DNS or even just getting accustomed to [gandi.net](gandi.net) - it could be a whole host of things honestly - but I don't remember github.io or Dreamhost going *nearly* as smoothly.

Dreamhost was my first time doing something like this, and it took a little over two hours. And the documentation was... ugly. It's not that it wasn't complete - it definitely was - it's just that at 3AM when you're frustrated and sleepy, this kind of thing is the *last* thing you want to see.

![](/content/images/2014/11/DreamhostTutorial.jpg)

Like I said, it gets the job done... It's just not easy. Contrast that with the Dreamhost version.

![](/content/images/2014/11/GhostTutorial.jpg)

Introductory tutorials are for beginners who have *no* idea what they're doing. You can't go wrong with catering to the lowest common denominator - the college freshman who just discovered coding at 4AM and is cranky is generally a good bet.

Like I said, DreamHost was my first experience ever with this sort of thing, and looking back, I chuckle at the mistakes I made. The docs they have are complete. But that doesn't help put to rest the long nights of frustration, and comparitively, Digital Ocean is an absolute breeze.

Perhaps it's also because I'm a late adopter to Digital Ocean. I've heard that in the beginning, Digital Ocean was also a mess. Regardless, the documentation is fantastic *now*. It's so brainless that even *I* managed without any difficulties. It's step by step with pictures and as simple as can be - just how documentation should be, quite frankly.

And don't even get me started on Github.io. Between the early adoption and learning Jekyll and the horrible documentation, I never looked back in that direction again. I'm told the documentation improved significantly since then.

And that was before this pleasant surprise:

######Dreamhost

	64 bytes from 173.236.188.12: icmp_seq=0 ttl=47 time=107.135 ms
	64 bytes from 173.236.188.12: icmp_seq=1 ttl=47 time=114.784 ms
	64 bytes from 173.236.188.12: icmp_seq=2 ttl=47 time=99.151 ms
	64 bytes from 173.236.188.12: icmp_seq=3 ttl=47 time=93.053 ms
	64 bytes from 173.236.188.12: icmp_seq=4 ttl=47 time=99.623 ms
	64 bytes from 173.236.188.12: icmp_seq=5 ttl=47 time=138.444 ms

######Github IO

	64 bytes from 192.30.252.154: icmp_seq=0 ttl=51 time=100.028 ms
	64 bytes from 192.30.252.154: icmp_seq=1 ttl=51 time=99.137 ms
	64 bytes from 192.30.252.154: icmp_seq=2 ttl=51 time=99.678 ms
	64 bytes from 192.30.252.154: icmp_seq=3 ttl=51 time=100.926 ms
	64 bytes from 192.30.252.154: icmp_seq=4 ttl=51 time=101.994 ms
	64 bytes from 192.30.252.154: icmp_seq=5 ttl=51 time=103.038 ms

######Digital Ocean

	64 bytes from 162.243.155.22: icmp_seq=0 ttl=53 time=56.726 ms
	64 bytes from 162.243.155.22: icmp_seq=1 ttl=53 time=25.045 ms
	64 bytes from 162.243.155.22: icmp_seq=2 ttl=53 time=29.548 ms
	64 bytes from 162.243.155.22: icmp_seq=3 ttl=53 time=28.834 ms
	64 bytes from 162.243.155.22: icmp_seq=4 ttl=53 time=35.222 ms
	64 bytes from 162.243.155.22: icmp_seq=5 ttl=53 time=35.824 ms
	64 bytes from 162.243.155.22: icmp_seq=6 ttl=53 time=27.656 ms
	64 bytes from 162.243.155.22: icmp_seq=7 ttl=53 time=28.982 ms

Of course, I'm in San Francisco at the time of testing, and I selected the San Francisco hosting location for my blog, so it had better blow the others out of the water.

Still, I'd be lying if I said 3X the speeds of the competitors was a bad thing. `:)`