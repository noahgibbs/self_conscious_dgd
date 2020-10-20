# Caller-Based Security

If you're sandboxing, it's important to be able to control what parts of the system can do different things.

The Cloud Server has a somewhat unusual model for that based on naming and the call stack.

## The Call Stack

In many dynamic languages it's easy to answer the question "who is calling this function?" DGD has several built-in functions (kfuns) that help with this.

`call_trace` returns an array of information about all callers, from the most immediate to the top of the stack. Offset zero is the very topmost caller, while the largest offset is the call to the current function itself.

previous_object returns the object that called this function, while previous_program returns the ***name*** of the same object. You can pass an argument to either one to get N layers up the stack instead of (the default) one layer up the stack.

## Cloud Server Naming

The Cloud Server imposes a directory structure by users. The /kernel path has extremely privileged code, but you're not supposed to add anything there. The /usr/System path contains the next-most privileged code, and should only be used for code that requires strong permissions. From there you can add other usr subdirectories and choose their level of privilege.

How does it impose this structure?

Many privileged operations in code under /kernel checks the caller's program name. If it isn't under /usr/System, the operation isn't performed. So if you want other code to be allowed to perform these operations, you must write a wrapper in /usr/System that verifies the inputs (and perhaps the caller) before performing the operation by calling into /kernel code.

This strongly encourages a layered architecture. /usr/System can also add layers by only allowing certain code in certain /usr directories to perform certain operations.

The other common check is to allow only one specific object to call a particular function. The Cloud Server's driver object has a compiling() function that it expects the Auto object to call when an object is being compiled. It checks and if the caller isn't the Auto object, it simply ignores the call. This is very similar to objects that require a /usr/System caller, but it's even more specific.

## Private Functions, Static Functions, Calling Out

You're already used to some kinds of caller-based security, of course. It's common in many languages to declare functions as "private" or "protected" and that restricts who can call them. Examining the call stack is mostly an extension of that same idea.

In DGD a "private" function can only be called from inside the same program that defined it. If you inherit from a library you still can't call its private functions. Private functions and private data fields are private just to that one program, regardless of things like inheritance.

DGD also has "static" functions. They can only be called by the same object, but they ***can*** be called by child objects that inherit. So a static function in DGD is a lot like a "protected" function in some other languages. An object can also use call_out to call its own static functions.

DGD has static data fields. They aren't like "protected." You should think of static as doing nothing at all on a DGD data field. Technically it determines whether the deprecated save_object and restore_object kfuns include those fields. But you should never use save_object and restore_object. So static doesn't really do anything on a data field.

What if you want a data field that can be accessed by the object and also by any children? Just don't put "private." DGD doesn't have an equivalent of "public." Your object can't reach into another object and directly manipulate its data fields. Instead you'll need to use getters and setters for that.
