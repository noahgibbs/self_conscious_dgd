# Why DGD?

I said DGD is weird. What's weird? What makes DGD unusual?

## No Console - DGD Likes Networks and Files

DGD doesn't normally operate by reading input from your keyboard and printing output back to you. It can print output, but that's not frequent. Instead, DGD is designed for a networked environment where it will read and write files, and read and write network sockets.

Older DGD servers tended to use a very simple network protocol called "telnet". Modern DGD servers frequently use HTML, and can also use SSH for command-oriented sessions.

DGD also lets you read and write files. So if you're wondering, "how can I debug and get error messages with no console?", that's the answer. DGD *can* also print to console, though it's not particularly common or encouraged.

You can find a few other languages with similar properties. Scratch, for instance, is a Smalltalk variant with a visual programming environment that only really works via web browsers and certainly doesn't have a useful text console.

## Tasks (a.k.a Timeslices)

DGD normally runs as a series of "tasks," which are also sometimes called "timeslices." This is a lot like an evented platform such as Node.js, and a bit like an actor model. You can find similar ideas in Scala and Erlang, for instance.

(Both names, "tasks" and "timeslices" are used in a variety of ways, making this hard to refer to. Similarly you could call them "events," which is even more ambiguous.)

A timeslice is a short period where code runs in response to some event occurring. The timeslice might run because it's time (e.g. "run every 10 milliseconds") or because network packets have arrived. DGD defines a few "top-level" events, but it's common for other DGD code to schedule more events afterward as well. This, too, is a lot like Node.js and other evented platforms.

Timeslices are weirder than they seem - see "atomicity" below.

A timeslice can only run for a short time, much like a Node.js event handler. DGD has some built-in runtime limiters so you can guarantee that a "runaway" function call doesn't go for too long. Instead DGD can interrupt them to guarantee a maximum timeslice length of your choice.

Limiting the timeslices is important because of some things DGD does in between them.

## Recompiling

Some languages are highly dynamic - they allow changing "everything" after the process has already started running, even without stopping and restarting. The Ruby language, for example, is (in)famous for allowing you to add or change methods and classes after your process is already up and running. Even relatively staid languages like Java allow for some amount of this. Old-fashioned, simple compiled languages ("static languages") like C are the opposite. They decide the structure up-front and if you want to change the definition of a function or add a data type, you can stop one running program and start a new one, please and thank you.

DGD is a fairly dynamic language. It doesn't allow quite the flexibility of Ruby (few languages do,) but it allows far more than C++, and mostly more than Java.

It does it by allowing you to recompile individual objects, one at a time, in between timeslices. Then you can update all the old objects to the latest version -- not just of the code, but any data updates that go with it.

You can ask DGD to recompile an object. After your timeslice has finished, DGD will recompile the object's code and figure out how to update existing objects to the new code if it can. A modern DGD application will make sure it's possible to update all objects -- some older DGD codebases would leave "orphan" objects around with obsolete code attached and you'd have to figure out how to clean them all up. This guaranteed cleanup and upgrade is one of many problems that the Kernel Library can solve for you.

This means that over time you can keep updating a single running DGD process to the latest code, potentially for many years. The record as I write this is a DGD application with an effective uptime of over twenty years.

## Atomicity

One of the most powerful features in DGD is its atomic functions. Any function in DGD can have the "atomic" modifier added. If an atomic function gets an error, it will revert everything the function did.

If you're familiar with databases, this is a lot like a transaction.

If that function changed the value of a global variable and it gets reverted, then that change is gone. If the function wrote to files or to the network, and it gets reverted, the write won't have happened. The function can return an error, but all other changes are completely gone, as though the function had never been called.

That probably sounds impossible, and it's made possible only through the magic of timeslices.

When you write to network connections or files, DGD doesn't instantly send that out into the world. Instead, those writes are held, buffered, until the outermost atomic function completes and/or the end of the timeslice. Since DGD is guaranteed a short "management" period in between timeslices, it can do things like file and network I/O at that time.

DGD has a more powerful older sibling called Hydra that uses this trick for multiprocessor performance. It can run future timeslices in a speculative way. Then if they conflict with anything else you were doing, Hydra can roll them back as though they never happened, to be restarted later. But if there were no conflicts, they will commit (finish successfully), and your single-process code got run fully in parallel without you having to change a thing. This can double (or more) the speed of your DGD application if it's written correctly.

## Natively Disk-Based

DGD is "natively disk-based." By that, I mean that objects in DGD prefer naturally to be "at rest" on the disk, not live in memory. Each DGD object manages its own memory arena, with limited ability to link into the memory arena of other objects. Unless you constantly touch a given object, DGD will swap it out onto disk. DGD does this quite aggressively.

If you're used to a standard memory model, this probably sounds quite bad. Swapping is bad, right? But DGD does it continuously, which minimizes the memory that's actually in use (the "working set"). Minimizing that is useful and powerful.

That also makes it extremely cheap to keep a copy of all your in-memory objects, since they're mostly already on the disk with minimal (or no) modifications.

Why is that useful? Keep reading.

## Snapshots

DGD is designed around the idea of Snapshots, also known as "statedumps." A snapshot can act as a live backup, where it writes out the contents of its memory and then keeps running. Taking a snapshot is also allowed to stop the process, so there would be an on-disk backup but no running application.

The live backups are powerful in the normal way. Keeping a live backup of your objects in memory is quite difficult in most languages. DGD makes it trivial.

Stopping a process with a fully-current backup makes it very easy to restart the process at exactly the same point, without having to manually write out the objects in use and do a full restart afterward. DGD objects are often written as if they were immortal, with minimal shutdown or startup code, because with snapshots ***they can be***.

Not every DGD application chooses full persistence using this method. But DGD leaves the option open, and it can be very powerful.

## Memory Arenas

If DGD is natively disk-based and most of (what would normally be) memory contents are actually on disk, how does one deal with references to other objects?

DGD handles this by declaring each "object" to manage all its own memory. Any arrays, structures (called Lightweight Objects or LWOs) or similar that an object points to is in its own memory area. If an object passes an array or LWO to another object, then at the end of the timeslice, that new object will make its own copy. So an object can pass a smaller-than-object chunk of memory around briefly, but only briefly. After that, each object holds its own copy, separately.

That means that the boundaries between objects in DGD are very important to performance, and even to the semantics of DGD as a language. You can pass around a reference to the internals of another object... briefly. But you can't hold onto it for more than a timeslice, which isn't long at all. In general, objects hold references to other objects, and if necessary have some way to query information from their internals. This should sound a lot like getters and setters, but with more enforcement. Effectively, each object will ***copy*** information from its internals, but you can't usefully ***keep a reference*** to anything in its internals, ever.

If this sounds unfriendly compared to languages you're used to, remember that DGD makes it easy to dump, reload and recompile the internals of every object, everywhere. Having references between their internals is a bad idea in such a world.

## So Then...?

All of these things together make a few particular clusters of oddity. DGD is designed for producing persistent (i.e. they don't reboot/restart) networked servers with complex state transitions (think: atomics, which are like DB transactions). It should make sense that this grew out of old massively-multiplayer games.

All of the types of oddity above are about DGD itself. There are some later types of weirdness that are about the Kernel Library. That is, they're about ***how to manage*** the situation, rather than the situation itself.