# Object Management

Object Management is one of the deeper topics in DGD, historically. An Object Manager can define its own set of permissions and a full set of lifecycle callbacks. It tracks what objects are outdated and which are recompiled. Your specific object manager's implementation can add a lot of new "rules" to your application.

And historically, the Kernel Manager ships with no object manager at all. You'd often have to write your own.

There have been several historical object managers for DGD.

* Geir Harald Hansen wrote one for an academic thesis about DGD
* I wrote one for my Phantasmal MUD Library
* Skotos.net wrote one for their games, called ProgDB
* The Cloud Server's built-in object manager is probably the first ever developed and was written by Erwin Harte long ago

The fourth object manager is what you are currently using. You're allowed to override and replace it if you like.

## An Object Manager Tracks Objects

The Kernel Library already has an ObjRegD which tracks basically all objects by their owner. That's important when checking quotas on how many objects each owner is permitted to have.

The Object Manager normally does more tracking. What objects does each object inherit? What files does it include? What issue (version) of each object's code is it using?

## Tracking and Preventing Inheritance

## Tracking and Preventing File Inclusion

## Tracking and Preventing Cross-Object Function Calls

## Upgrading Objects

When an object is recompiled, all instances of it will often need to be upgraded. They need to use the new code. They may have new data fields, or have old data fields deleted.

Often, a particular application might want to do other work. If you want to replace a data field, it may make sense to first add the new field and copy over the values from the old one.

Your object manager is allowed to define a function to be called on upgraded objects, allowing them to do custom work. The Cloud Server's default object manager allows you to define a "patch" function on an object to do this.

## Destroying Objects

Sometimes the last reference to an object is removed and that object goes away. It can also be manually destructed (and some [object types](11_ObjectTypes.md) have to be.) Your object manager will need to be aware when this happens and unregister it.

It's possible to create a destructor function that is called when the object is destructed. The Cloud Server default Object Manager doesn't do that, though.

## The API

Want to write an object manager? Here are the hooks it will need to support:

```
* void compiling(string path)

    The given object is about to be compiled.

* void compile(string owner, object obj, string *source,
               string inherited...)

    The given object has just been compiled.  If the source array is not
    empty, it was compiled from those strings.  Called just before the
    object is initialized with create(0).

* void compile_lib(string owner, string path, string *source,
                   string inherited...)

    The given inheritable object has just been compiled.  If the source
    array is not empty, it was compiled from those strings.

* void compile_failed(string owner, string path)

    An attempt to compile the given object has failed.

* void clone(string owner, object obj)

    The given object has just been cloned.  Called just before the object
    is initialized with create(1).

* void destruct(string owner, object obj)

    The given object is about to be destructed.

* void destruct_lib(string owner, string path)

    The given inheritable object is about to be destructed.

* void remove_program(string owner, string path, int timestamp, int index)

    The last reference to the given program has been removed.

* mixed include_file(string compiled, string from, string path)

    The file `path' (which might not exist) is about to be included by
    `from' during the compilation of 'compiled'.  The returned value can be
    either a string for the translated path of the include file, or an
    array of strings, representing the included file itself.  Any other
    return value will prevent inclusion of the file 'path'.

* int touch(object obj, string function)

    An object which has been marked by call_touch() is about to have the
    given function called in it.  A non-zero return value indicates that the
    object's "untouched" status should be preserved through the following
    call.

* int forbid_call(string path)

    Return a non-zero value if `path' is not a legal first argument
    for call_other().

* int forbid_inherit(string from, string path, int priv)

    Return a non-zero value if inheritance of `path' by `from' is not
    allowed.  The flag `priv' indicates that inheritance is private.
```
