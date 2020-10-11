# The Error Manager

The Kernel Library (or Cloud Server) allows you to set an error manager which will be notified when an error occurs.



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
