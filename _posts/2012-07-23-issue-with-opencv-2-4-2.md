---
layout: post
section-type: post
title: Issue compiling OpenCV 2-4-2
category: Blog
modified: 2015-12-16
tags: [ 'howto', 'OpenCV', 'fix' ]
---

This week-end, after [the release of JavaCV 0.2 with Raspberry Pi support](http://nicola.je/2012/07/24/javacv-0-2-and-raspberry-pi), I wanted to do some testing with both OpenCV and JavaCV, in order to see how well does the Java wrapper perform compared to the native use of OpenCV, on platforms with limited resources such as a Raspberry Pi.

But no matter what I tried, I always got cvCaptureFromCAM() returning NULL. And, of course, the same with OpenCVFrameGrabber.grab() from JavaCV.

I eventually found a solution: OpenCV2.4.2 has to be built in the DEBUG mode with CMAKE. I don't know how/why and if it is specific to the Raspberry Pi, but doing this is working. So, for your Raspberry Pi, just build it using:

<pre><code data-trim class="shell">cmake -D CMAKE_BUILD_TYPE=DEBUG -D WITH_CUDA=OFF ..
make
sudo make install
</code></pre>
and enjoy OpenCV2.4.2 and JavaCV0.2 working with usb webcam capture.

Now it's time to make some benchmarks.

<s>You can also use the prebuilt binaries of OpenCV2.4.2/FFmpeg0.11.1/JavaCV0.2/JavaCPP/Libfreenect I'm providing [on my download page](http://nicola.je/download/).</s>
