# Setting Up DGD

Before anything else, you're going to need to get DGD set up and running. Remember how I said it was never popular? We're going to compile this with a C compiler and install it ourselves, like our primitive ancestors did.

For any platform you'll need a copy of DGD's code. If you have Git installed, this is the way to get that:

    git clone https://github.com/dworkin/dgd.git

Inside the "dgd" directory you just cloned is a "src" directory containing the source code you'll be compiling.

I'm using an HTTP URL to clone DGD. That can be annoying in some cases. But this is just to get you up and running.

## Installing DGD: Mac

You're going to need GCC, which is a C compiler. If you're going to hack on DGD you'll need Bison, a parser-generator. But that's not needed for normal usage.

Once you have what you need, open a terminal app such as iTerm2 or the built-in Mac Terminal app. Change into the "dgd" directory you just cloned up above, and then cd into its "src" directory. Now:

    make install

This should build DGD and create an application called "dgd" in dgd/bin.

Now you'll need to either add dgd/bin to your path, or copy the "dgd" binary to someplace that's already in your path.

## Installing DGD: Linux

You'll need a basic compiler installed. On Debian-based distributions, this package is called build-essentials. You likely have it already. You'll also need the package for the GNU Bison parser generator, probably called "bison".

Now open a terminal window, and change into the dgd/src directory (dgd is the one you cloned up above.)

    make install

This should build DGD and create an application called "dgd" in dgd/bin.

Now you'll need to either add dgd/bin to your path, or copy the "dgd" binary to someplace that's already in your path.

## Installing DGD: Windows

First off, have you considered using the most recent WSL? It's going to be easier and run better, as a rule.

If not... TBD on this section.

## A Test Run

Now that you have dgd in your path, let's make sure it worked.

DGD doesn't ship with any runnable DGD applications. You're going to need to make one.

Luckily, the Kernel library fixes this problem: it's runnable out of the box. Next to where you cloned DGD, let's clone the Kernel library:

    git clone https://github.com/ChatTheatre/kernellib.git

Once you have it cloned, go to kernel.dgd and change its "directory" property to "src".

Now, run it with dgd:

    dgd kernel.dgd

You should see text indicating that a server is running:

```
Noahs-MBP-2:kernellib noah$ ../dgd/bin/dgd kernel.dgd
Sep 27 13:28:31 ** DGD 1.6.13
Sep 27 13:28:31 ** Initializing...
Sep 27 13:28:31 ** Initialization complete.
```

You have a functioning installation of DGD. Now you just have to learn how to use it!

(Hit control-C to interrupt the program.)

## Connecting to DGD

You'll want a program called "telnet" for this. It's a sort of old-school unencrypted "ssh".

### Installing Telnet: Mac with Homebrew

If you're using [Homebrew](https://brew.sh), just "brew install telnet".

### Linux

On Debian- or Ubuntu-based distributions, "apt-get install telnet". There's a pretty good chance your distribution of choice will also let you just install a package named "telnet".

### Windows

Windows has a telnet client called ["PuTTY"](https://www.putty.org) that works well. You can download and run it, then use it for connecting to MUDs in general, and DGD on your machine in particular.

Windows 10 also has a specific telnet feature which can be enabled. Google for "Windows 10: Enabling Telnet Client." Your own version of windows might have some system version of telnet as well.

## What Is the Kernel Library?

DGD has a bunch of weird, interesting features. Many of them require a bunch of extra infrastructure to manage properly. You can build it all yourself. Or you can use a structure that handles it. There are a lot of possible structures that ***could*** handle it, and DGD would like you to be able to use any of them.

But there's a specific, common way to handle most of it that you can just put in place and use. It's called the Kernel Library (or Kernellib or Kernel MUDLib.) There's also a closely-related later version called the Cloud Server that I would describe as "really not done yet." Most of this book assumes you're happy using the Kernel Library. There will be a chapter toward the end that explains what raw DGD is like without it.
