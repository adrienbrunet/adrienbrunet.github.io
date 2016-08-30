---
layout: post
title: My browser now shouts back at me
categories: [javascript]
tags: [code, general, javascript]
comments: true
description: I used to shout at my computer, it started shouting back.
---

# My browser can now shout back at me

![40% yelling](/assets/images/yelling.JPG)


I talk a lot to my computer:

> :loudspeaker: "WHY YOU SO SLOW? WHY DO YOU... UUHHHH... JUST DO IT... COME ON! YOU LOST WIFI AGAIN. DAMNIT.".

Ok, I admit it, I yell at it.
But it started shouting back.

I'm not nuts. Not yet. I just found one new feature :sparkles: (at least for me) on the `window` object with Chrome (had no luck with other browsers). Please note this is still an experimental technology.

Let me explain what we can do. Copy/paste this small script in your browser console. If the odds are with you, you should hear an almost friendly voice giving its greetings. (Mine is a french female voice by default but you can change that if you want to. See `getVoices()`)

<a class="jsbin-embed" href="http://jsbin.com/jicopubuma/embed?js">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.39.14"></script>

What this does: the first line creates an object holding the text to be spoken out and loud to the crowd, the second one makes you hear the warm and cute voice of your robot-computer.

Let's wrap this into an Immediately-invoked anonymous function to include this snippet everywhere. Let's make the internets a really bad place.

```javascript
(function() {
  var greetings = new SpeechSynthesisUtterance();
  greetings.text = "beep boop! Greetings human! I'm gonna eat your soul! Stop wasting your time on cat videos.";
  window.speechSynthesis.speak(greetings);
})();
```

That's it, your browser is now talking! (at least, if you're using a compatible one see for example [this doc](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance "Documentation on SpeechSynthesisUtterance")
I guess the default language voice depends on your settings, more on that at the end of this post.

I wonder why I wasn't fully aware of this earlier. The API is quite simple. I'm not sure what applications we can have with that, some kind of accessibility features I guess. It's great even if the voice is sometimes a bit robotic... I don't have any other idea how to use this. Or maybe I do. See second part of the article.

The fact that it's not a popular feature... Well... It's in the window object... Which is a total mess. Last time I checked what was in there, I found an old friend from college, a dead body and some candies.
But I'm not gonna talk trash about javascript or the window object, it's too much of an easy game, it's not even fair. Javascript is _fun_, 3 lines and it's even talking back to you, see? Let's use it now before it becomes self-aware.

# The useless part
Let's have a singing javascript:

```javascript
var singForMe = function (text) {
  var lyrics = new SpeechSynthesisUtterance();
  lyrics.text = text;
  window.speechSynthesis.speak(lyrics)
}
```

Now it's ready to sing. Just need some groove. Rock it sweety! :guitar:

```javascript
var myCoolLyrics = ("Buy it, use it, break it, fix it," +
  "Trash it, change it, mail - upgrade it," +
  "Charge it, point it, zoom it, press it," +
  "Snap it, work it, quick - erase it," +
  "TECHNOLOGIC! TECHNOLOGIC! TECHNOLOGIC!");
SingForMe(myCoolLyrics);
```

Here it is, hold tight Daft Punk, I'm coming at you!

_Bonus part_: Do you want me to sing it?
Let see what's behind `window.speechSynthesis.getVoices()`: a nice list of 20 voices! 

Let's make fun of the french accent: :microphone: :microphone: :microphone:

```javascript
var synth = window.speechSynthesis
var frenchyAccent = synth.getVoices().filter(function(voice) { return voice.lang == 'fr-FR'; })[0];
var lyrics = new SpeechSynthesisUtterance(myCoolLyrics);
lyrics.voice = frenchyAccent;
window.speechSynthesis.speak(lyrics);
```

What a delight!

In fact, you can have a lot more fun from that, here's a creepy example from [here](https://developers.google.com/web/updates/2014/01/Web-apps-that-talk-Introduction-to-the-Speech-Synthesis-API?hl=en):

```javascript
var msg = new SpeechSynthesisUtterance('I see dead people!');
msg.voice = speechSynthesis.getVoices().filter(function(voice) { return voice.name == 'Whisper'; })[0];
speechSynthesis.speak(msg);
```
Not sure about you, but I won't sleep well from now on. I work more than 8 hours a day with a creepy computer.
Want more? You can also tune the pitch, volume and rate.
Show me your best creation in the comments!

You're still here? Want more? There is also a `SpeechRecognition` module. But that will be for another day.

*Bonus:* if you ever read this @bmispelon, note that speechSynthesis can read emoji as well. :wink:

Hope you enjoyed it, Cheers :beer:
