# Running Without the Cloud Server or Kernel Library

You've learned a lot throughout this book about what DGD does. You've also learned a lot about working with the Kernel Library and the Cloud Server. You're probably not very solid on which parts come from which of those three. That's fine.

The Kernel Library defines a nice solid layer, a sort of concensus reality, around "what does a DGD application look like?" Let's talk about some of the weird worlds beyond it.

## Persistence and Old Code Issues

The Kernel Library has a bit of a straightjacket approach to different versions of an object. When you recompile a library, you quickly get to the point where only the newest version of that library exists anywhere in your application.

That's not an ironclad rule. It's just a thing the Kernel Library does. What if it didn't?

The answer is that you'd probably have to track many old "issues" (versions) of that library's code somehow. Your Object Manager can absolutely do that if you want it to. But it means that if a child object was compiled awhile back and has an old version... Well, then it was compiled awhile back and has an old version. Unless you destroy it and recreate it, why would that change?

You'll probably want some kind of rules around old issues, and some kind of handlers. For instance if you know that some old tracking code is bad, you can't fully fix it until all the old bad tracking code is gone (recompiled away.) So you may need to recreate most of the objects in your application. That can be a bit of a chore.

You can see why the Kernel Library does it the way it does. But there ***are*** alternatives. They just may require a lot of code.

Or you can drop support for DGD-style persistence. This problem goes away if your application doesn't have to stay up for a long time. Many languages get away with no recompiling their objects at all.

## Permissions and Directories

The Kernel Library imposes a simple, somewhat weird permissions system. But there's no reason there has to be one. And there's no reason it has to look like the Kernel Library's setup.

The Kernel, not DGD, imposes the /usr directory structure on applications. You could pick your own instead. You wouldn't even ***have*** to do it with users and directories, though most permissions systems choose to. But the Kernel's idea that the calling code and the object's path define the permissions isn't set in stone. You could imagine checking the current user and handling all permissions that way, for instance (though it would have to work completely differently.)

A lot of these issues are because the Kernel Library expects its applications to be highly multiuser, and perhaps multitenant. That is, that you'll have components running semi-trusted operations and they're supposed to hide data from each other. It's the same reason that DGD is so focused on sandboxing and resource limits. All of this is the legacy of running multiuser games with code from a lot of different builders. If you wrote an application that didn't work that way, you would have completely different production issues.

A lot of the difference between a MUD and World of Warcraft isn't the graphics or the physical models. A lot of the difference is that a MUD uses volunteers to write code while World of Warcraft has a small, controlled team and you can review all the code they write before you deploy it. The Kernel Library is designed around volunteer code in a way that few languages or libraries are.

That's also why DGD has some things in common with Javascript. Your browser is one of the other places on your computer where you run a lot of somebody else's code and you only partially trust it.
