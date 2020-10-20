# The Error Manager

The Cloud Server's driver allows you to set an error manager which will be notified when an error occurs.

The Cloud Server will do this for you, and report your errors to you if you are logged in when they happen. You could replace the Cloud Manager's own error manager with your own if you were so inclined. You can read over its usr/System/sys/errord for an example implementation of an error manager.

```
void set_error_manager(object errord)   [System only]

    Install an error manager, in which the following functions can be called
    afterwards:

    * void runtime_error(string error, int caught, mixed **trace)

        A runtime error has occurred.

    * void atomic_error(string error, int atom, mixed **trace)

        A runtime error has occurred in atomic code.

    * void compile_error(string file, int line, string error)

        A compile-time error has occurred.
```
