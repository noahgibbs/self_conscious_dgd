# Errors, Callers and Stack Traces

## LPC Errors

There are a lot of things that can cause errors. Bad typecasts, syntax errors, code running too long and running out of ticks...

In general, those errors can be caught by other LPC code.

## Error Notifications

DGD will notify the Cloud Server when an error occurs. The Cloud Server has an error manager by default, but you can register your own if you'd rather. See the [Error Manager](23_ErrorManager.md) for more details.

## Fatal Errors

Some conditions are fatal (unrecoverable) in DGD.

Those conditions include:

* Running out of memory and having memory allocation fail
* Running out of stack - (infinite/huge) stack depth
* Running out of sectors in DGD's own swap file (LPC mem usage too big)
* DGD attempting to call an LPC function that does not exist, usually in the Driver object

Fatal errors cause DGD to abort. They cannot be caught by LPC exception handlers.
