---
layout: post
title: Stylish is dead, vive moi
categories: [stuff]
tags: [code, stuff]
comments: true
description: Hide your facebook timeline without stylish
---

It's been a long time since my last article but I found a good reason to stop this ongoing silence.

I wrote [this article](http://blog.adrienbrunet.com/stuff/2016/09/15/hide-all-the-things.html) almost 2 years ago, showing how to use an extension called "stylish" to hide your facebook feed.

Sadly, I learned today that this extension has a spyware spying browser history. (see this source for example(https://www.bleepingcomputer.com/news/software/chrome-and-firefox-pull-stylish-add-on-after-report-it-logged-browser-history/))

What now ? I still want to hide my facebook feed but I want to get rid of this stupid addon.

Let's create my own !

I followed a simple tutorial called "borderify" provided by mozilla and off I go. :sparkles:

You need to create a `manifest.json` file with a content like this:

```json
{

  "manifest_version": 2,
  "name": "HideMyFbFeed",
  "version": "1.0",

  "description": "Hides your facebook timeline",

  "homepage_url": "https://github.com/adrienbrunet/hidemyfbfeed",

  "icons": {
    "48": "icons/border-48.png"
  },

  "content_scripts": [
    {
      "matches": ["*://*.facebook.com/*"],
      "js": ["hidemyfbfeed.js"]
    }
  ]

}
```
and here is my `hidemyfbfeed.js` file:

```javascript
/* Hide newsfeed */
let topnews = document.getElementById("topnews_main_stream_408239535924329");
topnews.style.visibility = "hidden";

/* Remove trending stories and suggested groups */
let pagelet_trending_tags_and_topics = document.getElementById("pagelet_trending_tags_and_topics");
let pagelet_ego_pane = document.getElementById("pagelet_ego_pane");
pagelet_trending_tags_and_topics.style.visibility = "hidden";
pagelet_ego_pane.style.visibility = "hidden";
```

Then, you can add the according icon and your addon is ready.

The submission process is really simple and I got accepted in no time.
If you want to try it out... [Here you go!](https://addons.mozilla.org/en-GB/firefox/addon/hidemyfbfeed/)

Cheers! 
:beers:
