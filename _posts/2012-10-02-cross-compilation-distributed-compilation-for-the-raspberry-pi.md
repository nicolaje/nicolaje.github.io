---
layout: post
section-type: post
title: Cross-compilation & Distributed compilation for the Raspberry-Pi
category: Howto
modified: 2015-12-16
tags: [ 'howto', 'ROS', 'raspberry pi' ]
---

## INTRODUCTION
Compiling large programs such as a Linux Kernel, or big libraries like [OpenCV](http://opencv.willowgarage.com/), [OpenNI](http://www.openni.org/) directly on your [Raspberry Pi](http://www.raspberrypi.org/) will take a lot of time, and sometimes will even fail ( I was not able to reach more than 6% in the compilation process of the [PCL](http://www.http//pointclouds.org/), when compiling directly on the Pi ).

In this howto I will show you how to:

1. [cross compile](http://en.wikipedia.org/wiki/Cross_compiler) programs, i.e.: how you can compile a program on your PC so that it will run on your Raspberry Pi
2. distribute the compilation so that when you compile a program from your Raspberry Pi it actually gets cross-compiled on your remote PC(s), in a totally transparent manner

I am assuming that just as me, you're manipulating directly in your $HOME directory, both on your Raspberry Pi and your PC, running any Debian-flavored Linux distribution ( [Debian](http://www.debian.org/), [Linux Mint Debian Edition](http://www.linuxmint.com/download_lmde.php), [Ubuntu](http://www.ubuntu.com/)... ). However, adapting this tutorial to your non-Debian distribution should not be too complicated, just use the equivalent packages you will find in your package manager.

### INSTALL A TOOLCHAIN
To cross compile you have to set up a toolchain. You have two options here:

1. build it yourself
2. use the one provided by the Raspberry Pi Foundation
Use the first option if you are curious and want to learn how to build your own toolchain using crosstool-ng, or use the second one to save you some time and be sure to end-up with something working.
Note: this part is happening on your Linux PC, not on the Raspberry Pi !

#### BUILD YOUR TOOLCHAIN
You can find good materials to build your own toolchain [here](http://www.bootc.net/archives/2012/05/26/how-to-build-a-cross-compiler-for-your-raspberry-pi/).

#### USING THE OFFICIAL RASPBERRY PI TOOLCHAIN
The Raspberry Pi Foundation is providing a ready-to-use toolchain on [their github repository](http://jeremy-nicola.info/web/20140513200423/https://github.com/raspberrypi/tools). You can use it to save yourself some time.

To do so, you need to have git installed and to clone the repository :

<pre><code data-trim class="shell">sudo apt-get install git-core
git clone https://github.com/raspberrypi/tools.git --depth=1
</code></pre>

The "--depth=1" is here to tell git we only want the last revision, and not the whole history to be cloned.

Assuming you use Raspbian, issue a:

<pre><code data-trim class="shell">jem@jem:~$ ls tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/
arm-linux-gnueabihf-addr2line
arm-linux-gnueabihf-gprof
arm-linux-gnueabihf-ar
arm-linux-gnueabihf-ld
arm-linux-gnueabihf-as
arm-linux-gnueabihf-ld.bfd
arm-linux-gnueabihf-c++
arm-linux-gnueabihf-ldd
arm-linux-gnueabihf-c++filt
arm-linux-gnueabihf-ld.gold
arm-linux-gnueabihf-cpp
arm-linux-gnueabihf-nm
arm-linux-gnueabihf-ct-ng.config
arm-linux-gnueabihf-objcopy
arm-linux-gnueabihf-elfedit
arm-linux-gnueabihf-objdump
arm-linux-gnueabihf-g++
arm-linux-gnueabihf-pkg-config
arm-linux-gnueabihf-gcc
arm-linux-gnueabihf-pkg-config-real
arm-linux-gnueabihf-gcc-4.7.2
arm-linux-gnueabihf-ranlib
arm-linux-gnueabihf-gcc-ar
arm-linux-gnueabihf-readelf
arm-linux-gnueabihf-gcc-nm
arm-linux-gnueabihf-size
arm-linux-gnueabihf-gcc-ranlib
arm-linux-gnueabihf-strings
arm-linux-gnueabihf-gcov
arm-linux-gnueabihf-strip
arm-linux-gnueabihf-gdb
arm-linux-gnueabihf-gdbtui
arm-linux-gnueabihf-gfortran
</code></pre>

Here are the binaries we will use to cross-compile. Let's test them !

Create a new file named hello_world.c and copy/paste the following code:

<pre><code data-trim class="c">&#35;include &#60;stdio.h&#62;

int main(void)
{
    printf("Hello, cross-compilation world !\n");
    return 0;
}
</code></pre>

Then, enter the following commands:

<pre><code data-trim class="shell">jem@jem:~$ tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-gcc hello_world.c -o hello_world
jem@jem:~$ ./hello_world
bash: ./hello_world : cannot execute binary file
jem@jem:~$ file hello_world
hello_world: ELF 32-bit LSB executable, ARM, version 1 (SYSV), dynamically
linked (uses shared libs), for GNU/Linux 2.6.26, BuildID[sha1]=0xfd72b5c6878433eb7f2296acceba9f648294a58c, not stripped
</code></pre>

As you see, you can't execute this program on your PC. The file command tells you that this executable is built for ARM processors.

Now, copy your hello_world file on your Raspberry Pi :

<pre><code data-trim class="shell">jem@jem:~$ scp hello_world pi@&#60;IP ADDRESS OF YOUR PI&#62;:/home/pi
&#35; From your Raspberry Pi:
pi@raspberrypi ~ $ ./hello_world
Hello, cross-compilation world !
</code></pre>

Congratulations, you just cross-compiled your first Raspberry-Pi program !

Now, you can develop your programs and compile them directly on your PC, then upload them on your Pi and voilà, everything should work !

But now imagine you want to cross-compile a library that depends on other librairies (which, in turn, could depend on other libraries...). How will you do to satisfy these dependencies? Having the libraries installed on your PC won't work, because they are compiled to run on a non-ARM architecture ( probably x86 x86_64 ).

Plus, imagine you have several PC's and you would like to share the cross-compilation tasks among each of them to speed-up the process. Wouldn't it be awesome? ( I confess this "compilation farm" functionality is the one that I like the most =) ).

And wouldn't it be nice if you were able to do all this from your Raspberry Pi? Just launching the make command, having your remote PC's working instead of your Pi, and a few seconds later having your program/library compiled just as if everything happened on the Pi itself?

This is possible using [distcc](http://jeremy-nicola.info/web/20140513200423/https://code.google.com/p/distcc/), a tool to distribute builds of C, C++... accross several machines on a network. This is what I will be covering in the second part of this HowTo.

## SETTING UP A DISTCC CLUSTER

### INSTALLATION
You probably have a distcc package ready to install with your package manager, but the distcc package available at the time of writing for Debian and Raspbian provides version 3.1 of distcc.

<pre><code data-trim class="shell">pi@raspberrypi ~ $ apt-cache show distcc
Package: distcc
Version: 3.1-5
(...)
</code></pre>

Problem is we need a [new feature introduced in version 3.2](http://distcc.googlecode.com/svn/trunk/NEWS) of distcc: the ability to configure the DISTCC_IO_TIMEOUT constant that was hardcoded to 300 seconds in previous versions. If you use a previous version it's fine, but you might see this kind of error:

<pre><code data-trim class="shell">(dcc_select_for_write) ERROR: IO timeout
</code></pre>

It means that a transfer of data has lasted more than 300 seconds, so distcc will presume the server is down, and will try to build the file locally, a thing we don't want. For example, it happened quite frequently when I was building the PCL.

So let's build distcc to have the latest version!

On both the Raspberry Pi and your PC, issue the following commands:

<pre><code data-trim class="shell">sudo apt-get install subversion autoconf automake python python-dev
svn checkout http://distcc.googlecode.com/svn/trunk/ distcc-read-only
cd distcc-read-only
./autogen.sh
</code></pre>

As usual when building a software from its sources, let's have a look to the options offered:

<pre><code data-trim class="shell">./configure --help
`configure' configures distcc 3.2rc1 to adapt to many kinds of systems.

(...)

Optional Packages:

(...)

--with-gtk build GTK+-based monitor

(...)
</code></pre>

So if you want to be able to follow the activity of distcc in a simple GUI monitor on your Pi during the build process, you will have to install the following package:

<pre><code data-trim class="shell">sudo apt-get install libgtk2.0-dev
</code></pre>

and its dependencies (quite a few). Now we can configure, build and install distcc:

<pre><code data-trim class="shell">./configure --with-gtk && make && sudo make install
</code></pre>

First, you have to make some symbolical links to be sure that when gcc or g++ are called on the Raspberry Pi, they are called through distcc.

So, on the Raspberry Pi run the following commands:

<pre><code data-trim class="shell">pi@raspberrypi ~ $ which gcc # This tells you where gcc lies
/usr/bin/gcc
pi@raspberrypi ~ $ which distcc &#35; This tells you where distcc has been installed
/usr/local/bin/distcc
pi@raspberrypi ~ $ sudo ln -s /usr/local/bin/distcc /usr/local/bin/gcc
pi@raspberrypi ~ $ sudo ln -s /usr/local/bin/distcc /usr/local/bin/cc
pi@raspberrypi ~ $ sudo ln -s /usr/local/bin/distcc /usr/local/bin/g++
pi@raspberrypi ~ $ sudo ln -s /usr/local/bin/distcc /usr/local/bin/c++
pi@raspberrypi ~ $ sudo ln -s /usr/local/bin/distcc /usr/local/bin/cpp
export PATH=/usr/local/bin:$PATH
pi@raspberrypi ~ $ which gcc &#35; Now, when gcc is called, distcc is called instead
/usr/local/bin/gcc
</code></pre>

It's a good idea to put the export command in your ~/.bashrc file, so your PATH variable is restored when you reboot the Pi and open a new terminal. It's important to put /usr/local/bin before $PATH, because when gcc/cc/g++/c++ will be called, the system will take the first instance it will find in your PATH variable.

It's also important to set the following environment variables:

<pre><code data-trim class="shell">&#35; The remote machines that will build things for you. Don't put the ip of the Pi unless
&#35; you want the Pi to take part to the build process.
&#35; The syntax is : "IP_ADDRESS/NUMBER_OF_JOBS IP_ADDRESS/NUMBER_OF_JOBS" etc...
&#35; The documentation states that your should set the number of jobs per machine to
&#35; its number of processors. I advise you to set it to twice as much. See why in the test paragraph.
&#35; For example:
export DISTCC_HOSTS="192.168.1.28/16"

&#35; When a job fails, distcc backs off the machine that failed for some time.
&#35; We want distcc to retry immediately
export DISTCC_BACKOFF_PERIOD=0

&#35; Time, in seconds, before distcc throws a DISTCC_IO_TIMEOUT error and tries to build the file
&#35; locally ( default hardcoded to 300 in version prior to 3.2 )
export DISTCC_IO_TIMEOUT=3000
&#35; Don't try to build the file locally when a remote job failed
export DISTCC_SKIP_LOCAL_RETRY=1
</code></pre>

You should put these lines in your ~/.bashrc too.

Now, on your PC you have to make sure that when distcc calls your compiler, it takes your cross-compiler. To do so:

<pre><code data-trim class="shell">jem@jem:~$ cd tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/
jem@jem:~/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin$ ln -s arm-linux-gnueabihf-gcc gcc
jem@jem:~/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin$ ln -s arm-linux-gnueabihf-gcc cc
jem@jem:~/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin$ ln -s arm-linux-gnueabihf-c++ c++
jem@jem:~/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin$ ln -s arm-linux-gnueabihf-cpp cpp
export PATH=$HOME/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin:$PATH
jem@jem:~$ which gcc &#35; Now your cross-compiler is called whenever you call cc/gcc/g++/c++/cpp
/home/jem/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/gcc
</code></pre>

Once more, it's convenient to put the export line at the end of your ~/.bashrc file.

On your PC, you now have to run the distccd daemon:

<pre><code data-trim class="shell">&#35; --jobs sets the number of jobs this machine can take in parallel
&#35; --allow sets allows the machine with the given ip address to connect
&#35; ( for more than one IP, call --allow again, i.e.: --allow 192.168.1.1 --allow 192.168.1.2 ...
&#35; --log-stderr redirect the errors in the standard error output
&#35; --no-detach don't detach from this terminal, so we can track the log in real time
jem@jem:~$ distccd --daemon --jobs 16 --allow 192.168.1.42 --verbose --log-stderr --no-detach
</code></pre>

Do these steps for each PC you want to use as a remote cross-compiler (called a server in the distcc documentation).

### LET'S TEST IT !
Let's test our distcc setup by building [OpenCV](http://www.opencv.org/) !

![Cross Compiling OpenCV](http://nicola.je/img/OpenCVCompilation.png)
This is what you want to see when using distributed cross-compilation: all your PC's processors working 100% while your Raspberry Pi stays calm.

After you launched the distccd daemon on each of your remote PC's ( not on the Raspberry Pi unless you want it to build OpenCV too ), issue the following commands from your Pi:

<pre><code data-trim class="shell">pi@raspberrypi ~ $ git clone --depth=1 git://code.opencv.org/opencv.git
pi@raspberrypi ~ $ cd opencv
pi@raspberrypi ~ $ mkdir redist && cd redist
pi@raspberrypi ~ $ cmake -DCMAKE_BUILD_TYPE=DEBUG -DBUILD_JASPER=ON -DBUILD_OPENEXR=ON -DBUILD_JPEG=ON -DBUILD_PNG=ON -DBUILD_TIFF=ON -DBUILD_ZLIB=ON -DBUILD_DOCS=OFF -DBUILD_PERF_TESTS=OFF -DBUILD_TESTS=OFF -DBUILD_opencv_gpu=OFF -DWITH_CUDA=OFF -DWITH_CUFFT=OFF -DWITH_OPENCL=OFF ..
pi@raspberrypi ~ $ time make -j8
(...)
real 18m32.275s
user 9m38.320s
sys 2m49.230s
</code></pre>

Building OpenCV took only 18 minutes for me ! Building it directly on the Raspberry Pi takes something like 5 hours ( yes, I already built it without cross distcc... And don't want to do that again to measure the exact time it takes ;) )

Note that I followed the documentation: my PC is said to have 8 processors ( 4 cores actually, with 2 threads per core )

<pre><code data-trim class="shell">jem@jem:~$ cat /proc/cpuinfo | grep processor | wc -l
8
</code></pre>

so I allowed 8 jobs to occur in parallel in the distccd daemon. But, setting this number to twice more processors than I have:

<pre><code data-trim class="shell">pi@raspberrypi ~/opencv/redist $ time make -j16

(...)

real 12m6.442s
user 6m13.860s
sys 1m51.690s
</code></pre>

it took 6 minutes less. This is why I suggest you to set up the distccd daemon to run (2 * Number of processors) jobs.

I hope this howto will be of some use someday to someone!

If something is not working, let me know in the comments below =)

Jeremy

## REFERENCES
[https://code.google.com/p/distcc/](https://code.google.com/p/distcc/) the official distcc website

[http://www.raspberrypi.org/phpBB3/viewtopic.php?t=11776](http://www.raspberrypi.org/phpBB3/viewtopic.php?t=11776) a Raspberry Pi Forumer using distcc to distributively build kernels on his 3-Rasperry Pi cluster.

[http://raspberrypi.stackexchange.com/questions/782/how-do-i-install-distcc](http://raspberrypi.stackexchange.com/questions/782/how-do-i-install-distcc) see Alex Chamberlain post

[http://manpages.ubuntu.com/manpages/gutsy/man1/distcc.1.html](http://manpages.ubuntu.com/manpages/gutsy/man1/distcc.1.html) distcc manpage on the Ubuntu Manpages ( not up to date )

[http://manpages.ubuntu.com/manpages/gutsy/man1/distccd.1.html](http://manpages.ubuntu.com/manpages/gutsy/man1/distccd.1.html) distccd manpage on the Ubuntu Manpages
