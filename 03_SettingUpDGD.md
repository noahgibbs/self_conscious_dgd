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

Luckily, the Cloud Server fixes this problem: it's runnable out of the box. Next to where you cloned DGD, let's clone the Cloud Server:

    git clone https://github.com/dworkin/cloud-server.git

Once you have it cloned, change into its directory. Then go to cloud.dgd and change its "directory" property to "src".

Now, run it with dgd:

    dgd cloud.dgd

You should see text indicating that a server is running:

```
Noahs-MBP-2:cloud-server noah$ ../dgd/bin/dgd cloud.dgd
Sep 27 13:28:31 ** DGD 1.6.13
Sep 27 13:28:31 ** Initializing...
Sep 27 13:28:31 ** Initialization complete.
```

You have a functioning installation of DGD. Now you just have to learn how to use it in later chapters!

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

## What is the Kernel Library?

DGD has a bunch of weird, interesting features like atomic functions and persistence. They require a bunch of extra infrastructure to manage properly. You can build it all yourself. Or you can use a structure that handles it. There are a lot of possible structures that ***could*** handle it, and the base DGD language would like you to be able to use any of them.

But there's a specific, common way to handle most of it that you can just put in place and use. It's called the Kernel Library (or Kernellib or Kernel MUDLib.) It divides objects into inheritable versus cloneable by their path for persistence, and defines an atomic-friendly error handling method. It adds permissions and users on top of raw DGD, which doesn't really have those. It adds some "standard library" sorts of functionality which you ***could*** build for yourself but it's nice not to have to.

There are a few reasons you might not want to use the Kernel Library, but normally you would. It's a bit like the C standard library, which can be removed entirely for tiny embedded processors. Despite that, 99.9% of everybody just uses it without a second thought. We ***like*** conventions and we ***like*** a standard library.

The Kernel Library [exists on its own in deprecated form](https://github.com/dworkin/kernellib). There is also a [separate fork maintained by Raymond Jennings](https://github.com/shentino/kernellib). But the author's latest work on it is as part of the Cloud Server.

## What is the Cloud Server?

The Kernel Library is designed to be extremely stable. It's like an operating system interface &mdash; changes to it are rare and its interfaces are kept intentionally as simple as possible to avoid changing often. Those interfaces are often inconvenient, but convenience is simply not as high a priority. You could say all of this about the Linux API for all the same reasons. Libc (the C language standard library) is similar that way too. It is a low-level capability-based interface that should almost never break existing code.

The Kernel is also designed to require no direct modification. You might upgrade to a later version eventually. But when you're using it, it calls hooks into other parts of your application. It's a library, not an example application.

The Cloud Server is different. It's built on top of the Kernel Library. But it includes a web server, an LPC parser designed for building LPC-on-top-of-LPC, various data structures and iterators and other standard library stuff. In the past it has included a little bit of text-game content. It's intended to be a playground and showroom for some of DGD's more interesting features. But it changes occasionally. Some of its features may require modification to be useful for you. You might find yourself forking (copying and modifying) from some of the base parts of the Cloud Library.

It's not an OS interface. It's not really a standard library, though it contains useful general-purpose code.

Most of this book assumes you're happy using the Kernel Library and/or the Cloud Server. There will be a chapter toward the end that explains what raw DGD is like without it.

In the tutorial, you'll clone the Cloud Server and use it. But mostly you'll be using the Kernel Library.

Why clone the whole Cloud Server, then? Because it's small and it has a few useful extra changes. It's also the most up-to-date version of the Kernel Library. And if you decide to poke around inside it, it can show you a lot about how to use the Kernel Library. Sample code for DGD is hard to find and the Cloud Library is a high-quality source of examples written by the author of DGD.

## How Do I Keep Track?

I'll try to distinguish between the Kernel Library and the Cloud Server in this book, though sometimes it's hard. However if you learn both of them together, you probably won't have a great understanding of where one ends and the other begins. That's okay. You'll learn when and how you need to, but only ***if*** you need to.

If you need to, the first thing to do is to read the source code for the Cloud Server and Kernel Library.