# Control Flow and Data Structures

Frankly, DGD isn't likely to surprise you in this category if you're used to languages descended from C syntax, as most general-purpose modern languages are (e.g. Java, C++, C#, JavaScript, Perl/Ruby/Python.)

## For Loops

A for loop in LPC uses parentheses around three clauses separated by semicolons. Here's one way that can look:

```
static void create(varargs int clone)
{
    int i;

    for(i = 0; i < 10; i++) {
        this_user()->message("Iteration number " + i + "\n");
    }
}
```

The parens and semicolons are mandatory. You can't skip them. That can occasionally give oddities like how infinite loops work:

```
static void create(varargs int clone)
{
    int i;

    for(;;) {
        this_user()->message("Iteration number " + i + "\n");
        i++;
        if(i > 5) break;
    }
}
```

Notice that you can use "break" to break out of a loop. A "continue" will begin back at the start of the loop, but on the next iteration. And a "return" will return from the entire function, which will also break out of the loop.

## While Loops

```
static void create(varargs int clone)
{
    int i;

    i = 0;
    while(i < 10) {
        this_user()->message("Iteration number " + i + "\n");
        i++;
    }
}
```

## Do/While

There's also a do/while loop:

```
static void create(varargs int clone)
{
    int i;

    i = 0;
    do {
        this_user()->message("Iteration number " + i + "\n");
        i++;
        } while(i < 10);
}
```

## Each/Foreach?

Nope. We'll cover iterating over arrays and hashes later, but it's going to be with a for loop.

## If/Else

LPC has if/else that's basically identical to C or JavaScript:

```
static void create(varargs int clone)
{
    if (7 == 7) {
        this_user()->message("Yes, seven is equal to seven.\n");
    }

    if (random(2) == 0) {
        this_user()->message("And you won the coin flip!\n");
    } else if (random(2) == 0) {
        this_user()->message("And you lost the first coin flip, but won the second!\n");
    } else {
        this_user()->message("And you lost two coin flips in a row. Unlucky!\n");
    }
}
```

The example above could be a fun one to just clone repeatedly &mdash; you should see different results each time. That's because random() returns a different result each time you call the function.

## Goto

LPC is a bit old-fashioned. It has "goto", which most modern languages prefer to avoid. Still, sometimes goto can be useful! It uses labels to determine where to go.

```
static void create(varargs int clone)
{
    int i;

    i = 0;
    start_of_loop:  /* A label is an identifier followed by a colon. */
    this_user()->message("Iteration number " + i + ".\n");
    i++;
    if(i < 10) goto start_of_loop;
    this_user()->message("Finished!\n");
}
```

## The Ternary Operator

LPC also has the "ternary" operator. That name just means it takes three arguments, as opposed to "unary" (one arg) or "binary" (two args.) It's the one with the question mark and colon you may recall from Javascript that acts as a sort of if/else:

```
static void create(varargs int clone)
{
    this_user()->message("And you " + (random(2) == 0 ? "won" : "lost") + " the first coin flip!\n");
}
```

## Errors and Catching Them

DGD allows you to throw an error with the error() function: `code error("Oh noes!");`. The error object has to be a string.

You can also catch one with the catch keyword, which can be used in two different ways.

```
static string try_something()
{
    if (random(2) == 0)
        return "bobo";
    else
        error("This is awful!");
}

static void create(varargs int clone)
{
    string err, result;

    err = catch(result = try_something());
    if (err)
        this_user()->message("We got an error when we tried something!\n");
    else
        this_user()->message("When we tried something, the result was " + result + ".\n");
}
```

If you clone this object repeatedly, you should see text about an error about half the time. The other half the time, the result will be "bobo."

There's another form of catch that LPC supports:

```
static string try_something()
{
    if (random(2) == 0)
        return "bobo";
    else
        error("This is awful!");
}

static void create(varargs int clone)
{
    string result;

    catch {
        result = try_something();
        this_user()->message("When we tried something, the result was " + result + ".\n");
    } : {
        this_user()->message("We got an error when we tried something!\n");
    }
}
```

Notice that there's no obvious way to get the error object from the catch if you use this form.

## Switch

Like in C, you have to manually "break" out of each case in the switch or LPC will begin executing the next one.

```
static void create(varargs int clone)
{
    switch(random(3)) {
    case 0:
        this_user()->message("Very lucky roll!\n");
        break;
    case 1:
        this_user()->message("Very okay roll.\n");
        break;
    default:
        this_user()->message("Very unlucky roll :-(\n");
    }
}
```

## RLimits

This one is specific to DGD: rlimits. DGD permits your code to specify how much CPU ("ticks") and stack space a given operation is allowed to take. That looks like this:

```
static void create(varargs int clone)
{
    rlimits(50; 1000) {
        this_user()->message("Luckily, this won't take too many ticks or too much stack.\n");
    }
}
```

If the code inside runs for too long or tries to call too many layers of sub-functions, DGD will raise an error. This is useful for "sandboxing" code and making sure it doesn't use up too many resources. Not surprisingly, DGD will sandbox ***you*** when you're running code. That way a single infinite loop in a "code" command won't halt the whole application.

If you make the rlimits numbers above much smaller, like 10 and 10, you'll instead see an "Out of ticks" message. That means trying to send you a message cost more than 10 ticks and got an error. You didn't catch the error so it just showed up on the telnet connection.

