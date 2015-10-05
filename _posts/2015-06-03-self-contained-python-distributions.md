---
layout: article
title: "Self contained Python distributions"
categories: blog
modified: 2015-06-02
tags: [Python,Teaching,Robotics]
---
When teaching a subject, we want to go to the point with the minimum friction.
And often, when what we want to teach is related to programming (be it a library, a simulation framework, simple application of a mathematics class...), frictions are everywhere. EVERYWHERE.

"Oh, you are using Windows? But that library only works on UNIX systems!"
"An Ubuntu virtual machine? Smart. But that's not going to work for that 3D heavy simulator."
"You can't use the dll I precompiled for you? Well, you are using Visual Studio, and I compiled them with MinGW..."
"No, that only works with version X of <insert one or several dependencies here>. Please upgrade your Ubuntu 8.04 to 15.04, break your system, cry a lot and then update the aforementioned packages!"
"You need Python to be able to run the configure script. Then you must install MingGW to compile the library, do some Voodoo on a full-moon night wrapped in bacon and then you can try to compile the HelloWorld I provide. Oh, by the way, you needed Python 2.X, not 3.X. Re-install, update your PATH variable and try again."

You got the point. So unless your have complete control over a bunch of computers in the room where the presentation happens (which you don't if you present at a University), hours to spend, and you manage to install everything for them, there is almost no way out of this situation. You will have to fix a lot of user-specific issues, no matter how hard you prepared your teaching material, on a per-user basis.

And not only is this a huge waste of time that could have been spent directly on the subject of interest, but if you have non-computer scientists in your audience you just lost them. They spent 80% of the session to install your white elephant and still have no idea how that works.

A dream solution would be to have some sort of installation free (as in ready to use from a downloaded folder), self contained (compiler, libraries, an IDE, the doc...) package available on OSX, Linux 32, 64 bits, Windows 32, 64 bits, for the language you need to use.
And when I mean "installation free", I mean you wouldn't even have to have your users touch a single environment variable. Nothing to install system-wide. Unzip, click, code, run.
Need to teach that subject in a native language? C++ folks? Keep dreaming. Froze yourself for the next 15 years and hope by then someone will have had the time to make that kind of package. If you are a visitor from the future, and know of a solution that hasn't been invented yet (or, in a more humble manner, that I don't know of), enlight us in the comment section.

That subject can be taught in Python? Keep reading, that's going to be interesting.

First, in France, from this year, every new engineering student will have been taught Python during its 2 years [Higher School Preparatory Classes](http://en.wikipedia.org/wiki/Classe_préparatoire_aux_grandes_écoles), even in a non computer-sciences related branch. That is awesome.
With packages such as [NumPy](http://www.numpy.org), you can dig into engineering problems without focusing on the language itself, in a [MatLab](http://www.scipy.org/NumPy_for_Matlab_Users) way. But more free. And with the power of a real programming language if you want to go deeper.

So, we need a Python3 "box", that can be used on OSX, Linux, Windows, doesn't mess with the system so we can distribute it on computers without a root access, an integrated IDE would be great, some doc, the ability to pre-install easily scientific packages is a big plus.
The "Box" should be ready to use. No package installation required, no command lines to issue. It should also be portable: the user should be able to move the folder anywhere in his computer, and still be able to use it.

///////////////////////////////
// TODO:

Anaconda:
Tester sous Linux, car dit :
"Installs into a single directory and doesn't affect other Python installations on your system. Doesn't require root or local administrator privileges"
vient avec Spyder. Le fait de devoir lancer un script au demarage serait un point negatif. Pyzo se base aussi sur Conda pour la partie install de paquets donc peu de differences.

Ajoute un repertoire sur le bureau

Installe dans un repertoire MAIS edite le .bash_profile pour que le PATH trouve le repertoire... BUMP
L'etudiant va drag & drop le repertoire -> ne se lance meme plus!

Pyzo:
Yabon

Entought:
commercial

Portable Python

WinPython

| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|----
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
