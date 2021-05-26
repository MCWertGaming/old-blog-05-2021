+++
title = "A look behind the scenes"
date = "2020-12-27"
author = "Damon Leven"
description = "This post gives a quick overview about the technical behind the scenes of OpenFoxBlog, as well as why I have chosen the tools I'm using."
+++

In this post, I will give some insight on the technical behind the scenes of OpenFoxBlog. This will include how it is technically accomplished and what problems I encountered on my way. later on, I will explain why I have chosen the way I have in matter of the tools and services.

[German version](/de/2020/ein-blick-hinter-die-kulissen/)

# How was this blog setup?
I already talked [in an earlier post](/2020/introduction/#how-did-this-blog-come-about), that I'm using Hugo as static website generator. But what does that mean? And why am I not just writing HTML files directly or am using something like medium or WordPress?

# Why not just medium or Wordpress?
First of all, I have a big problem with WordPress. Written in old PHP and lacking on proper security with a lot of problems and downsides. At the same time, I would have to buy or host a webserver to serve my site which would cost me money (even though, there are some free ones available I think). Because WordPress Isn't an option I would go with, there is only medium left. I haven't tried medium myself, but I read multiple times now, that people are criticizing on how medium tries to monetize their blog and posts. I really dislike that, because I want everyone to be able to read my blog without paying for it or having to look at dumb ads. Not to forget the tracking on free sites. In the end I chose to create my blog with Hugo and host it for free on GitHub. I want to get my thought onto the screen, not money into my pocket.

# What is a static website generator? 
before I get into the question, I quickly want to give a little insight into how my blog comes onto your screen. This blog is located on GitHub inside a [public repository](https://github.com/MCWertGaming/mcwertgaming.github.io) and gets automatically generated on every commit. This generation is done by the static website generator. You simply provide a theme to the generator as well as your posts in form of html or markdown pages and it will put the contents of your posts into the theme. The cool thing about this method is that the theme and in the end the design of every page will be exactly the same and you don't have to manually create templates to edit for your posts. At the same time, you can just roll out a new theme or edit it without the need of updating every page manually. The generator can even generate RSS feeds and homepages with your latest posts on it after a before defines template (even though, WordPress and similar software can do that as well).
But my personal favorite is the ability to just write you blog-posts in pure markdown. Markdown is a well-known and wide spread text formatting system and it provides the same options for formatting as html but with more user-friendly syntax. To visualize this quickly, here is an example: 

The rendered version:

> # A test
> This is a small test for the differences of markdown / html.
> - first part of a list
> - second part of a list
> ## smaller Heading
> 
> [A link](/)
> 
> ![A picture](/img/free-images/panorama.svg)
>
> **Bold**
>
> *Emphasized*
>
> ~~Strikethrough~~

The markdown code:

```markdown
# A test
This is a small test for the differences of markdown / html.
- first part of a list
- second part of a list
## smaller Heading

[A link](/)

![A picture](/img/free-images/panorama.svg)

**Bold**

*Emphasized*

~~Strikethrough~~
```

The same in html:

```html
<h1 id="a-test">A test</h1>
<p>This is a small test for the differences of markdown / html.</p>
<ul>
<li>first part of a list</li>
<li>second part of a list<h2 id="smaller-heading">smaller Heading</h2>
</li>
</ul>
<p><a href="/">A link</a></p>
<p><img src="/img/free-images/panorama.svg" alt="A picture"></p>
<p><strong>Bold</strong></p>
<p><em>Emphasized</em></p>
<p><del>Strikethrough</del></p>
```

We can clearly see, that markdown's syntax is a lot easier to write as well as a lot cleaner than the same in actual html. At the other hand, I really don't like web development and would like to avoid that at least a bit so I'm even more happy about this feature.

# But why Hugo?
I have already noted in my last post that I have used Jekyll on the old version of this blog but I experienced some problems and difficulties that would have cost me a lot of time. The first version of this blog was German only but I wanted to make this blog multilingual. As Jekyll don't supports using two languages well and at the same time, I didn't find a good theme with two languages already build in, I tried Hugo. Sure, Hugo does not offer a plugin system, but at least it has features for adding a second language built in. It also handles themes a lot better than Jekyll making it extremely easy to add themes and even update them without the need of migrating the whole site. One last thing, that made me switch, was that I found the theme I'm currently using immediately. I like It a lot more than all other themes, even on Jekyll I found.

# Problems with Hugo
Like at probably most things at programming Hugo created some problems. The first thing I encountered with was the way Hugo creates web files. Instead of creating normal html files with the site name it creates folders with the site name and places an index.html inside that. At first this is a bit weird (at least it was for me!) but makes sense in the end. Because of that the browser hides the URL has no .html extension and makes the URL look a bit cleaner.
All other things went without problems at all. I could just create all required sites with their content and Hugo did everything other for me.
At the end there was actually one a bit bigger problem. The default RSS feed configuration didn't suite my needs. It just includes the first paragraph inside the feed instead of just providing the full post. After changing that, the markdown converter of Hugo included illegal character inside of the xml file and made it corrupt. I will make a dedicated post on how I fixed that real soon. stay tuned!

Thats for today's post. I hope you enjoyed this little behind the scenes and with you some happy last Christmas days as well as a great new year!

#### Happy coding!

Sources of used software and assets on this site can be found [here](/about/#software-used-on-this-site).
