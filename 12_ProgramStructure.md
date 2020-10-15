# Functions and Program Structure

DGD calls the code in a single .c file a "program." These code files have a particular structure.

The earlier chapters had only extremely simple programs. Let's look at some more typical ones.

## An Example

Here's an excerpt of an example object for you to look over. The rest of this chapter will explore the kinds of code you see here, what it does and how it works.

```
/* ~System/sys/memoryd.c */

# include <status.h>

# include <kernel/kernel.h>
# include <kernel/access.h>
# include <kernel/rsrc.h>

inherit access API_ACCESS;
inherit rsrc API_RSRC;

# include "/usr/System/lib/query_origin.c"

# define MEM_CHECK_INTERVAL 3600

string hostname;
int mem_check_callout;

void get_hostname();

static void
create() {
    access::create();
    rsrc::create();

    /* ... */
}

string
get_hostname()
{
    return hostname;
}
```

## Preprocessor Definitions

In the code above, MEM_CHECK_INTERVAL is a preprocessor definition. It's basically a constant value. It's valid to use from the point in the file where it's defined and below. You can't use it earlier in the file than you define it.

DGD uses a C-style preprocessor to define and mess with simple text-based macros. To put that another way: there's a separate system that sits in front of the actual compiler that transforms your code a bit, before it really gets used. Here's a very simple example:

```
#define HELLO_WORLD "hello, " + "world!\n"

static void create(varargs int clone)
{
    this_user()->message(HELLO_WORLD);
}
```

The all-caps name HELLO_WORLD looks like a variable. But it's actually a text substitution. If you put an integer addition like "7 + 3" as the value, it wouldn't add it up to 10 and keep going. Order of operations would do weird things to it because it's not 10 &mdash; it's 7 + 3. For example:

```
#define YOUR_NUMBER 7 + 3

static void create(varargs int clone)
{
    this_user()->message("Your math result is " + (string)(YOUR_NUMBER * 4) + "\n");
}
```

It prints 19, not 40, because "7 + 3 * 4" does the multiply before the add. YOUR_NUMBER isn't 10, it's "7 + 3" and those aren't always the same thing.

You could, of course, put that in parentheses. And if somebody asks you "why do you put all those constants in parentheses?", now you can tell them exactly why.

In addition to #define for constants, we have #if and #ifdef and #else and more for conditionals. We'll cover that more in a later chapter. For now, the important two are #define and #include.

## Includes and Headers

\#include will include the contents of a file where you tell it to. For instance, "#include <kernel/kernel.h>" will find kernel.h and drop the contents into the file where you put the \#include. It's basically cut and paste.

Like the C language it descends from, LPC has two types of includes. One uses angle brackets, while the other uses double-quotes. When including, DGD checks the normal set of include directories to find the file it was told to look for. The only difference with double-quotes is that DGD will first check the absolute path given and ***then*** check in the include directories. What's the "normal" set of include directories? They come from your Configuration File (see later chapter.)

Why do you care about double-quotes versus angle brackets? Usually, they're mostly used idiomatically - angle-bracket include is often for things like the Cloud Server's own files, indicating they are "system" files, while double-quote includes are mostly used for headers from your own application.

Inclusion isn't like inheritance. Inclusion just substitutes the contents of the included file into the program, exactly as though you had cut-and-pasted it into place. It's not clever about it. There's no special data hiding or anything.

Files meant to be included normally have a ".h" extension for "header", and normally consist of constants and function prototypes. Less commonly they might contain private functions to be used by other functions in the program. It's possible for them to contain other code, but it's unusual.

In the file above, there are includes of Cloud Server files like kernel/access.h. Those define constants like API_ACCESS for our program to use.

There's also an include for "/usr/System/lib/query_origin.c". In that case, it's a file with an actual function in it. Instead of using "inherit" to get the function through a parent object, the example above just puts a copy of the function into the source code directly, getting its own copy. That can be wasteful if a function is used a lot since there's one copy of the code for everybody that uses it. But it's allowed, and sometimes it's useful.

## Program Variables

In the example code, there are some program variables:

```
string hostname;
int mem_check_callout;
```

Those are variables that exist in the object once it's been compiled. If the object was cloneable (this one isn't) the clones would each get their own hostname and mem_check_callout variables as well, as instance variables.

In an inheritable (library) program, the original program doesn't have variables you can actually use. You're not allowed to touch them. But if a singleton object inherits from it, it will have those variables. And if a cloneable inherits from it, it will have them and so will all its clones.

You can think of these as fields in a class. They have a type like "int" or "string". They have a name. And they can have some other modifiers, "private" and "static."

### static

Static program variables won't be affected by the (mostly obsolete) save_object and restore_object kfuns. You'll rarely see static used, and it basically never makes the slightest difference.

Save_object and restore_object are leftovers from older pre-DGD versions of LPC. They're not normally used in DGD-based MUDs.

Note that statedumps are ***not*** obsolete, and the "static" modifier has no effect on them.

### private

A private program variable can't be accessed by other classes, even if they inherit from this program. Only this program can touch its private variables.

## Function Prototypes

A function prototype says "this function will exist, but I'm not giving its full definition yet."

In the example above there's one:

```
void get_hostname();
```

This would allow the create() function to call get_hostname(), even though get_hostname() isn't actually defined until after create().

## Function Definitions

### Create()

If a singleton or cloneable program has a create() method, it will be called when that program gets a function called. If you're using the Cloud Server, that normally means immediately when it's cloned, but not necessarily when first compiled.

Create() will be called with no arguments for a cloneable parent object or for a singleton. It will be called with the integer 1 as the only argument for a cloned object.

Inheritable objects can define create(). Then in the case of single inheritance an inheriting program that doesn't define its own create would call them automatically. But usually, an inheriting program will need to call create() in its parent class(es) for itself.
