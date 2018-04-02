---
layout: post
title: Updates on Rooting the Galaxy S5
date: '2014-11-27 05:01:12'
tags:
- android
- galaxy-s5
---

### Switching to S5

I got my Galaxy S5 in June back when my S3 stopped turning on altogether (I didn't have insurance). Now I've got the much sturdier S5, which I'm quite happy with, and the insurance that covers everything to boot (water damage, theft, just general stupidity).

And overall it's a great phone - I'm really happy with it, truly. It's a market improvement over the S3, though this could be just because skipping generations always has this effect. And to their credit, Samsung has improved TouchWiz remarkably. But it's not enough.

Time to time, I find it to be quite flaky. I don't like the laundry list of pre-installed applications that I find taking up both disk and memory, and overall I found myself wanting a cleaner android environment.

So I decided to root my phone. I thought this would be something that took many hours, but it was surprisingly painless and easy. The problem was not in the actual rooting process, but rather finding a guide that wasn't so outdated!

This post should hopefully serve as a series of warnings of what not to do: namely, all the problems I had.

### The Rooting Process

Things you will need:

1. A Windows PC - please, please, please. I thought I could do this on the Mac / Linux; I could not. It's just *so* much easier on a Windows. If you can do this, more power to you. But I could not.

2. A USB 2.0 cable to connect your S5 to said Windows PC

3. A working internet connection fast enough to download files as big as 20MB.

And that's it! The process is so painless and simple that it took me (once I found the correct guide) 10 minutes. I didn't even back up my phone.

The best guide by far is the one at [Android Central](http://androidcentral.us/2014/04/how-to-root-t-mobile-samsung-galaxy-s5-sm-g900t/) **not** [galaxys5root.com](http://galaxys5root.com/). I found the ones at galaxys5root to be rather dated. Regardless of which guide you end up choosing, you'll have to download three key pieces of software.

Since there are plenty of guides that are far better than any I could write, I'm not going to re-write a guide here. Instead, I'll offer instead a few words of warning and explain the pitfalls I fell into.

**Odin**

[Odin](http://en.softonic.com/s/odin-3-v-1.85) is a Windows application to flash applications to your phone. I used Odin 3 v 1.85 and it worked the best. I tried to use other versions, didn't seem to work out. Based on my research, the general rule of thumb seems to be that the latest version is almost always the one you want to use.

**Custom Recovery Loader**

I used Philz Touch recovery loader, a fairly popular one. If you do, the key mistake I made was using ```philz_touch_6.26.2-klte.tar.md5``` instead of ```philz_touch_6.26.6-klte.tar.md5```. Use the latter, 6.26.6, and not 6.26.2. I spent almost an hour trying to figure out what went wrong; then I stumbled upon this version and everything worked like a charm!

**Unlock Your Screen**

In almost every guide you'll see, there'll be a step where you copy some zip file from your Desktop or wherever to the root directory of your phone. The problem for me was whenever I navigated to my phone, there was nothing there. No other folders. And when I tried to paste the file, it would say something to the effect of "Device has been disconnected or is not responding."

Felt rather silly, but my screen was locked. Unlock the screen and, voila! All the folders appear and everything works as it should.

**Developer Options**

This step was also rather non-intuitive. You need to make sure the USB debugging option is selected on your phone. Only you won't find that anywhere. It's in a "Developer Options" section that's not available by default.

You unlock this by going to the "About Device" section in the System Settings, and tapping on it 7 times in a row. And then you can enable this option.

### Conclusion

Rooting honestly is very simple, and well worth it. Rid yourself of bloatware, install all the custom roms you like, overclock your CPU, get more RAM, etc. One I discovered the Android Central guide, it took me less than 10 minutes to root my phone.

All in all, a decent use of a Wednesday evening!