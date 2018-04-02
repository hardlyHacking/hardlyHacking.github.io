---
layout: post
title: Setting Up My Ghost Blog
date: '2015-11-21 23:38:46'
tags:
- blogging
- ghost-tag
---

I'm liking Ghost tremendously. It's entirely customizable, and I'm a sufficiently late adopter that there is plenty of documentation available for nearly all the issues I face.

That said, there are inevitably somethings that get out of date, and I like to document the issues I ran into, if for no other reason than to help myself in the future.

With that, here are a couple easy things I did on my blog.

<h2 id="settinghome" name="settinghome">Setting 'Home'</h2>

I deployed my blog on Digital Ocean, and whenever I clicked on the 'Home' button or the favicon on the top, it would redirect me to a page that something like "Oops! Looks like you haven't configured your 'config.js' file!".

It's fairly simple:

1. Copy the the ```config.example.js``` file to create a ```config.js``` file located at the root ghost directory.
2. Edit the URL to point to your own blog
3. **Restart your droplet**. Remember to do a ```sudo service ghost restart```

<h2 id="customfavicon" name="customfavicon">Custom Favicon</h2>

I really quite like the Ghost favicon, and in general I'm pretty comfortable with it. Still, I ended up loading my own because... why not? ```:)```

This is really quite self explanatory, and there are plenty of [guides](http://ghostforbeginners.com/how-to-add-a-favicon-to-your-ghost-blog/) on how to do this.

<h2 id="rss" name="rss">RSS</h2>

Wow Ghost is awesome. I literally had *nothing* to do. Publish my first blog post, go to Feedly, search for my website, and BAM! It's right there on my phone!

![](/images/2015/08/IMG_0221.PNG)

<h2 id="mailconfiguration" name="mailconfiguration">Mail Configuration</h2>

[Ghost documentation](http://support.ghost.org/mail) provides the answer here for customizing mail. Simply follow along.

This depends on who your DNS provider is; I used [gandi](https://www.gandi.net/).

<h2 id="syntaxhighlighting" name="syntaxhighlighting">Syntax Highlighting</h2>

I ended up using [prism.js](http://prismjs.com/), which supports a fair number of languages along with features like line numbering, displaying the language on the corner, and various CSS themes.

[This](http://www.incrediblemolk.com/add-code-highlighting-to-your-ghost-blog/) blog post details the procedure quite well, though I ended up making some changes. The process of adding Prism itself is quite simple:

* Add the relavent css and js files into the ```/ghost/themes/[theme name]/assets/```, where there is both a ```css``` folder and a ```js``` folder
* Edit your ```default.hbs``` file, located at ```/ghost/themes/[theme name]/```

If you need more details, the blog post linked above provides them.

One thing to note, though, is that the markdown does not always work. And furthermore, if you want line numbers, I find that it's simply much easier to use literal HTML blocks. [This](http://stackoverflow.com/questions/18391425/markup-not-showing-up-using-prism-js) Stack Overflow post details some confusion that I had.

The <code class="language-html">&lt;pre&gt;</code> tag should have the <code class="language-html">class="line-numbers"</code> class, and the <code class="language-html">&lt;code&gt;</code> tag should have the <code class="language-html">class="language-*"</code> class.

Rather than trying to force this into an awkward Markdown syntax, I find using HTML is much easier.

Additionally, line numbers are handled beautifully and copying code is a breeze.

![](/images/2015/08/out.gif)

<h2 id="fullmarkdown" name="fullmarkdown">Full Markdown</h2>

In case you didn't know, Ghost [does not support full Markdown](https://ghost.org/forum/using-ghost/257-markdown-support-table/). This problem was really annoying for me - features like tables and such are just far easier to do in Markdown; in fact, it's probably the single most important feature.

And even worse, I had recently found [Haroopad](http://pad.haroopress.com/), which seems absolutely amazing. Ghost *seriously* needs Github-flavored Markdown. It would solve so many issues.

Anyways, I finally found the [table extension](https://github.com/showdownjs/table-extension), but to be honest, I don't write *that* many tables in my blog posts. And between Prism.js and the ease of just creating the darn table (or, in the worst cases, writing it in Lightpaper or Haroopad and exporting it), it didn't seem worth it to go through the setup.

Of course, if they had the full Github-flavored Markdown from the beginning, we wouldn't need Prism or any of these other hacks. But given that my hacks are already in place and running...

<h2 id="toc" name="toc">Table of Contents</h2>

I sometimes tend to write really long posts, and it's useful to have a table of contents. [This](http://dannynelson.io/creating-a-table-of-contents-in-ghost/) blog post details how to write the more classic table of contents at the top of the blog post.

And that's nice, don't get me wrong. But what I really wanted was [the side navbar](http://getbootstrap.com/getting-started/) like the one on the Bootstrap page that shows which section you're currently viewing, and, as you scroll down, automatically updates.

At last I found [Tocify](http://gregfranko.com/jquery.tocify.js/) which is *exactly* what I was looking for!

Couple quick tips that helped me out:

* Tocify *automagically* generates the table of contents; it wasn't clear from the site, but you should merely create an *empty* div for it to fill up.
* Don't put said div in the ```default.hbs```, or it will show up in every page, including the index. I found it was far more useful to place it in ```post.hbs```, since you'll just want it in the actual blog posts.
* Watch out for CSS madness. Bootstrap (one of the defaults Tocify gravitates towards) is totally incompatible with Casper, Ghost's default theme.

I ended up mucking with the source code a bit, as well as the CSS, for a rough version 1.0 to get something working. The [annotated source code](http://gregfranko.com/jquery.tocify.js/docs/jquery.tocify.html) for tocify shows three key lines:

```javascript
else if(this.options.theme === "bootstrap") {
  this.element.find(headerClass + "," + subheaderClass).addClass("nav nav-list");
  this.focusClass = "active";
}
```

If the theme is ```"bootstrap"```, it adds the CSS classes ```active```, ```nav```, and ```nav-list``` (remember this is Bootstrap 2.3.2 - as ```nav-list``` has been deprecated in Bootstrap 3+). So I just downloaded the [Bootstrap source code](http://getbootstrap.com/2.3.2/), plucked out the lines relevant to just those three classes, stuck those into a new asset, and had my ```default.hbs``` file import it.

Voila! Super hacky, but the CSS conflicts were gone, and everything seemed to be working just fine.

If I ever get a super nifty solution, I'll write an updated blog post about it then. ```:)```

<h2 id="copycode" name="copycode">Copy Code</h2>

Since this is a coding blog, I'll be posting helpful code snippets with every post (pretty much). It'd be nice to have a helpful copy button on the top right of all the code blocks, so the user would automatically get the code into their clipboard without any hassle.

The problem is that, for legacy security reasons, it's basically [impossible to do this without Flash](http://stackoverflow.com/questions/6355300/copy-to-clipboard-without-flash). Ah, but you say - if *reading* from a user's clipboard may reveal sensitive information, why not *write* to it? Again, legacy security reasons. Most browsers haven't changed this.

Still, I didn't want to use Flash. Eventually, I found this [magical Stack Overflow post](http://stackoverflow.com/questions/17527870/how-does-trello-access-the-users-clipboard) that illustrates how Trello does something that very closely approximates this without Flash! I ended up using the trick described there.

But, (as seen in the GIF above) since Prism.js's syntax highlighting actually prevents copying number lines, I decided that simply pre-selecting the text wasn't worth the effort, and using a Flash solution was too disgusting. So, after spending a day or two putting it in, I decided to take it out.

UPDATE!

The greatest web browser ever has published an update that finally allows (in newer versions of Firefox - hopefully Chrome and others will follow suit soon) copying without any Flash! Natively! (Update 2016, they have done so!) [Voila](https://hacks.mozilla.org/2015/09/flash-free-clipboard-for-the-web/)!

Given my long-standing support for Mozilla and everything they do, I'm officially using this solution instead. The blog post has a great tutorial and explanation for how to use this API.

There was actually some trickery (perhaps because I'm a javascript n00b) to being able to select the code, as the normal ```.select()``` doesn't work with ```<pre>``` and ```<code>``` blocks as it does with textareas, but [this](http://stackoverflow.com/questions/985272/selecting-text-in-an-element-akin-to-highlighting-with-your-mouse) stack overflow post covers everything you need to know. And because of the latest version of Ghost's Code Injection feature, I didn't even have to change my templates! I simply appended the following to it:

```javascript
<script>
function SelectText(element) {
	var doc = document, range, selection;

	if (doc.body.createTextRange) {
		range = document.body.createTextRange();
		range.moveToElementText(text);
		range.select();
	} else if (window.getSelection) {
		selection = window.getSelection();
		range = document.createRange();
		range.selectNodeContents(element)
		selection.removeAllRanges();
		selection.addRange(range);
		document.execCommand('copy');
		selection.removeAllRanges();
	}
}

var snippets = document.querySelectorAll('pre');
Array.from(snippets).map(function(element) {
	var input = element.querySelector('code');

    // Create the copy button
    var newIcon = document.createElement('i');
    newIcon.className = 'fa fa-clipboard fa-lg';
    var newSpan = document.createElement('span');
    newSpan.className = 'btn-clipboard';
    newSpan.appendChild(newIcon);
    var newDiv = document.createElement('div');
    newDiv.className = 'zero-clipboard';
    newDiv.appendChild(newSpan);

    // Insert it right above the code block
    var parent = element.parentNode;
    parent.insertBefore(newDiv, element);
    
	newDiv.addEventListener('click', function(event) {
		event.preventDefault();
		SelectText(input);
	});
});
</script>
```

This finds all instances of code and dynamically creates a new span element that contains an image courtesy of [Font Awesome](https://fortawesome.github.io/Font-Awesome/). For those curious, I mimic the [Bootstrap](http://getbootstrap.com/css/#overview) behavior with the copy button, only I replaced the word with an icon.

So now, whenever you want to copy a long block of code, simply click the copy icon at the top right, and it copies to your clipboard! Amazing.

<h2 id="tbd" name="tbd">To Be Done</h2>

I still have a lot to do and add, but this was the bulk of what was done before I really started publicizing my blog.

#### Comments

I purposefully left out adding a comments section to the blog. I can't really decide whether to put something to enable them or not. I doubt this blog will ever really get any significant traffic, so opening up a section to allow trolling and snide remarks seems unwise. The purpose of this blog was never to remain secret, but it also was not to advertise as a blog. It was mostly for myself to keep track of the various things I do and look back on them later.

#### Search

Searching through posts has gotten slightly tedious; at times I write super long winded articles, and knowing exactly where I said what somtimes gets crazy, particularly if I speak on a topic repeatedly. But Ghost's tagging system has bridged this greatly, so until it gets more annoying, I probably won't do anything about it.

<h2 id="update" name="update">Update Dec 2015</h2>

So I upgraded ghost, and boy was it long overdue. I went from 0.5 to 0.7.2. There's a lot of added support for code injection, exporting, and many other things. Overall, the editing interface looks *much* cleaner, which is great. But, as things happen, a *lot* of stuff broke. The upgrade process broke my table of contents via Tocify as well as my code highlighting, since Prism isn't served out of a CDN.

I decided I don't want to have to redo the copy-pasting of Prism every time I upgrade, so I'm sticking with [Highlight.js]() as suggested in [this blog post](https://www.timofejew.com/fixing-ghost-casper-theme/). Now with the code injection, I just include a link to the CDN at the top, and no more mucking with themes. So hopefully this aspect of the blog will never break again! One notable issue is that, unlike Prism, they purposefully [do not support line numbers](http://highlightjs.readthedocs.org/en/latest/line-numbers.html) due to simplicity being a core value of theirs. I'm not sure how I feel about this, to be honest. But in reality, I don't paste such long code snippets to really require this (yet). If such a thing should occur, I will update this section accordingly. In addition to [Prism](http://envyandroid.com/syntax-highlighting-in-ghost/), I also considered [Prettify](http://blog.quanttrader.org/syntax-highlighting-in-ghost/), but I ended up sticking with Highlight.

Tocify was fairly easy, since it is CDN-based. One tricky thing (I totally forgot, since it's been so long) is that tocify depends on JQuery UI. But that's not too bad.

<h2 id="update" name="update">Update Feb 2016</h2>

Added SSL support to the blog, and am now successfully redirecting HTTP to HTTPS.

There are [lots of](http://support.ghost.org/setup-ssl-self-hosted-ghost/) articles [helping you](http://nginx.org/en/docs/http/configuring_https_servers.html) do this, and I used [LetsEncrypt](https://letsencrypt.org/howitworks/) for the certificate.

<h2 id="update2" name="update2">Update May 2016</h2>

Subscribing to Google Analytics was way easier than I thought it would be. I'm apparently averaging ~100 unique visitors per month, which, honestly, seems way too high. I wish I could find out how many of these are bots, but there you have it. Part of me cringes at this, since I really didn't want to get involved in the rat-race of how many people read my blog. I'm mostly doing this for me.

Google Webmaster Tools was similarly easy, and by this point there are plenty of [Ghost-specific guides](http://academy.ghost.org/how-do-i-submit-my-sitemap-to-google/) out there to help whoever wants to adopt.

The only main hiccup was the whole email deal - although there is a [guide](http://support.ghost.org/mail), Mailgun doesn't play nice with Digital Ocean because their documentation is totally wrong. Luckily, someone else wrote a blog post about Mailgun + Ghost + Digital Ocean, and [his post](http://code.krister.ee/mailgun-digitalocean/) got me through.
