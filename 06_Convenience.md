# On DGD, Old Tools and Convenience

One thing you're going to notice rapidly about DGD is that it's ***inconvenient***. That's a lot of why it never got popular. Telnet is a terrible UI by modern standards. DGD Documentation is hard to find and I wrote some ungodly large percentage of it, first in 2003-or-so and then again in 2020.

This means you're going to need a couple of approaches: workarounds and building up.

## Workarounds

When you first start using DGD, you're going to be looking for workarounds. Can I use my local text editor instead of editing inside telnet? Great, let's do that. The wiztool shell is terrible, so can I cut-and-paste my commands repeatedly from a text file? Great, let's do that.

There's nothing wrong with workarounds. They help your experience. They're a great way to feel clever. They show you the ropes. They start building your experience.

But over time they slow you down and annoy you.

## Building Up

DGD is a programming environment. That means you can do a lot to make it more convenient for yourself.

Have a look at /kernel/obj/wiztool.c (the text-UI interface for admins) and you'll quickly see that there's not much to it. Some commands, a bit of text parsing and ***nothing*** else. One joyful consequence of that is that once you're ready to edit or override it, you can do a lot.

Want to rebuild a particular source file before every command? Easy. Or add a command to repeat the previous command? Not hard.

You'll discover rapidly that the Kernel Library is a sort of miniature, rough, old-style operating system. It comes with very few conveniences. The hard part is that it feels very rough. The opportunity is that you can put in a shockingly small amount of effort and get a much better result out of it.

## Letting Me Do It

I've also produced a few tools for DGD. I'll recommend them to you to start with. You may stick with them. You may augment them. You may outgrow them. Regardless of how they go, you're certainly welcome to them. If they do anything useful for you, you're welcome.
