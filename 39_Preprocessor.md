# The Preprocessor

DGD's Preprocessor is a sort of miniature language on top of LPC. If you're used to C's preprocessor it will feel straightforward and natural. If you're not, it can take some explaining.

A lot of the preprocessor's operations are about simple text cutting and pasting. It may drop the contents of a file into a particular spot, or snip out a section you don't want, or replace one bit of text with another one.

## include

We covered #include earlier - it simply substitutes a whole file worth of text in, as though you'd copy-pasted the file into that spot.

The locations DGD will check for files to include are defined in the Configuration File.

LPC has two types of includes. One uses angle brackets, while the other uses double-quotes. When including, DGD checks the normal set of include directories to find the file it was told to look for. The only difference with double-quotes is that DGD will first check the absolute path given and ***then*** check in the include directories.

Why do you care about double-quotes versus angle brackets? Usually, they're mostly used idiomatically - angle-bracket include is often for things like the Cloud Server's own files, indicating they are "system" files, while double-quote includes are mostly used for headers from your own application.

## if, else, elif, endif

These work a lot like the LPC control statements if, else and end. In the preprocessor, each leading statement has to be alone on a line, so you can't do "#else #if" -- instead, you need to use "#elif". I'm not sure why it's "#endif" rather than "#end", except that that's how the C preprocessor does it.

In a true block (one inside an #if 1, for instance), DGD will treat the code as existing and compile it normally. In a block that isn't taken (e.g. #if 0), the code effectively doesn't exist. It can contain syntax errors or other problems and they won't be seen or compiled.

Preprocessor statements like these are often used for turning specific language features on and off. For instance, DGD can be compiled with double-slash comment support or not. You can't use a normal LPC "if" statement around those if DGD doesn't have them &mdash; they'd give syntax errors. But a preprocessor #if can avoid them completely.

(That's a lot of effort to go to for conditional double-slash comments. So that would be silly. But you get the idea.)

## ifdef, ifndef, undef

The #ifdef directive is the same as "#if defined()". It's true if the thing in question is already defined and false if not. #ifndef is the opposite ("if not defined") - it's true if the identifier is ***not*** defined and false if it is.

The #undef directive is to undefine something that was defined with #define.

## error

If one of these is "really" hit (not in an #else that isn't taken or an #if 0 or something), compilation will stop and DGD will report the error.

## line

This sets DGD's idea of the file and the line number for when it reports errors. The reason you might care is for code generators and similar trickery. Often it's better to report to the programmer where you "really" saw the problem -- the pre-transformation location -- rather than the "real" location of the error in generated code.

You won't use this for normal coding.

## define

Define is a simple text-substitution... Or a not-so-simple function-like construct, sometimes called a macro or preprocessor macro.

A define with no parentheses afterwards is the simple kind that you use for constants. If you define a version with parentheses afterwards and arguments, it becomes a more interesting substitution:

```
#define ADD_TWO(a, b) (a + b)
#define MULT_TWO(a, b) (a * b)
#define MY_MATH MULT_TWO(7 + 4)
```

As with all text-substitution, be aware that you may get unexpected results because it's ***just*** putting in the exact text, not assigning to intermediate variables. If you use the same text twice, it will happen twice:

```
#define DOUBLE(a) (a + a)

void bad_idea()
{
    DOUBLE(call_an_expensive_function());  /* This will call the expensive function twice */
}
```

## pragma

This is for future expansion and is unused. You can look it up in C if you'd like to see the logic behind it, and how some C compilers use it. In DGD, it does nothing.
