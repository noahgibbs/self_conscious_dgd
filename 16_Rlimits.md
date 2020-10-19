# Rlimits

DGD permits your code to specify how much CPU ("ticks") and stack space a given operation is allowed to take. That looks like this:

```
static void create(varargs int clone)
{
    rlimits(50; 1000) {
        this_user()->message("Luckily, this won't take too many ticks or too much stack.\n");
    }
}
```

If the code inside runs for too long or tries to call too many layers of sub-functions, DGD will raise an error. This is useful for "sandboxing" code and making sure it doesn't use up too many resources. Not surprisingly, the Cloud Server will sandbox ***you*** when you're running code. That way a single infinite loop in a "code" command won't halt the whole application.

If you make the rlimits numbers above much smaller, like 10 and 10, you'll instead see an "Out of ticks" message. That means trying to send you a message cost more than 10 ticks and got an error. You didn't catch the error so it just showed up on the telnet connection.

## Ticks

Ticks are counted by DGD per operation. While the amount of CPU cost for a given operation can vary (swapping from disk, different processor type, etc.), the number of ticks is entirely predictable from the operations themselves. The tick count for an operation is also portable across different processors and hosts.

That means ticks don't directly translate into CPU cost. It's not as simple as multiplying the number of ticks by some constant to get processor time or wall-clock time. But ticks translate operations into processor time at some range of rates - there is some maximum and minimum amount of processor that can be represented by ticks. With luck it's a fairly narrow range (that is, every operation's CPU cost per tick is pretty constant) and with less luck it's still a limited amount of "bad." The fact that an operation's cost in ticks is predictable is, frankly, more important than having it predictably translate to an exact processor cost.

The default maximum number of ticks for the Cloud Server is around 5 million, though it's possible to change it per-user.

You can get the current remaining number of ticks using the status() kfun:

```
#include <status.h>

void create(void) {
    this_user()->message("Stack depth limit: " + status()[ST_TICKS] + "\n");
}
```

Keep in mind that this is the remaining number, so it should change over the course of the function:

```
#include <status.h>

void create(void) {
    int *a;

    this_user()->message("Tick limit: " + status()[ST_TICKS] + "\n");
    a = allocate_int(1000);
    this_user()->message("Remaining tick limit: " + status()[ST_TICKS] + "\n");
}
```

This might output the following:

```
Tick limit: 4998718
Remaining tick limit: 4996358
```

## Stack Depth

The stack depth is normally measured in ***calls***, or ***stack frames***. In other words, if your maximum remaining stack depth is three then you could have a function call a function call a function, but no further inward. If the remaining depth is zero, you can't call any more functions.

The Cloud Server has a default maximum depth of 100, which is pretty good for most cases. It's possible to set the limit higher if needed. But it usually makes more sense to use something like call_other() to divide the work across multiple timeslices rather than do a gigantic chunk of work with many function calls.

You can get the current remaining stack depth using the status() kfun:

```
#include <status.h>

void create(void) {
    this_user()->message("Stack depth limit: " + status()[ST_STACKDEPTH] + "\n");
}
```
