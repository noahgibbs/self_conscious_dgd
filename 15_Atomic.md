# Atomic Functions

You can add the "atomic" modifier in front of a function's name. You can then call it as normal.

When you do, what changes?

## Fully Complete or Never Happened

If the function finishes without an error, mostly it's the same as a non-atomic function. If you wrote to global variables, for instance, they're changed as you would expect.

But if the function had an uncaught error, your changes are reverted. Writes to variables never happened. Network writes never happened.

## Limitations and Differences

There are a few ways that even an error-free atomic function is different from non-atomic function.

Atomic functions count as double-cost for ticks. That reflects the fact that an atomic function requires more memory and CPU resources even when it succeeds.

An atomic function isn't allowed to write to files or directories, or to use the DGD built-in editor.

## Multiple Levels

An atomic function is allowed to call another atomic function. This can lead to situations where the inner function gets an error and is reverted, but the outer function completes successfully. In those cases the reverted part is reverted and the completed part is completed.

Similarly, if a call in atomic function A to atomic function B completes successfully, its changes are just to the state inside atomic function A. If A completes successfully as well then all the changes are kept. If A gets an error, all the changes are reverted (thrown away.)

DGD refers to these nested levels as "dataplanes." When an atomic function finishes, it "commits to a dataplane." If it was the outermost atomic function that means its changes are kept as normal. If it was an inner atomic function (inside another atomic call) then its changes become part of another atomic layer and they will either be kept or thrown away when that atomic function call finishes.

Atomic functions cost double the normal number of ticks, but it's not "double per level." Code inside three levels of atomic function calls costs twice as many ticks, not eight times as many.

## Error Handling

Often when something goes wrong, you'd like to keep some information about the error. What about inside an atomic function call?

You can't write to an error logfile since file writes aren't allowed inside atomic functions.

No problem, right? Just add an error manager that stores an array of strings. Then you can log to that error manager. Except... that gets reverted when the atomic function gets an error.

Anything that isn't disallowed gets reverted. So how do you store information about the error?

Part of the answer is "you don't." There's no special not-affected-by-atomic state that you can write to, just like a database doesn't normally keep special "non-transaction-able" table rows.

But in a practical sense you can put anything you want into the string DGD passes to "error." That lets you pass other data (e.g. stack trace, internal state) encoded into that string, and your error manager can decode it on the other side. But then, DGD has a maximum string length, which can limit the amount of information that can be retrieved...

## Driver::atomic_error

All this encoding and decoding could be inconvenient, so the Cloud Server added a method to do it more automatically: send_atomic_message and retrieve_atomic_messages. Under the hood it works a lot like the above: collect atomic messages inside an atomic function call. Then if an error occurs, the messages can be encoded with the original error string and passed out to the surrounding context. The send_atomic_message method takes a string to be passed outward, and retrieve_atomic_messages gets any and all messages that were passed from inside.

This method is used in the Cloud Server to do atomic compilation of objects but pass compilation errors out to the surrounding context if it fails.

Underneath, this is accomplished because the driver object can replace the error string for atomic_error and runtime_error. That way, atomic error strings are replaced with their encoded version, while the runtime_error generated ***outside*** the atomic context is replaced by its decoded version.

## C Extensions and JIT

There are various "shortcuts" you can use to optimise DGD. DGD has built-in JIT ability. How does that affect atomic functions?

It shouldn't. If it does, that's a bug. The whole point of JIT is that it shouldn't change the semantics of the language. It should be entirely invisible to the application developer.

What about C extensions? That's a harder question.

Some of the answer is: DGD already includes C extensions, but they're built-in. Network I/O is done by the driver and it fully and properly supports atomic functions. But file I/O is also done by the driver - and it supports file reads in atomic functions, but file writes are forbidden.

So which is "correct?" Both.

A well-written C extension should either notice atomic functions and handle them like network I/O does, or it should notice them and fail like file writes do.

So for instance, an extension should never write a file inside an atomic function, then leave it there when the atomic function fails. It should never write to a global variable or other type of state, then leave that write intact when the function fails.

It's possible for you to write a buggy C extension that doesn't properly handle atomic functions. C extensions are very powerful and they can mess up DGD in all sorts of ways. But you should assume that a C extension that does those things is wrong. Atomic functions should be atomic - they should fully and correctly succeed or they should fail. If a given extension can't be reverted, it should fail when you try. If you're writing an extension and you're not sure how to support atomic functions, you should check for them and fail.

What if your C extension has a real-world side effect? Is it just incompatible with atomic? Maybe. If possible, though, you could consider delaying that effect until you leave the outermost atomic function. Make a list of changes to be made later, and then only "really" do them once you've finished all atomic functions, or after the timeslice is over. This is more complicated, but it also doesn't break when developers use atomic functions. If it sounds too complicated, just have your extension fail inside any atomic function. You'd be doing the wrong thing anyway, but now you know that.
