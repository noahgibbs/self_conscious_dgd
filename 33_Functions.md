# Functions

## Static

A static function can only be called inside the object it is defined in. That includes inherited objects &mdash; it can be seen by its parent or child objects. But it's effectively invisible from outside the object itself.

It can be masked by an inheriting program.

## Private

A private function can only be seen or called from inside the object it is defined in, ***not*** including parent or child objects. It can't be masked. It can't be called from another outside object.

## Nomask

A nomask function can't be masked. In other words, an object that inherits from this object can't declare another function with the same name to replace it. Attempting to define a masking function is a compile error.

## Atomic

An atomic function will have no effects if it is aborted by an error. Network I/O, file I/O and global variable writes will all be reversed if they occurred inside an atomic function and an error is then raised. Certain operations are not permitted inside an atomic function. Atomic functions use more processor and memory resources. They cost double the normal number of ticks when they run inside rlimits().

For more about atomic functions, see a later chapter. They're one of the most powerful features of DGD, but they can be tricky to use well. In effect, they are database transactions for the language itself.

## Scope and Inheritance

By default if you call a function on an object, you call that object's version of that function.

If the object inherits from one or more parents, it's possible from that object to call an inherited version of the function.

In general, the notation `::function_name` can be used to mean "inherited version of this function." If there are multiple inherited versions of the function due to multiple inherited parent objects and a child object needs access to them, it normally makes sense to name each inheritance to gain access to its same-named functions via a parent name.

For example:

```
inherit access API_ACCESS;
inherit rsrc API_RSRC;

static void create(void) {
    access::create();
    rsrc::create();
    /* ... */
}
```

## Varargs Functions

A function can have varargs arguments, indicating that they are optional. You've seen this with the "varargs int clone" argument to create(), for instance.

Starting with the argument marked "varargs", that and all later arguments are optional. If the call doesn't include them, they will be uninitialised. An int varargs parameter will default to 0, a string or mapping will default to nil and so on.

### "..." - function definitions

After a final parameter, a function definition may contain "...":

```
static object compile_object(string path, string source...)
```

"Source," in this case will be an array of strings rather than a single string. Note that the array can be of length zero if no strings were passed for the final argument.

By using a "mixed" as the final argument type, any number of arguments of any types may be supplied.

### "..." - function calls

To pass multiple arguments to a function, there is also a "..." operation in the function call.

Specifically, a call may include "..." immediately after the final argument, to convert a final array argument to multiple parameters:

```
if (sizeof(source) != 0)
    compile_object(path, source...)
```

You can pass a zero-length array with "..." and it will pass zero additional arguments.

## kfuns, afuns, efuns, hooks, lfuns, applies...

DGD and LPC have some unusual ways it talks about its functions. You'll especially notice this in some of the documentation.

A DGD "apply" is a function called by the DGD interpreter itself. You won't directly see applies if you're using the Cloud Server &mdash; DGD calls the Cloud Server, which might then call to your code.

A "hook" is a function you define that is expected to be called by somebody else. For instance, if you call DRIVER->set_object_manager(my_object) then you're saying my_object has defined a lot of different functions, which you expect the Cloud Server will call from time to time. In this example, my_object would have its function "compiling" called whenever an object starts being compiled. Compiling is a "hook" function, called by the Cloud Server.

An afun (also called an efun) is defined by the Cloud Server's auto object, and is thus available in any object anywhere. Something like find_object() or call_other() or get_dir() would be an efun. Note that an efun can sometimes shadow built-in DGD functions. In that case your objects will see the shadowed version defined by the Cloud Server rather than the built-in DGD type. If you see multiple pieces of documentation for the same function and it has different numbers of arguments (example: clone_object), that's often why.

Built-in DGD functions are called kfuns. They're implemented by the DGD interpreter itself using C++ code. If they're not shadowed (see efun above), your code can call them from anywhere.

An lfun is a function defined by one of your objects, not by DGD or by the Cloud Server. For instance, you define create() in your object, to be called later by the Cloud Server. It is both a hook and an lfun.

Older LPC dialects often had widely varying behaviour between these function types in which ones were visible where. DGD mostly has a simpler system where you don't (mostly) need to worry exactly where a function was defined, and DGD-defined kfuns act like Auto-defined efuns act like inheritable-defined lfuns from the point of view of an inheritor.

## Recursion

DGD permits both direct recursion and mutual recursion. To call a function that isn't defined yet, a function prototype is generally the best choice (see [Program Structure](12_ProgramStructure.md).)
