---
layout: post
title: Coding Green
categories: [stuff]
tags: [code, general]
comments: true
description: Coding Green - a matter of performance.
---


![100% organic](/assets/images/organic.jpg)



# Green Coding


_"Green coding"? What about it? I'm not gonna plant trees while coding dude!_


Well, _"green coding"_ :seedling: is not really a thing (or I'm not aware of it yet) but I'd like some feedback.



I have read a thousand times that these emails :email: I'm sending everyday from my phone or laptop are equivalent to driving an average-size car per kilometer.

Add a 1-megabyte email attachment and you could power a low-wattage light bulb for two hours. Oopsie Daisie. :sunflower:


Okay, cool story bro :+1:, but do you really think your 2hours-light-bulb saving is worth the pain? And what are you talking about anyway?


Well, emails are one thing. Let's say you're a developer. You write code, each word adds a few bytes to your files. 
You start a project, add a few features for your application to rock! You want to be as famous as... Pokemon Go!
A few months later, you have built a 4Mb javascript application, you're really proud. Everything works, it's fully tested, automated, some black magic track the users, deploy the code automagically, you don't even need to work anymore. Time for some holidays. :palm_tree:


What now? Your application gets some fame. (moderate) Let's say you have 1000 users a day. :clap: :clap:

Using the previous comparaison, the power you need on one day for your 4Mb application, transfered to 1000 users.... represents the power you need for 3 years with your light bulb turned on 8hours a day. Well, that's not that alarming or is it? What if you have 1 million users? 1 Billion? Your impact only gets bigger.


This approximation is obviously simplistic, not even scientific for one second and does not take into account many things like code minification, caching, shared resources etc...

_Plus, this two hours light bulb thing can be found on many websites/blog posts but I haven't found the source of it. What the real footprint of 1Mb in your website assets? I'd appreciate any help._


At some point, we need to realize our footprint matters, as a developer.

Great, now, you feel ashamed to do your job. You can't write less code, your application ain't gonna have features by itself if you do not add it. But you don't want to pollute anymore.

Hold on tiger.


We have a few motto hanging around, and it's worth remembering them.

- YAGNI: you ain't gonna need it

Less is more, write the feature only when you absolutely need it. You know users want it, it comes from both intuition and validated learning. It won't rot a few weeks before you realize it's pointless.

- DRY: don't repeat yourself

If your code is well factored, you won't have any copy/pasted lines, everything will be written once and for all.
If you follow a bit of best practices or had good reads (Clean Code, Refactoring, Pragmatic Programmer), you must be familiar with notions of clean code, DRY and refactoring.


I'm not asking for anything new. It's already in the best practices we know.

Some frameworks/languages help you do that and state it.

Have a look for example at [Django Design Philosophies](https://docs.djangoproject.com/en/1.9/misc/design-philosophies/). Among the first bullet points, you have:

- Less Code
- DRY
- ...


See! We're already doing "green coding". Let's just keep in mind we can have an impact and try to do even better than before.


At least, if you don't care about the footprint you might have, just think that your footprint correlates with your app performance. If you need to transfer 20Mo to display your landing page, (I hope that's not the case), your ecologic footprint is dramatic but ... your performance will be really bad as well.


If you don't do it for your planet, do it for your users. And maybe, in a few years we'll have badges like _'bio code'_, _'raised in open air'_, _'fair trade commits'_.


Cheers ! :beers:
