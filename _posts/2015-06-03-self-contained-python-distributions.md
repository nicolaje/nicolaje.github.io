---
layout: post
section-type: post
title: "Self contained Python distributions"
category: Blog
modified: 2015-06-02
tags: [ 'python', 'teaching', 'robotics' ]
---

## The problem

When teaching a subject, we want to go to the point with the minimum friction. And often, when what we want to teach is related to programming (be it a library, a simulation framework, simple application of a mathematics class...), frictions are everywhere. EVERYWHERE.

> "Oh, you are using Windows? But that library only works on UNIX systems!"

> "An Ubuntu virtual machine? Smart. But that's not going to work for that 3D heavy simulator."

> "You can't use the dll I precompiled for you? Well, you are using Visual Studio, and I compiled them with MinGW..."

> "No, that only works with version X of \<insert one or several dependencies here\>. Please upgrade your Ubuntu 8.04 to 15.04, break your system, cry and then update the aforementioned packages!"

> "You need Python to be able to run the configure script (I am looking at you, [waf](https://waf.io)). Then you must install MingGW to compile the library, do some Voodoo on a full-moon night all wrapped in bacon and you can finally try to compile the HelloWorld I provide. Oh, by the way, you needed Python 2.X, not 3.X. Re-install, update your PATH variable and try again."

You got the point. So unless your have complete control over a bunch of computers in the room where the presentation happens (which you don't if you present at a University), hours to spend, and you manage to install everything for them, there is almost no way out of this situation. You will have to fix a lot of user-specific issues, no matter how hard you prepared your teaching material, on a per-user basis.

And not only is this a huge waste of time that could have been spent directly on the subject of interest, but if you have non-computer scientists in your audience you just lost them. They spent 80% of the session installing your white elephant and still have no idea how that works.

A dream solution would be to have some sort of installation free (as in ready to use from a downloaded folder), self contained (compiler, libraries, an IDE, the doc...) package available on OSX, Linux 32, 64 bits, Windows 32, 64 bits, for the language you need to use.
And when I mean "installation free", I mean you wouldn't even have to have your users touch a single environment variable. Nothing to install system-wide. Unzip, click, code, run.
Need to teach that subject in a native language? C++ folks? Keep dreaming. Froze yourself for the next 15 years and hope by then someone will have had the time to make that kind of package. If you are a visitor from the future, and know of a solution that hasn't been invented yet (or, in a more humble manner, that I don't know of), please, enlight us in the comment section.

That subject can be taught in Python? Keep reading, that's going to be interesting.

## The requirements
First, in France, from this year, every new engineering student will have been taught Python during his 2 years [Higher School Preparatory Classes](http://en.wikipedia.org/wiki/Classe_préparatoire_aux_grandes_écoles), even in a non computer-sciences related branch. That is awesome.
With packages such as [NumPy](http://www.numpy.org), you can dig into engineering problems without focusing on the language itself, in a [MatLab](http://www.scipy.org/NumPy_for_Matlab_Users) way. But more free. And with the power of a real programming language if you want to go deeper.

So, we need a Python3 "box", that can be used on OSX, Linux, Windows, doesn't mess with the system so we can distribute it on computers without a root access, an integrated IDE would be great, some doc, the ability to pre-install easily scientific packages is a big plus.
The "Box" should be ready to use. No package installation required, no command lines to issue. It should also be "movable": the user should be able to move the folder anywhere in his computer, and still be able to use it.

## The options

### Anaconda:
If you [googled "Python"](https://www.google.com/?q=python) in the last years, you have stumbled upon [Anaconda](https://www.continuum.io/why-anaconda). You also probably scratched your head a few minutes wondering what that is. Anaconda is simply a Python distribution shipped with the [Spyder IDE](http://pythonhosted.org/spyder/) and [an impressive bunch of pre-installed Python packages for scientific computing](http://docs.continuum.io/anaconda/pkg-docs). It also includes [Conda](http://conda.pydata.org/docs/), a Python package manager like the famous [pip](https://pip.pypa.io/en/stable/). It is free, available on OSX, Windows, Linux, and the installation doesn't require administrator privileges. The only drawback I found is that it messes with you PATH environment variable. Which means that if you type "python" in your console, it will now call Anaconda's Python executable. No big deal, but it is preferable that the user's system is left untouched. Moreover, if we move the installation folder and click on the launcher, it won't launch Anaconda anymore, so there is probably a path that was hardcoded somewhere during the installation. It is really no big deal and is probably easily fixed, but as is, it doesn't fit the requirements.

### Pyzo
[Pyzo](http://www.pyzo.org) is a little Python gem that has not a lot of visibility on the web. It was developed by Almar Klein and Rob Reilink during their PhDs. It is a Python3 distribution bundled with [IEP](http://www.iep-project.org), an IDE they also developed, and a bunch of [pre-installed Python packages](http://www.pyzo.org/packages.html#packages). It ships with [Conda](http://conda.pydata.org/docs/) and [pip](https://pip.pypa.io/en/stable/), so more packages can be installed in the distribution. It is a zero-installation distribution: unzip, click, code. It is distributed for Windows, OSX and Linux. If you move the Pyzo directory, everything still works. It is exactly what we were looking for.
The only drawback I see is that the developers are onto other things like [zoof](http://zoof.io) and [flexx](https://flexx.readthedocs.org/en/latest/). The Pyzo and IEP source code are on a low-visibility [Bitbucket repository](https://bitbucket.org/pyzo/), and the developers are not willing to move to Github. I am not sure about the future of Pyzo, or if [zoof](http://zoof.io) will be a viable replacement.

### Enthought Canopy
[Enthought Canopy](https://www.enthought.com/products/canopy/) seems to be very similar to what anaconda offers. This is a self contained Python environment bundled with [a 100 scientific packages](https://www.enthought.com/products/canopy/package-index/) and they offer a paid plan for more pre-installed packages, among other things.
The installation doesn't set environment variables if you don't want, and the installation folder can be moved and still be used. It is available for OSX, Windows, Linux.

### Zoof
[Zoof](http://zoof.io) is developed by the developers of Pyzo. It is still in development but it seems promising.

### Portable Python
[Portable Python](http://portablepython.com) is not developed anymore and was for Windows only.

### WinPython
The name says it all, [WinPython](http://winpython.github.io/) is for Windows only.

### Python(x,y)
[Python(x,y)](http://python-xy.github.io) is Windows only.

## The choice
The distributions listed above include much, much more features than what we judge them for. But our requirements were portability (OSX, Linux, Windows), portability (movable installation), installation free, no need for administrator privileges.
From these requirements, I will retain [Pyzo](http://www.pyzo.org). Even if the IDE is not as polished as Spyder and if its future is uncertain, it fits all the requirements, especially the unzip/click/code feature. I hope [Zoof](http://zoof.io) will be a good alternative solution in the future if Pyzo doesn't receive updates anymore.
