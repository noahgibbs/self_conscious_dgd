# Atomic Functions

You can add the "atomic" modifier in front of a function's name. You can then call it as normal.

When you do, what changes?

## Fully Complete or Never Happened

If the function finishes without an error, mostly it's the same as a non-atomic function. If you wrote to global variables, for instance, they're changed as you would expect.

But if the function had an uncaught error, your changes are reverted. Writes to variables never happened. Network writes never happened.

## Limitations and Differences

There are a few ways that even an error-free atomic function is different.

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

But in a practical sense you can put anything you want into the string DGD passes to "error." That lets you pass other data (e.g. stack trace, internal state) encoded into that string, and your error manager can decode it on the other side.

It's possible to overflow the string length and not be able to pass everything you want that way, so be careful. But it ***is*** basically possible to pass information that way, and it's the only way to get information out from a failed atomic call.
