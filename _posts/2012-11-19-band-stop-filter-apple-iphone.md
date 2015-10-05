---
layout: article
title: "Band-stop filters on apple iPhones"
categories: blog
modified: 2013-04-10
tags: [Programming,Signal processing]
---
While I was working on my [Android-tachometer project](), I came across very strange results regarding the frequency response on Apple iPhones microphones.

Look at the following plot:
EMBED 2CVSpectrogram
Spectrogram of a Citroën 2CV engine recorded with an Apple iPhone
It represents the [spectrogram](http://en.wikipedia.org/wiki/Spectrogram) of a [Citroën 2CV](http://fr.wikipedia.org/wiki/Citroën_2CV) engine audio recording. You can clearly see the vertical blue band located at 17KHz and the large vertical blue band starting at 19KHz until the end of the spectrum. It means that at these locations, the recorded signal has very few energy. But this is weird and unexpected, so I performed the same tests with a Samsung Galaxy SII and a laptop microphone (click to enlarge):
EMBED Renault4LSpectrogram
Spectrogram of a Renault 4L engine using an Apple iPhone, a Samsung Galaxy SII and a laptop.
We see that the strange frequency response happens only on the Apple iPhone, meaning the empty frequency bands are not a property of the recorded signal, but of the microphone !

So we have a [band-stop filter](http://en.wikipedia.org/wiki/Band-stop_filter) at 17KHz, and a [low-pass filter](http://en.wikipedia.org/wiki/Low-pass_filter) at 19KHz...

Why do you think Apple would put such filters on iPhones microphones? I know some teenagers use those high-frequencies ring tone so they can't be heard by adults, and they can text in class with impunity.

But in this case Apple should have put those filters on the speakers of the iPhones instead on the microphones.

If anybody knows the possible reason to put such filters on iPhones microphones, please let me know !
