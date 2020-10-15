# Types of Objects in DGD

DGD calls the code in a single .c file a "program." Programs can be a lot of different kinds of things.

In DGD, each program manages a small area of memory all its own (a "memory arena.") The object can be in-memory ("swapped in") or exist on disk, waiting to be used ("swapped out.") DGD tries to keep most objects ***out*** of memory most of the time, and brings objects into memory only when somebody tries to use them.

An "object" in DGD is one of these programs that manages its own memory. See "Lightweight Objects" below for one subtlety, though...

## Cloneable Objects

An object with /obj/ in its name, like the ones you've been using in the tutorial, are called "cloneable" objects. You've been using the "clone" command to clone them. Make sense?

They're not allowed to have /lib/ anywhere in their names. You'll see why below under "Libraries." It also can't have /data/ in the name. You'll see why under "Lightweight Objects."

What DGD calls a "clone," most languages would call an "instance." DGD is a little unusual in that the "parent" object exists and gets its own data fields. Then each clone (instance) gets its own data, as you'd expect.

The parent object can be used to store class-wide data. Or it can just hang out and not do much. The parent object has to exist for you to make clones of it, though.

The create() function gets an argument that we've been calling "is_clone." If it's not there (nil) then the object is the parent object. If is_clone is 1, the object is a cloned object. An object can also check its object name (see below) to tell if it's a clone. A parent cloneable object has a name like "/usr/admin/obj/some_object". A clone has a hash sign and an integer object ID, like "/usr/admin/obj/some_object#37".

A clone (either a parent or an instance) can always be looked up by name. DGD won't ever automatically destruct them, because the name acts as a sort of automatic top-level reference to them. They can be destructed manually, of course.

The Cloud Server has automatic per-owner tracking of cloneables, including both parents and instances. So a sufficiently-privileged object can get a full list of all of them that exist.

## Libraries, a.k.a. "Inheritables"

One of the major purposes of the Cloud Server is to separate out inheritable libraries from cloneables. You may remember me mentioning that when talking about persistence and dynamic code upgrades.

To phrase this like a different language would: in DGD, all base classes are ***abstract base classes***. They can't be instantiated. They may have data fields that a ***concrete*** child class will use. But if a program can be inherited then it can't be instantiated ("cloned.")

An inheritable object will have /lib/ in its name, and will not have /obj/ or /data/ in that name.

You can never get a reference to a library object. They exist internally, but you're not allowed to touch them. You can't call functions on them, for instance. If you did, DGD would need to create that object and give it those internal data fields. Instead, you're not allowed to ever get a reference to one of them and they act as if they were pure code with no data attached.

Libraries can be destructed as part of reloading them with new code. But the Cloud Server is very careful not to allow outdated code to stick around. You ***can*** destroy them, but you'd normally only do it as part of a destroy-and-reload operation to update code.

## Lightweight Objects (LWOs)

Certain things in DGD ***don't*** manage their own memory. An array or a mapping lives inside another object. It doesn't get its own managed chunk of memory. A primitive data object like an integer or a string lives inside another object as well.

At the end of DGD's timeslices, it will make copies of those objects so that they don't cross boundaries between memory arenas. Pretend object A returns one of its internal array values to object B, and object B keeps a reference to it. At the end of the timeslice, B will make a copy of that array. It will no longer be shared with A.

Lightweight Objects, or LWOs, are DGD programs that you write, that act like arrays or mappings. In other words, they act like ***values***. They don't manage their own memory. They exist inside another object. If two different objects have a reference to one of them, at the end of the timeslice ***both*** objects will get their own copy, so the LWO doesn't stay shared.

LWOs are like cloneables: there's a parent ("master") object and then a lot of instances. The parent object is compiled just like a cloneable. Instead of /obj/ in its path it has /data/.

LWOs are automatically destroyed when the last reference to them goes away.

## Daemons, Managers, Singletons

A non-library object doesn't have to be cloneable. It can just be a singleton object that can't be inherited, instantiated or created as an LWO.

The convention for manager-type singleton objects is to put /sys/ in their name rather than /obj/ or /lib/. That doesn't actually do anything. It's basically documentation.

Like cloneables, singletons can be looked up by name. They will never be automatically destructed, but they can be manually destructed.

## None of the Above

An object's path doesn't have to include /obj/, /lib/, /sys/ or /data/. They act exactly like the singletons above. A common example of this is "initd" objects which are used to initialise a library or system when it starts up.

## Object Names

An object's name has to match its path. When you compile /usr/System/initd.c, it will get the name "/usr/System/initd" automatically. You can't assign it a different name, or choose a name that doesn't match its path.

Similarly, names with a hash sign and a number (clones, LWOs) aren't optional. You can query the object's name and look for the hash sign to determine if it's a clone or LWO, for instance.

## Object Life Cycle

When an object is created, its create() method will be called if it has one. If you have an object manager in place (see object management) then you can add other "hooks" into the object life cycle. It's common to add a method name that gets called when an object is recompiled, for instance &mdash; the Cloud Server calls this method "patch".

Similarly, it's possible to create your own destructors. But neither DGD nor the Cloud Server puts that in place by itself.
