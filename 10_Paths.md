# Paths in DGD

You're used to paths on your computer. That's one kind of path in DGD, too. But there are some others. Let's talk about them.

## The DGD Root

In the tutorial, you probably noticed that there was a DGD root directory, which we called "root". It may have different other names in other DGD applications. But DGD can only see files inside its area under the DGD root.

That's a good way to sandbox your DGD code, keeping it from affecting anything outside DGD. If you configuration file is outside the DGD root, no DGD code could overwrite it and escalate DGD's own ability to cause trouble. If your statedumps are outside DGD, then no code in DGD can read through them and see things it shouldn't.

A path on your computer might be something like "/Users/noah/src/lpc_playground/root/usr/System/initd.c", while the path under your Git (or other source control) root might be root/usr/System/initd.c, and DGD's own path to the file would be usr/System/initd.c.

## The Home Directory

The Cloud Server adds a tilde-path feature, a lot like one you've used in your OS console.

A file in /usr/System/initd.c might be referred to as ~System/initd.c. If you're currently the user "admin" then you could refer to /usr/admin/sys/my_mgr.c as ~/sys/my_mgr.c. Not that tilde-usename means it's in that user's directory, while tilde-slash means "it's under ***my*** home directory."

## Current Directories

You can keep track of a current directory (or multiple.) But DGD won't mostly do it for you - you have to do it for yourself.

The exception to this is that the Cloud Server wiztool has an idea of the current directory if you inherit from it.
