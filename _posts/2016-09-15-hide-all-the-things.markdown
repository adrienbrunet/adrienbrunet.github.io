---
layout: post
title: Hide all the things
categories: [stuff]
tags: [code, general]
comments: true
description: Want to hide tiny part of well know websites? Here is a trick.
---

Do you want to hide parts of facebook or twitter? Have a look.

<p align="center"><img src="/assets/images/hide-all-the-things.jpg" alt="Hide all the things"></p>

I recently discovered the chrome extension called "Stylish". It allows you to add an extra layer of css on top of the css website. The css being what controls the style of your page.
It means you can tampered any website style. You always dreamed of a pink facebook bar?

First of all... Install Stylish:
  [Their official website](https://userstyles.org/) (really sorry, it's quite ugly)


So far, it's easy, right? You shoud have it installed by now.

You can now tweak any websites to your will with it. Let try with an example!

# Facebook

<p align="center"><img src="/assets/images/facebook.png" alt="Hide all the things"></p>

On facebook, I'm friend with a large amount of people (I barely know half of them) and I haven't spoken to most of them for years.

And yet, I can see them in my news feed. You could say that's a good thing.
What's the matter?

Well, my news feed is getting jammed with posts always more stupid than the previous one, click bait related or 2-day-old internet jokes are getting boring. And I don't care seing every news from all this people.

I could unfriend people I'm no longer feeling confortable with. Or tune my news feed to see less stuff from them. But I'd feel gulty.

And, come on, even if it's a two clicks actions, I'm not gonna spend hours doing that 500 times.

I was looking for a better solution but then I realised I don't need that news feed at all.

What I need:

- Use the chat aka messenger
- being able to see any notification and jump to the post
- see the content of profiles/groups

Well, I don't need the news feed. And I'm not gonna waste my time scrolling it anymore.
Here is the template you can use to hide facebook news feed`using you newly installed app:

(click on your extension icon, then on write style for *website name*, copy paste the following code, add a name on the top left, save, rince, repeat)

```css
/* Hide newsfeed */
#topnews_main_stream_408239535924329 { visibility: hidden; }

/* Remove trending stories and suggested groups */
#pagelet_trending_tags_and_topics, #pagelet_ego_pane { display: none; } 
```

:tada: Tadaaaa :tada:

# Wired

One more example and I let you do your own.
Wired keeps driving me crazy with its anti ad-blocker page. The benefits of an ad-blocker, or the fact we should or not use it won't be address here.

The fact is: I don't want to see it.

Here is a template to hide it:

```css
/* Hide anti ad-blocker layer */
#viel {
	display: none !important;
}
```

:tada:

Take the control back. Be a h4kz0r like me. Feel powerful.


Cheers :beer:
